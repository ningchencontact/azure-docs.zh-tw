---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 如何建立已啟用 Azure Active Directory 的 Azure Kubernetes Service (AKS) 叢集。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 026c0eefc0c4fe31e72ecad91a4a7b558f367487
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192114"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>整合 Azure Active Directory 與 Azure Kubernetes Service

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此組態中，您可以登入使用您的 Azure Active Directory 驗證語彙基元的 AKS 叢集。 此外，叢集系統管理員也可以設定 Kubernetes 角色型存取控制 (RBAC) 根據使用者的身分識別或目錄群組的成員資格。

這篇文章說明如何部署 AKS 和 Azure AD 的必要條件，然後如何部署 Azure AD 啟用叢集，並在 AKS 叢集中使用 Azure 入口網站中建立基本的 RBAC 角色。 您也可以[完成這些步驟中使用 Azure CLI][azure-ad-cli]。

套用下列限制：

- 只有建立啟用 RBAC 功能的新叢集時，才能啟用 Azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。
- *客體*使用者在 Azure AD 中，例如，如果您使用不同的目錄中，從同盟的登入不支援。

## <a name="authentication-details"></a>驗證詳細資料

透過 OpenID Connect 對 AKS 叢集提供 Azure AD 驗證。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。 如需 OpenID Connect 的詳細資訊，請參閱 [OpenID Connect 文件][open-id-connect]。

從 Kubernetes 叢集內部，Webhook 權杖驗證用來確認驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。 如需 Webhook 權杖驗證的詳細資訊，請參閱 [Webhook 驗證文件][kubernetes-webhook]。

若要提供 AKS 叢集中的 Azure AD 驗證，會建立兩個 Azure AD 應用程式。 第一個應用程式是伺服器元件，提供使用者驗證。 第二個應用程式是適用於驗證的 CLI 會提示您時，會使用用戶端元件。 此用戶端應用程式會將伺服器應用程式用於實際的驗證的用戶端所提供的認證。

> [!NOTE]
> 設定 Azure AD 進行 AKS 驗證時，會設定兩個 Azure AD 應用程式。 Azure 租用戶系統管理員，就必須完成這些步驟，將每個應用程式的權限委派。

## <a name="create-server-application"></a>建立伺服器應用程式

第一個 Azure AD 應用程式用來取得使用者的 Azure AD 群組成員資格。 在 Azure 入口網站中建立此應用程式。

1. 選取  **Azure Active Directory** > **應用程式註冊** > **新增註冊**。

    * 指定應用程式的名稱，例如*AKSAzureADServer*。
    * 針對**支援的帳戶類型**，選擇*只有此組織目錄中的帳戶*。
    * 選擇*Web* for**重新導向 URI**輸入，然後輸入下列任何格式的 URI 值*https://aksazureadserver*。
    * 選取 **註冊**完成。

1. 選取 [資訊清單]，並將 `groupMembershipClaims` 值編輯為 `"All"`。

    ![將群組成員資格更新為全部](media/aad-integration/edit-manifest.png)

    **儲存**完成後的更新。

1. 在 Azure AD 應用程式的左側導覽中，選取**憑證與祕密**。

    * 選擇 **+ 新的用戶端祕密**。
    * 新增金鑰的描述，例如*AKS Azure AD 伺服器*。 選擇到期時間，然後選取**新增**。
    * 記下金鑰值。 它只顯示這個初始的時間。 當您部署 Azure AD 啟用 AKS 叢集時，這個值指`Server application secret`。

1. 在 Azure AD 應用程式的左側導覽中，選取**API 的權限**，然後選擇 **+ 新增權限**。

    * 底下**Microsoft Api**，選擇*Microsoft Graph*。
    * 選擇**委派的權限**，然後旁邊加上核取**目錄 > Directory.Read.All （讀取目錄資料）**。
        * 如果預設委派的權限**使用者 > User.Read (登入及讀取使用者設定檔)** 不存在，請勾選此權限。
    * 選擇**應用程式權限**，然後旁邊加上核取**目錄 > Directory.Read.All （讀取目錄資料）**。

        ![設定 graph 權限](media/aad-integration/graph-permissions.png)

    * 選擇**新增權限**，儲存的更新。

    * 底下**授與同意**區段中，選擇**授與系統管理員同意**。 此按鈕會呈現灰色且無法使用，如果目前的帳戶不是租用戶管理員。

        成功授與權限時，入口網站會顯示下列通知：

        ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

1. 在 Azure AD 應用程式的左側導覽中，選取**公開 API**，然後選擇 **+ 新增範圍**。
    
    * 設定*領域名稱*，*系統管理員同意顯示名稱*，並*系統管理員同意描述*，例如*AKSAzureADServer*。
    * 請確定**狀態**設為*已啟用*。

        ![將伺服器應用程式公開為與其他服務搭配使用的 API](media/aad-integration/expose-api.png)

    * 選擇**新增範圍**。

