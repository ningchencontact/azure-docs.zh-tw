---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 如何建立 Azure Active Directory 啟用 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: db166c82e39e9184528fde67ff868229cf9b1d57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061118"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>整合 Azure Active Directory 與 Azure Kubernetes Service

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (Azure AD)，來進行使用者驗證。 在此組態中，您可以登入 AKS 叢集中使用您的 Azure AD 驗證權杖。

叢集系統管理員可以設定 Kubernetes 角色型存取控制 (RBAC) 根據使用者的身分識別或目錄群組的成員資格。

本文說明如何：

- 部署 AKS 和 Azure AD 的必要條件。
- 部署 Azure AD 啟用叢集。
- 使用 Azure 入口網站，在 AKS 叢集中建立基本的 RBAC 角色。

您也可以使用來完成這些步驟[Azure CLI][azure-ad-cli]。

> [!NOTE]
> 當您建立新的 RBAC 啟用叢集時，才可以啟用 azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。

## <a name="authentication-details"></a>驗證詳細資料

Azure AD 驗證會提供給具備 OpenID Connect 的 AKS 叢集。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。

如需 OpenID Connect 的詳細資訊，請參閱[授權存取 web 應用程式使用 OpenID Connect 和 Azure AD][open-id-connect]。

在 Kubernetes 叢集中，webhook 權杖驗證來驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。

如需有關 webhook 權杖驗證的詳細資訊，請參閱 < [Webhook 權杖驗證][ kubernetes-webhook] Kubernetes 文件中的一節。

若要提供 AKS 叢集中的 Azure AD 驗證，會建立兩個 Azure AD 應用程式。 第一個應用程式是伺服器元件，提供使用者驗證。 第二個應用程式是適用於驗證的 CLI 會提示您時，會使用用戶端元件。 此用戶端應用程式會將伺服器應用程式用於實際的驗證的用戶端所提供的認證。

> [!NOTE]
> 當您設定 Azure AD 進行 AKS 驗證時，會設定兩個 Azure AD 應用程式。 Azure 租用戶系統管理員，就必須完成這些步驟，將每個應用程式的權限委派。

## <a name="create-the-server-application"></a>建立伺服器應用程式

若要取得使用者的 Azure AD 群組成員資格，會套用第一個 Azure AD 應用程式。 若要在 Azure 入口網站中建立此應用程式：

1. 選取 [Azure Active Directory]   > [應用程式註冊]   > [新註冊]  。

    a. 指定應用程式的名稱，例如*AKSAzureADServer*。

    b. 針對**支援的帳戶類型**，選取**只有此組織目錄中的帳戶**。
    
    c. 選擇**Web**重新導向 URI 的類型，然後輸入任何 URI 格式的值，例如 *https://aksazureadserver* 。

    d. 選取 **註冊**完成時。

2. 選取  **Manifest**，然後編輯**groupMembershipClaims:** 值**所有**。 當您完成更新時，選取**儲存**。

    ![將群組成員資格更新為全部](media/aad-integration/edit-manifest.png)

3. 在 Azure AD 應用程式的左窗格中，選取**憑證與祕密**。

    a. 選取  **+ 新的用戶端祕密**。

    b. 新增金鑰的描述，例如*AKS Azure AD 伺服器*。 選擇到期時間，然後選取**新增**。

    c. 請注意索引鍵的值，此時只會顯示。 當您部署 Azure AD 啟用 AKS 叢集時，這個值就會呼叫伺服器應用程式祕密。

4. 在 Azure AD 應用程式的左窗格中，選取**API 的權限**，然後選取 **+ 新增權限**。

    a. 底下**Microsoft Api**，選取**Microsoft Graph**。

    b. 選取 **委派的權限**，然後選取旁邊的核取方塊**目錄 > Directory.Read.All （讀取目錄資料）** 。

    c. 如果預設委派的權限**使用者 > User.Read (登入及讀取使用者設定檔)** 不存在，請選取它旁邊的核取方塊。

    d. 選取 **應用程式權限**，然後選取旁邊的核取方塊**目錄 > Directory.Read.All （讀取目錄資料）** 。

    ![設定 graph 權限](media/aad-integration/graph-permissions.png)

    e. 選取 **新增權限**，儲存的更新。

    f. 底下**授與同意**，選取**授與系統管理員同意**。 此按鈕不是無法使用，如果目前的帳戶不是租用戶管理員。

    當成功授與權限時，下列通知會出現在入口網站中：

   ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

5. 在 Azure AD 應用程式的左窗格中，選取**公開 API**，然後選取 **+ 新增範圍**。
    
    a. 輸入**領域名稱**，則**系統管理員同意顯示名稱**，然後**系統管理員同意描述**例如*AKSAzureADServer*。

    b. 請確定**狀態**設為**已啟用**。

    ![將伺服器應用程式公開為與其他服務搭配使用的 API](media/aad-integration/expose-api.png)

    c. 選取 **新增範圍**。

