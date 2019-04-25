---
title: 限制 Azure Kubernetes Service (AKS) 中的 kubeconfig 存取權
description: 了解如何控制叢集管理員和叢集使用者對 Kubernetes 組態檔 (kubeconfig) 的存取
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/03/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 141aacc71d129bb45dc53774af876d5b07b7fc86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466443"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>使用 Azure 角色型存取控制來定義 Azure Kubernetes Service (AKS) 中的 Kubernetes 組態檔存取權

您可以使用 `kubectl` 工具與 Kubernetes 叢集互動。 Azure CLI 可讓您使用 `kubectl` 輕鬆地存取連線到 AKS 叢集所需的認證和組態資訊。 若要限制可取得該 Kubernetes 組態 (*kubeconfig*) 資訊的人員，並限制他們之後擁有的權限，您可以使用 Azure 角色型存取控制 (RBAC)。

本文將說明如何指派 RBAC 角色，以限制誰可以取得 AKS 叢集的組態資訊。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

本文也需要您執行 Azure CLI 2.0.53 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="available-cluster-roles-permissions"></a>可用的叢集角色權限

當您使用 `kubectl` 工具與 AKS 叢集互動時，組態檔會用來定義叢集連線資訊。 此組態檔通常會儲存在 ~/.kube/config。此 kubeconfig 檔案中可定義多個叢集。 您可以使用 [kubectl config use-context][kubectl-config-use-context] 命令在叢集間進行切換。

[az aks get-credentials][az-aks-get-credentials] 命令可讓您取得 AKS 叢集的存取認證，並將其合併至 kubeconfig 檔案。 您可以使用 Azure 角色型存取控制 (RBAC) 來控制這些認證的存取權。 這些 Azure RBAC 角色可讓您定義誰可以擷取 kubeconfig 檔案，以及他們在叢集內具有的權限。

兩個內建角色如下：

* **Azure Kubernetes Service 叢集管理員角色**  
    * 允許存取 Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action API 呼叫。 此 API 呼叫會[列出叢集管理員認證][api-cluster-admin]。
    * 下載 *clusterAdmin* 角色的 kubeconfig。
* **Azure Kubernetes Service 叢集使用者角色**
    * 允許存取 Microsoft.ContainerService/managedClusters/listClusterUserCredential/action API 呼叫。 此 API 呼叫會[列出叢集使用者認證][api-cluster-user]。
    * 下載 clusterUser 角色的 kubeconfig。

## <a name="assign-role-permissions-to-a-user"></a>指派角色權限給使用者

若要將其中一個 Azure 角色指派給使用者，您需要取得 AKS 叢集的資源識別碼和使用者帳戶的識別碼。 下列範例命令會執行下列步驟：

* 對 myResourceGroup 資源群組中名為 myAKSCluster 的叢集使用 [az aks show][az-aks-show] 命令，以取得叢集資源識別碼。 如有需要，請提供您自己的叢集和資源群組名稱。
* 使用 [az account show][az-account-show] 和 [az ad user show][az-ad-user-show] 命令取得您的使用者識別碼。
* 最後，使用 [az role assignment create][az-role-assignment-create] 命令指派角色。

下列範例會指派「Azure Kubernetes Service 叢集管理員角色」：

```azurecli
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

如有需要，您可以將上述指派變更為「叢集使用者角色」。

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

透過已指派的 RBAC 角色，使用 [az aks get-credentials][az-aks-get-credentials] 命令來取得您 AKS 叢集的 kubeconfig 定義。 下列範例會取得 --admin 認證，如果使用者已獲派「叢集管理員角色」，則認證可正常運作：

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接著，您可以使用 [kubectl config view][kubectl-config-view] 命令，確認叢集的「內容」會顯示已套用管理員組態資訊：

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.chinaeast.azmk8s.io:443
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

若要移除角色指派，請使用 [az role assignment delete][az-role-assignment-delete] 命令。 指定在先前命令中取得的帳戶識別碼和叢集資源識別碼：

```azurecli
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>後續步驟

若要加強 AKS 叢集存取的安全性，請[整合 Azure Active Directory 驗證][aad-integration]。

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: https://docs.microsoft.com/rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: https://docs.microsoft.com/rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[az-account-show]: https://docs.azure.cn/zh-cn/cli/account?view=azure-cli-latest#az-account-show
[az-ad-user-show]: https://docs.azure.cn/zh-cn/cli/ad/user?view=azure-cli-latest#az-ad-user-show
[az-role-assignment-create]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create
[az-role-assignment-delete]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-delete
[aad-integration]: aad-integration.md