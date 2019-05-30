---
title: 透過 SSH 連線至 Azure Kubernetes Service (AKS) 叢集
description: 了解如何使用 Azure Kubernetes Service (AKS) 叢集節點建立 SSH 連線，以進行疑難排解和維護工作。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 57eacca75d711c5125a2856a7b6219cd2ec5306b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242040"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>使用 SSH 連線到 Azure Kubernetes Service (AKS) 叢集節點以進行維護或疑難排解

在 Azure Kubernetes Service (AKS) 叢集的生命週期中，您可能需要存取 AKS 節點。 此存取可能用於維護、記錄收集，或其他疑難排解作業。 您可以存取 AKS 節點使用 SSH，包括 Windows Server （目前在 AKS 中的預覽） 的節點。 您也可以[連接到使用遠端桌面通訊協定 (RDP) 連線的 Windows Server 節點][aks-windows-rdp]。 基於安全考量，AKS 節點不會公開至網際網路。

本文會示範如何使用私人 IP 位址以 AKS 節點建立 SSH 連線。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要 Azure CLI 2.0.64 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="add-your-public-ssh-key"></a>新增公開 SSH 金鑰

根據預設，SSH 金鑰是取得，或產生，則當您建立 AKS 叢集加入節點。 如果您需要指定不同於建立 AKS 叢集時所使用的 SSH 金鑰，請將 SSH 公開金鑰加入 Linux AKS 節點。 如有需要您可以建立 SSH 金鑰使用[macOS 或 Linux] [ ssh-nix]或是[Windows][ssh-windows]。 如果您使用 PuTTY Gen 來建立金鑰組，OpenSSH 中的金鑰組儲存格式而不是預設的 PuTTy 私密金鑰格式 （.ppk 檔案）。