1. 傳回應用程式**概觀**頁面上，並記**應用程式 （用戶端） 識別碼**。 當您部署 Azure AD 啟用 AKS 叢集時，這個值指`Server application ID`。

   ![取得應用程式識別碼](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>建立用戶端應用程式

第二個 Azure AD 應用程式可在登入 Kubernetes CLI (`kubectl`)。

1. 選取  **Azure Active Directory** > **應用程式註冊** > **新增註冊**。

    * 指定應用程式的名稱，例如*AKSAzureADClient*。
    * 針對**支援的帳戶類型**，選擇*只有此組織目錄中的帳戶*。
    * 選擇*Web* for**重新導向 URI**輸入，然後輸入下列任何格式的 URI 值*https://aksazureadclient*。
    * 選取 **註冊**完成。

1. 在 Azure AD 應用程式的左側導覽中，選取**API 的權限**，然後選擇 **+ 新增權限**。

    * 選取 **我的 Api**，然後選擇 Azure AD 伺服器應用程式建立在上一個步驟中，例如*AKSAzureADServer*。
    * 選擇**委派的權限**，再加上您的 Azure AD 伺服器應用程式旁邊的核取。

        ![設定應用程式權限](media/aad-integration/select-api.png)

    * 選取 **新增權限**。

    * 底下**授與同意**區段中，選擇**授與系統管理員同意**。 此按鈕會呈現灰色且無法使用，如果目前的帳戶不是租用戶管理員。

        成功授與權限時，入口網站會顯示下列通知：

        ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

1. 在 Azure AD 應用程式左側導覽中，記下**應用程式識別碼**。 部署已啟用 Azure AD 的 AKS 叢集時，這個值就是指 `Client application ID`。

   ![取得應用程式識別碼](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>取得租用戶識別碼

最後，取得您的 Azure 租用戶識別碼。 當您建立 AKS 叢集時，會使用此值。

從 Azure 入口網站，選取 [Azure Active Directory] > [屬性]，並記下 [目錄識別碼]。 當您建立 Azure AD 啟用 AKS 叢集時，這個值指`Tenant ID`。

![取得 Azure 租用戶識別碼](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>部署叢集

使用 [az group create][az-group-create] 命令來建立 AKS 叢集的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令來部署叢集。 下列範例命令中的值取代為建立伺服器應用程式識別碼和祕密、 用戶端應用程式識別碼和租用戶識別碼的 Azure AD 應用程式時收集到的值：

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

花幾分鐘的時間來建立 AKS 叢集。

## <a name="create-rbac-binding"></a>建立 RBAC 繫結

必須先建立角色繫結或叢集角色繫結，Azure Active Directory 帳戶才能搭配 AKS 叢集使用。 「角色」會定義要授與的權限，而「繫結」會將角色套用至需要的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先，使用[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令搭配`--admin`登入具有系統管理員存取叢集的引數。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下來，建立您想要授與 AKS 叢集的存取權的 Azure AD 帳戶 ClusterRoleBinding。 下列範例會在叢集中的所有命名空間提供帳戶的完整存取權。

- 如果使用者您授與 RBAC 繫結是在相同的 Azure AD 租用戶中，指派權限的使用者主體名稱 (UPN) 為基礎。 移至步驟來建立 ClusterRuleBinding YAML 資訊清單。

- 如果使用者是在不同的 Azure AD 租用戶、 查詢，並使用*objectId*屬性改為。 如有需要取得*objectId*的必要的使用者帳戶使用[az ad 使用者 show] [ az-ad-user-show]命令。 提供必要的帳戶使用者主體名稱 (UPN):

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

建立 *rbac-aad-user.yaml* 等的檔案，並貼上下列內容。 在最後一行中，取代*userPrincipalName_or_objectId* UPN 或物件識別碼，視使用者是否包含相同的 Azure AD 租用戶。

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

使用 [kubectl apply][kubectl-apply] 命令套用繫結，如下列範例所示：

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，也可以為 Azure AD 群組的所有成員建立角色繫結。 Azure AD 群組是使用群組物件識別碼所指定的，如下列範例所示。 建立 *rbac-aad-group.yaml* 等的檔案，並貼上下列內容。 以 Azure AD 租用戶中的群組物件識別碼來更新使用者名稱。

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

使用 [kubectl apply][kubectl-apply] 命令套用繫結，如下列範例所示：

```console
kubectl apply -f rbac-aad-group.yaml
```

如需有關使用 RBAC 保護 Kubernetes 叢集的詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

## <a name="access-cluster-with-azure-ad"></a>透過 Azure AD 存取叢集

接下來，使用 [az aks get-credentials][az-aks-get-credentials] 命令，提取非系統管理員使用者的內容。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

執行之後`kubectl`命令時，系統會提示您使用 Azure 進行驗證。 請遵循螢幕上指示，完成此程序，如下列範例所示：

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

完成時，會快取驗證權杖。 您只被 reprompted 登入的權杖已過期時，或重新建立的 Kubernetes 組態檔。

如果成功登入後看到授權錯誤訊息，請檢查：
1. 使用者登入，是 Azure AD 執行個體 （此案例通常是如果您使用已同盟的帳戶，從不同的目錄） 中的來賓。
2. 使用者不是 200 個以上的群組成員。
3. 伺服器的應用程式註冊中所定義的密碼不符合使用-aad 伺服器-應用程式密碼設定的值

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>後續步驟

若要使用 Azure AD 使用者和群組來控制對叢集資源的存取，請參閱[控制在 AKS 中使用角色型存取控制與 Azure AD 身分識別的叢集資源的存取權][azure-ad-rbac]。

如需如何保護 Kubernetes 叢集的詳細資訊，請參閱[AKS 存取和身分識別選項)][rbac-authorization]。

如需身分識別和資源控制的最佳作法，請參閱[AKS 中驗證和授權的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