6. 傳回應用程式**概觀**頁面，並請注意**應用程式 （用戶端） 識別碼**。 當您部署 Azure AD 啟用 AKS 叢集時，這個值稱為伺服器應用程式識別碼。

    ![取得應用程式識別碼](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>建立用戶端應用程式

當您使用 Kubernetes CLI (kubectl) 登入，則會使用第二個 Azure AD 應用程式。

1. 選取 [Azure Active Directory]   > [應用程式註冊]   > [新註冊]  。

    a. 指定應用程式的名稱，例如*AKSAzureADClient*。

    b. 針對**支援的帳戶類型**，選取**只有此組織目錄中的帳戶**。

    c. 選取  **Web**重新導向 URI 的類型，並輸入任何 URI 格式化值這類 *https://aksazureadclient* 。

    d. 選取 **註冊**完成時。

2. 在 Azure AD 應用程式的左窗格中，選取**API 的權限**，然後選取 **+ 新增權限**。

    a. 選取 **我的 Api**，然後選擇 Azure AD 伺服器應用程式建立在上一個步驟中，例如*AKSAzureADServer*。

    b. 選取 **委派的權限**，然後選取您的 Azure AD 伺服器應用程式旁邊的核取方塊。

    ![設定應用程式權限](media/aad-integration/select-api.png)

    c. 選取 **新增權限**。

    d. 底下**授與同意**，選取**授與系統管理員同意**。 此按鈕無法使用，如果目前的帳戶不是租用戶管理員。當授與權限時，下列通知會顯示在入口網站中：

    ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

3. 在 Azure AD 應用程式的左窗格中，選取**驗證**。

    - 底下**預設用戶端類型**，選取**Yes**來**視為公開用戶端的用戶端**。

5. 在 Azure AD 應用程式的左窗格中，記下應用程式識別碼。 當您部署 Azure AD 啟用 AKS 叢集時，這個值稱為用戶端應用程式識別碼。

   ![取得應用程式識別碼](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>取得租用戶識別碼

接下來，取得您的 Azure 租用戶的識別碼。 當您建立 AKS 叢集時，會使用此值。

從 Azure 入口網站中，選取**Azure Active Directory** > **屬性**，並記下**目錄識別碼**。 當您建立 Azure AD 啟用 AKS 叢集時，這個值稱為租用戶識別碼。

![取得 Azure 租用戶識別碼](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>部署 AKS 叢集

使用 [az group create][az-group-create] 命令來建立 AKS 叢集的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用[az aks 建立][ az-aks-create]命令來部署 AKS 叢集。 接下來，將下列範例命令中的值。 使用您在建立伺服器應用程式識別碼、 應用程式密碼、 用戶端應用程式識別碼和租用戶識別碼的 Azure AD 應用程式時收集到的值

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

AKS 叢集需要幾分鐘的時間建立。

## <a name="create-an-rbac-binding"></a>建立 RBAC 繫結

您可以使用 Azure Active Directory 帳戶與 AKS 叢集之前，您必須建立角色繫結或叢集角色繫結。 角色定義的權限授與，並繫結將它們套用至所需的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先，使用[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令搭配`--admin`登入具有系統管理員存取叢集的引數。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下來，建立您想要授與 AKS 叢集的存取權的 Azure AD 帳戶 ClusterRoleBinding。 下列範例會在叢集中的所有命名空間提供帳戶的完整存取權：

- 如果使用者您授與 RBAC 繫結是在相同的 Azure AD 租用戶中，指派權限的使用者主體名稱 (UPN) 為基礎。 移至步驟來建立 ClusterRoleBinding YAML 資訊清單。

- 如果使用者是在不同的 Azure AD 租用戶、 查詢，並使用**objectId**屬性改為。 如有需要請使用來取得必要的使用者帳戶的 objectId [az ad 使用者 show] [ az-ad-user-show]命令。 提供必要的帳戶使用者主體名稱 (UPN):

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

建立檔案，例如*rbac-aad-user.yaml*，然後貼上下列內容。 在最後一行中，取代**userPrincipalName_or_objectId**並提供 UPN 或物件的識別碼。 選擇取決於使用者是否為相同的 Azure AD 租用戶。

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

藉由套用繫結[kubectl 套用][ kubectl-apply]命令，在下列範例所示：

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，也可以為 Azure AD 群組的所有成員建立角色繫結。 Azure AD 群組會使用指定的群組物件識別碼，如下列範例所示。

建立檔案，例如*rbac-aad-group.yaml*，然後貼上下列內容。 以 Azure AD 租用戶中的群組物件識別碼來更新使用者名稱。

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

藉由套用繫結[kubectl 套用][ kubectl-apply]命令，在下列範例所示：

```console
kubectl apply -f rbac-aad-group.yaml
```

如需有關使用 RBAC 保護 Kubernetes 叢集的詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

## <a name="access-the-cluster-with-azure-ad"></a>存取叢集與 Azure AD

使用提取的非系統管理使用者的內容[az aks get-credentials 來取得認證][ az-aks-get-credentials]命令。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

執行之後`kubectl`命令時，您將會提示您使用 Azure 進行驗證。 請遵循螢幕上指示，完成此程序，如下列範例所示：

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

此程序完成時，會快取驗證權杖。 系統只會提示登入一次當權杖到期，或重新建立 Kubernetes 組態檔。

如果您已成功登入後，您會看到授權錯誤訊息，請檢查下列準則：

```console
error: You must be logged in to the server (Unauthorized)
```


- 您可以定義適當的物件識別碼或 UPN，取決於使用者帳戶是否在相同的 Azure AD 租用戶中。
- 使用者不是 200 個以上的群組的成員。
- 定義於應用程式註冊的伺服器符合使用所設定的值的祕密`--aad-server-app-secret`。

## <a name="next-steps"></a>後續步驟

若要使用 Azure AD 使用者和群組來控制對叢集資源的存取，請參閱[控制在 AKS 中使用角色型存取控制與 Azure AD 身分識別的叢集資源的存取權][azure-ad-rbac]。

如需如何以安全的 Kubernetes 叢集的詳細資訊，請參閱[AKS 存取和身分識別選項][rbac-authorization]。

若要深入了解身分識別和資源控制，請參閱[AKS 中驗證和授權的最佳做法][operator-best-practices-identity]。

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
