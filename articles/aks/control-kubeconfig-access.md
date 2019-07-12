---
title: 限制 Azure Kubernetes Service (AKS) 中的 kubeconfig 存取權
description: 了解如何控制叢集管理員和叢集使用者對 Kubernetes 組態檔 (kubeconfig) 的存取
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615682"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>使用 Azure 角色型存取控制來定義 Azure Kubernetes Service (AKS) 中的 Kubernetes 組態檔存取權

您可以使用 `kubectl` 工具與 Kubernetes 叢集互動。 Azure CLI 可讓您使用 `kubectl` 輕鬆地存取連線到 AKS 叢集所需的認證和組態資訊。 若要限制可取得該 Kubernetes 組態 (*kubeconfig*) 資訊的人員，並限制他們之後擁有的權限，您可以使用 Azure 角色型存取控制 (RBAC)。

本文將說明如何指派 RBAC 角色，以限制誰可以取得 AKS 叢集的組態資訊。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 AKS 快速入門[使用 Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]。

本文也會要求您執行 Azure CLI 版本 2.0.65 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="available-cluster-roles-permissions"></a>可用的叢集角色權限

當您使用 `kubectl` 工具與 AKS 叢集互動時，組態檔會用來定義叢集連線資訊。 此組態檔通常會儲存在 ~/.kube/config  。此 kubeconfig  檔案中可定義多個叢集。 您使用叢集間切換[kubectl 組態使用內容][kubectl-config-use-context]命令。

[Az aks get-credentials 來取得認證][az-aks-get-credentials]命令可讓您取得 AKS 叢集的存取認證，並將它們合併*kubeconfig*檔案。 您可以使用 Azure 角色型存取控制 (RBAC) 來控制這些認證的存取權。 這些 Azure RBAC 角色可讓您定義誰可以擷取 kubeconfig  檔案，以及他們在叢集內具有的權限。

兩個內建角色如下：

* **Azure Kubernetes Service 叢集管理員角色**  
    * 允許存取 Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action  API 呼叫。 此 API 呼叫[列出叢集系統管理員認證][api-cluster-admin]。
    * 下載 *clusterAdmin* 角色的 kubeconfig  。
* **Azure Kubernetes Service 叢集使用者角色**
    * 允許存取 Microsoft.ContainerService/managedClusters/listClusterUserCredential/action  API 呼叫。 此 API 呼叫[列出叢集使用者的認證][api-cluster-user]。
    * 下載 clusterUser  角色的 kubeconfig  。

這些 RBAC 角色可以套用至以 Azure Active Directory (AD) 使用者或群組中。

## <a name="assign-role-permissions-to-a-user-or-group"></a>將角色權限指派給使用者或群組

若要將其中一個可用的角色指派，您需要取得 AKS 叢集的資源識別碼和 Azure AD 使用者帳戶或群組的識別碼。 下列範例命令：

* 取得叢集資源識別碼使用[az aks show][az-aks-show]名為叢集命令*myAKSCluster*中*myResourceGroup*資源群組。 如有需要，請提供您自己的叢集和資源群組名稱。
* 會使用[az 帳戶 show][az-account-show] and [az ad user show][az-ad-user-show]命令，可讓您的使用者識別碼。
* 最後，會指派角色，使用[az 角色指派建立][az-role-assignment-create]命令。

下列範例會將指派*Azure Kubernetes 服務叢集系統管理員角色*至個別使用者帳戶：

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> 如果您想要將權限指派給 Azure AD 群組，更新`--assignee`參數的物件識別碼與前一個範例所示*群組*而非*使用者*。 若要取得群組的物件識別碼，請使用[az ad 群組顯示][az-ad-group-show]命令。 下列範例會取得名為 Azure AD 群組物件識別碼*appdev*: `az ad group show --group appdev --query objectId -o tsv`

如有需要，您可以將上述指派變更為「叢集使用者角色」  。

下列範例輸出顯示已成功建立角色指派：

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>取得及驗證組態資訊

已指派的 RBAC 角色，使用[az aks get-credentials 來取得認證][az-aks-get-credentials]命令來取得*kubeconfig*定義您的 AKS 叢集。 下列範例會取得 --admin  認證，如果使用者已獲派「叢集管理員角色」  ，則認證可正常運作：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

然後您可以使用[kubectl 組態檢視][kubectl-config-view]命令，確認*內容*叢集會顯示已套用的系統管理員的組態資訊：

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>移除角色權限

若要移除角色指派，請使用[az 角色指派刪除][az-role-assignment-delete]命令。 指定的帳戶識別碼 」 和 「 叢集資源識別碼，如同先前命令中取得。 如果您指派給群組，而不是使用者的角色，指定適當的群組物件識別碼，而不是帳戶的物件識別碼`--assignee`參數：

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>後續步驟

為了加強安全性，到 AKS 叢集的存取權[整合 Azure Active Directory 驗證][aad-integration]。

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
