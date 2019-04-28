---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 了解如何使用 Azure CLI 建立和啟用 Azure Active Directory 的 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: c403d809aa1fc6cdbb0555eef414f79b02ab5a8a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764115"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>整合 Azure Active Directory 以 Azure Kubernetes 服務使用 Azure CLI

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此組態中，您可以登入使用 Azure AD 驗證權杖的 AKS 叢集。 叢集操作員也可以設定 Kubernetes 角色型存取控制 (RBAC) 根據使用者的身分識別或目錄群組的成員資格。

這篇文章會示範如何建立所需 Azure AD 的元件，然後部署 Azure AD 啟用叢集，並在 AKS 叢集中建立基本的 RBAC 角色。 您也可以[完成這些步驟中使用 Azure 入口網站][azure-ad-portal]。

如這篇文章中使用完整的範例指令碼的情況下，請參閱 < [Azure CLI 範例-與 Azure AD 的 AKS 整合][complete-script]。

套用下列限制：

- 只有建立啟用 RBAC 功能的新叢集時，才能啟用 Azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。
- *客體*使用者在 Azure AD 中，例如，如果您使用不同的目錄中，從同盟的登入不支援。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

為求一致，並協助執行本文中的命令，建立您想要的 AKS 叢集名稱的變數。 下列範例會使用名稱*myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 驗證概觀

透過 OpenID Connect 對 AKS 叢集提供 Azure AD 驗證。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。 如需 OpenID Connect 的詳細資訊，請參閱 [OpenID Connect 文件][open-id-connect]。

從 Kubernetes 叢集內部，Webhook 權杖驗證用來確認驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。 如需 Webhook 權杖驗證的詳細資訊，請參閱 [Webhook 驗證文件][kubernetes-webhook]。

> [!NOTE]
> 設定 Azure AD 進行 AKS 驗證時，會設定兩個 Azure AD 應用程式。 這項作業必須由 Azure 租用戶系統管理員完成。

## <a name="create-azure-ad-server-component"></a>建立 Azure AD 伺服器元件

為了與 AKS 整合，您可以建立並使用 Azure AD 應用程式，做為身分識別要求的端點。 您需要第一個 Azure AD 應用程式取得使用者的 Azure AD 群組成員資格。

