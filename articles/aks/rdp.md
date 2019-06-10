---
title: Rdp 連線到 Azure Kubernetes Service (AKS) 叢集 Windows 伺服器節點
description: 了解如何使用 Azure Kubernetes Service (AKS) 叢集進行疑難排解和維護工作的 Windows 伺服器節點建立 RDP 連線。
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: twhitney
ms.openlocfilehash: 11f6869d4d5a2ee0ef2e986ee8268c7a001ea015
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688627"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>透過 RDP 連接到 Azure Kubernetes Service (AKS) 叢集 Windows 伺服器節點，進行維護或疑難排解

在您的 Azure Kubernetes Service (AKS) 叢集的生命週期，您可能需要存取 AKS Windows 伺服器節點。 此存取可能用於維護、記錄收集，或其他疑難排解作業。 您可以使用 RDP 的 AKS Windows 伺服器節點。 或者，如果您想要使用 SSH 來存取 AKS Windows 伺服器節點，而且您可以存取在叢集建立期間所使用的相同金鑰組，您可以依照中的步驟[透過 ssh 連線到 Azure Kubernetes Service (AKS) 叢集節點][ssh-steps]. 基於安全考量，AKS 節點不會公開至網際網路。

Windows Server 節點支援目前為預覽狀態，AKS 中。

這篇文章會示範如何建立 AKS 節點使用其私人 IP 位址的 RDP 連線。

## <a name="before-you-begin"></a>開始之前

本文假設您有現有的 AKS 叢集與 Windows 伺服器節點。 如果您需要 AKS 叢集，請參閱文件上[與 Windows 容器，使用 Azure CLI 建立 AKS 叢集][aks-windows-cli]。 您需要的 Windows 系統管理員使用者名稱和密碼來疑難排解在 Windows Server 節點。 您也需要 RDP 用戶端這類[Microsoft 遠端桌面][rdp-mac]。

您也需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>將虛擬機器部署到您的叢集相同的子網路

Windows 伺服器節點，您的 AKS 叢集不需要從外部存取的 IP 位址。 若要讓 RDP 連線，您可以部署具有可公開存取的 IP 位址的虛擬機器相同的子網路，為您的 Windows 伺服器節點。

下列範例會建立名為的虛擬機器*myVM*中*myResourceGroup*資源群組。

首先，取得 Windows Server 的節點集區所使用的子網路。 若要取得子網路識別碼，您需要的子網路名稱。 若要取得之子網路名稱，您需要 vnet 的名稱。 查詢您的叢集網路的清單，以取得 vnet 的名稱。 若要查詢的叢集，您需要它的名稱。 您可以取得所有這些 Azure Cloud Shell 中執行下列命令：

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

已 SUBNET_ID 之後，請在建立 VM 相同的 Azure Cloud Shell 視窗執行下列命令：

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

下列範例輸出會顯示 VM 已成功建立，並顯示虛擬機器的公用 IP 位址。

```console
13.62.204.18
```

記錄的虛擬機器的公用 IP 位址。 您將在稍後步驟中使用此位址。

## <a name="get-the-node-address"></a>取得節點位址

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli][az-aks-install-cli] 命令：
    
```azurecli-interactive
az aks install-cli
```

若要設定 `kubectl` 來連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

列出使用 Windows Server 節點的內部 IP 位址[kubectl get] [ kubectl-get]命令：

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

記錄您想要進行疑難排解在 Windows Server 節點的內部 IP 位址。 您將在稍後步驟中使用此位址。

## <a name="connect-to-the-virtual-machine-and-node"></a>連接到虛擬機器和節點

連接到您稍早使用例如 RDP 用戶端所建立的虛擬機器的公用 IP 位址[Microsoft 遠端桌面][rdp-mac]。

![連接到使用 RDP 用戶端的虛擬機器的映像](media/rdp/vm-rdp.png)

您已連接到您的虛擬機器之後，連接到*內部 IP 位址*您想要使用從您的虛擬機器內的 RDP 用戶端進行疑難排解在 Windows Server 節點。

![連接到使用 RDP 用戶端的 Windows 伺服器節點的映像](media/rdp/node-rdp.png)

您現在已連線到您的 Windows Server 節點。

![[Windows 伺服器] 節點中的 cmd 視窗的影像](media/rdp/node-session.png)

您現在可以執行任何疑難排解指令*cmd*視窗。 Windows 伺服器節點，使用 Windows Server Core，因為沒有完整的 GUI 或其他 GUI 工具透過 RDP 連線到 Windows Server 的節點時。

## <a name="remove-rdp-access"></a>移除的 RDP 存取

完成時，結束 RDP 連線到 Windows Server 的節點，然後結束虛擬機器的 RDP 工作階段。 結束這兩個 RDP 工作階段之後，刪除虛擬機器[az vm 刪除][ az-vm-delete]命令：

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>後續步驟

如果您需要其他的疑難排解資料，您可以[檢視 Kubernetes 主要節點記錄檔][ view-master-logs]或是[Azure 監視器][azure-monitor-containers]。

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