> [!NOTE]
> SSH 金鑰可以目前只能加入至使用 Azure CLI 的 Linux 節點。 如果您使用 Windows Server 的節點時，使用建立 AKS 叢集時所提供的 SSH 金鑰，並跳至步驟上[如何取得 AKS 節點位址](#get-the-aks-node-address)。 或者，[連接到使用遠端桌面通訊協定 (RDP) 連線的 Windows Server 節點][aks-windows-rdp]。

根據您執行的 AKS 叢集類型，取得 AKS 節點的私人 IP 位址的步驟會有所不同：

* 對於大部分的 AKS 叢集，請依照下列步驟來[取得的 IP 位址規則的 AKS 叢集](#add-ssh-keys-to-regular-aks-clusters)。
* 如果您使用任何預覽功能中使用虛擬機器擴展集，例如多個節點的集區或 Windows Server 容器支援的 AKS[遵循的步驟進行虛擬機器擴展集為基礎的 AKS 叢集](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters)。

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>將 SSH 金鑰新增至規則的 AKS 叢集

若要將您的 SSH 金鑰新增至 Linux AKS 節點中，完成下列步驟：

1. 使用 [az aks show][az-aks-show] 取得 AKS 叢集資源的資源群組名稱。 提供您自己的核心資源群組和 AKS 叢集名稱。 叢集名稱指派給名為的變數*CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. 使用 [az vm list][az-vm-list] 命令列出 AKS 叢集資源群組中的虛擬機器。 這些虛擬機器是您的 AKS 節點：

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    下列範例輸出顯示 AKS 節點：

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. 若要將 SSH 金鑰新增至節點，請使用 [az vm user update][az-vm-user-update] 命令。 提供資源群組名稱，以及上一個步驟中取得的其中一個 AKS 節點。 根據預設，AKS 節點的使用者名稱是 *azureuser*。 提供您自己的 SSH 公開金鑰位置，例如 *~/.ssh/id_rsa.pub*，或貼上 SSH 公開金鑰的內容：

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>將 SSH 金鑰新增至虛擬機器擴展集為基礎的 AKS 叢集

若要將您的 SSH 金鑰新增至 Linux AKS 節點虛擬機器擴展集的一部分，完成下列步驟：

1. 使用 [az aks show][az-aks-show] 取得 AKS 叢集資源的資源群組名稱。 提供您自己的核心資源群組和 AKS 叢集名稱。 叢集名稱指派給名為的變數*CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. 接下來，取得虛擬機器擴展集使用 AKS 叢集[az vmss 清單][ az-vmss-list]命令。 虛擬機器擴展集名稱指派給名為的變數*SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. 若要將您的 SSH 金鑰新增至虛擬機器擴展集中的節點中，使用[az vmss 擴充功能組][ az-vmss-extension-set]命令。 從先前的命令提供的叢集資源群組和虛擬機器擴展集名稱。 根據預設，AKS 節點的使用者名稱是 *azureuser*。 視需要更新的您自己 SSH 公用金鑰位置，例如 *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. 套用到使用節點的 SSH 金鑰[az vmss update-執行個體][ az-vmss-update-instances]命令：

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>取得 AKS 節點位址

AKS 節點不會公開至網際網路。 對於 SSH 至 AKS 節點，您可以使用私人 IP 位址。 在下一個步驟中，您的協助程式 pod 中建立 AKS 叢集，可讓您 SSH 到節點的此私人 IP 位址。 根據您執行的 AKS 叢集類型，取得 AKS 節點的私人 IP 位址的步驟會有所不同：

* 對於大部分的 AKS 叢集，請依照下列步驟來[取得的 IP 位址規則的 AKS 叢集](#ssh-to-regular-aks-clusters)。
* 如果您使用任何預覽功能中使用虛擬機器擴展集，例如多個節點的集區或 Windows Server 容器支援的 AKS[遵循的步驟進行虛擬機器擴展集為基礎的 AKS 叢集](#ssh-to-virtual-machine-scale-set-based-aks-clusters)。

### <a name="ssh-to-regular-aks-clusters"></a>透過 ssh 連線到一般的 AKS 叢集

使用 [az vm list-ip-addresses][az-vm-list-ip-addresses] 命令檢視 AKS 叢集節點的私人 IP 位址。 提供您自己的 AKS 叢集資源群組名稱，此名稱會在前一個 [az-aks-show][az-aks-show] 步驟中取得：

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

下列範例輸出顯示 AKS 節點的私人 IP 位址：

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>透過 ssh 連線到虛擬機器擴展集為基礎的 AKS 叢集

列出使用節點的內部 IP 位址[kubectl get 命令][kubectl-get]:

```console
kubectl get nodes -o wide
```

下列範例輸出會顯示所有節點的內部 IP 位址在叢集中，包括 Windows Server 節點。

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

記錄您想要進行疑難排解之節點的內部 IP 位址。 您將在稍後步驟中使用此位址。

## <a name="create-the-ssh-connection"></a>建立 SSH 連線

若要建立到 AKS 節點的 SSH 連線，您可以在 AKS 叢集中執行協助程式 Pod。 此協助程式 Pod 為您提供到叢集的 SSH 存取權，以及額外的 SSH 節點存取權。 若要建立和使用此協助程式 Pod，請完成下列步驟：

1. 執行 `debian` 容器映像，並將終端機工作階段與它連結。 您可以使用此容器搭配 AKS 叢集中的任何節點來建立 SSH 工作階段：

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > 如果您使用 Windows Server 節點 （目前在 AKS 中的預覽） 時，新增節點選取器至命令，以排程在 Linux 節點上的 Debian 容器，如下所示：
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. 基底 Debian 映像不包含 SSH 元件。 終端機工作階段連線到容器後，請使用 `apt-get` 安裝 SSH 用戶端，如下所示：

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 在未連線到容器的新終端機視窗中，，請使用 [kubectl get pods][kubectl-get] 命令列出 AKS 叢集上的 Pod。 在前一個步驟中建立的 Pod 名稱開頭為 *aks-ssh*，如下列範例所示：

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. 在本文的第一個步驟中，您將公用 SSH 金鑰新增至 AKS 節點。 現在，將私人 SSH 金鑰複製到 Pod 中。 這個私密金鑰會用來將 SSH 建立至 AKS 節點中。

    提供上一個步驟中所取得您自己的 *aks-ssh* Pod 名稱。 如有需要，將 *~/.ssh/id_rsa* 變更到私人 SSH 金鑰的位置：

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. 回到容器的終端機工作階段，更新複製的 `id_rsa` 私人 SSH 金鑰使用權限，讓它是使用者唯讀：

    ```console
    chmod 0600 id_rsa
    ```

1. 現在，建立到 AKS 節點的 SSH 連線。 同樣地，AKS 節點的預設使用者名稱是 *azureuser*。 在 SSH 金鑰首次受到信任時，接受提示以繼續進行連線。 接著會提供您 AKS 節點的 bash 提示：

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>移除 SSH 存取

完成時，`exit` SSH 工作階段，然後 `exit` 互動式容器工作階段。 這個容器工作階段關閉時，會刪除用來從 AKS 叢集存取 SSH 的 Pod。

## <a name="next-steps"></a>後續步驟

如需其他疑難排解資料，您可以[檢視 kubelet 記錄][view-kubelet-logs]或[檢視 Kubernetes 主要節點記錄][view-master-logs]。

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