建立伺服器應用程式元件中使用[az ad app 建立][ az-ad-app-create]命令，則更新的群組成員資格宣告使用[az ad app update] [az-ad-app-update]命令。 下列範例會使用*aksname*中所定義的變數[在開始之前](#before-you-begin)區段，然後建立變數

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

現在建立伺服器應用程式中使用的服務主體[az ad sp 建立][ az-ad-sp-create]命令。 此服務主體來驗證本身內的 Azure 平台。 然後，以取得服務主體祕密 using [az ad sp 認證重設][ az-ad-sp-credential-reset]命令，並將指派給名為的變數*serverApplicationSecret*其中一種用於下列步驟：

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 需要權限來執行下列動作：

* 讀取目錄資料
* 登入及讀取使用者設定檔
* 讀取目錄資料

指派這些權限[az ad 應用程式權限新增][ az-ad-app-permission-add]命令：

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

最後，授與伺服器應用程式使用上一個步驟指派的權限[az ad 應用程式權限授與][ az-ad-app-permission-grant]命令。 步驟失敗時，如果目前的帳戶不是租用戶管理員。您也需要新增 Azure AD 應用程式要求資訊可能需要使用系統管理員同意的權限[az ad 應用程式權限系統管理員同意][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>建立 Azure AD 用戶端元件

第二個 Azure AD 應用程式可在使用者記錄在 AKS 叢集中使用 Kubernetes CLI (`kubectl`)。 此用戶端應用程式會從使用者的驗證要求，並驗證其認證和權限。 建立用戶端元件使用的 Azure AD 應用程式[az ad app 建立][ az-ad-app-create]命令：

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

建立用戶端應用程式使用的服務主體[az ad sp 建立][ az-ad-sp-create]命令：

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

取得伺服器應用程式的 oAuth2 識別碼，允許使用的兩個應用程式元件之間的驗證流程[az ad app show] [ az-ad-app-show]命令。 下一個步驟中使用這個 oAuth2 ID。

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

新增用戶端應用程式的權限和伺服器應用程式元件，以使用 oAuth2 通訊流程使用[az ad 應用程式權限新增][ az-ad-app-permission-add]命令。 然後，授與權限與伺服器應用程式中使用的通訊用戶端應用程式[az ad 應用程式權限授與][ az-ad-app-permission-grant]命令：

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>部署叢集

建立兩個 Azure AD 應用程式，現在建立 AKS 叢集本身。 首先，使用下列方法建立資源群組[az 群組建立][ az-group-create]命令。 下列範例會建立資源群組中的*EastUS*區域：

建立叢集的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

取得您的 Azure 訂用帳戶使用的租用戶識別碼[az 帳戶 show] [ az-account-show]命令。 接著，建立 AKS 叢集使用[az aks 建立][ az-aks-create]命令。 建立 AKS 叢集的命令提供伺服器和用戶端應用程式識別碼、 伺服器應用程式服務主體的密碼和您的租用戶識別碼：

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

最後，取得叢集使用的系統管理員認證[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令。 您可以在其中一個下列的步驟，取得一般*使用者*叢集以查看 Azure AD 驗證的認證運作中的流程。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>建立 RBAC 繫結

必須先建立角色繫結或叢集角色繫結，Azure Active Directory 帳戶才能搭配 AKS 叢集使用。 「角色」會定義要授與的權限，而「繫結」會將角色套用至需要的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

取得目前使用者登入使用的使用者主體名稱 (UPN) [az ad 簽署-使用者 show] [ az-ad-signed-in-user-show]命令。 下一個步驟中的 Azure AD 整合被啟用此使用者帳戶。

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> 如果您授與 RBAC 繫結的使用者位於相同的 Azure AD 租用戶，指派為基礎的權限*userPrincipalName*。 如果使用者是在不同的 Azure AD 租用戶、 查詢，並使用*objectId*屬性改為。

建立名為 YAML 資訊清單`basic-azure-ad-binding.yaml`並貼上下列內容。 在最後一行中，取代*userPrincipalName_or_objectId*前一個命令的 UPN 或物件識別碼輸出：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

建立使用 ClusterRoleBinding [kubectl 套用][ kubectl-apply]命令並指定您的 YAML 資訊清單的檔案名稱：

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>透過 Azure AD 存取叢集

現在我們要測試的 AKS 叢集的 Azure AD 驗證的整合。 設定`kubectl`設定內容使用一般使用者的認證。 此內容會傳遞回透過 Azure AD 的所有驗證要求。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

現在，使用[kubectl get pods] [ kubectl-get]命令來檢視所有的命名空間之間的 pod:

```console
kubectl get pods --all-namespaces
```

使用網頁瀏覽器的 Azure AD 認證進行驗證的提示字元中，您就會收到是符號。 您已成功驗證之後，`kubectl`命令會顯示 pod 在 AKS 叢集中，如下列範例輸出所示：

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

收到驗證權杖`kubectl`會快取。 您只被 reprompted 時的權杖已過期或 Kubernetes 組態檔重新建立登入。

如果您已成功使用登入後的網頁瀏覽器，如下列範例輸出所示，您會看到授權錯誤訊息，請檢查下列可能的問題：

```console
error: You must be logged in to the server (Unauthorized)
```

* 使用者因為無法在您登*客體*（這通常是如果您使用不同的目錄的同盟登入） 的 Azure AD 執行個體中。
* 使用者不是 200 個以上的群組成員。

## <a name="next-steps"></a>後續步驟

完整的指令碼包含在本文中所示的命令，請參閱 < [AKS 中的 Azure AD 整合指令碼範例儲存機制][complete-script]。

若要使用 Azure AD 使用者和群組來控制對叢集資源的存取，請參閱[控制在 AKS 中使用角色型存取控制與 Azure AD 身分識別的叢集資源的存取權][azure-ad-rbac]。

如需如何保護 Kubernetes 叢集的詳細資訊，請參閱[AKS 存取和身分識別選項)][rbac-authorization]。

如需身分識別和資源控制的最佳作法，請參閱[AKS 中驗證和授權的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
