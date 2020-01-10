---
title: 私人 Azure Kubernetes Service 叢集
description: 瞭解如何建立私用 Azure Kubernetes Service （AKS）叢集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 6152becb8debd0700ddab6190284514c6d6cf69d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830049"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>公開預覽-私人 Azure Kubernetes Service 叢集

在私人叢集中，控制平面/API 伺服器將會在[RFC1918](https://tools.ietf.org/html/rfc1918)中定義內部 IP 位址。  藉由使用私人叢集，您可以確保您的 API 伺服器與節點集區之間的網路流量只會保留在私人網路上。

控制平面/API 伺服器（位於 AKS 管理的 Azure 訂用帳戶中）與客戶叢集/節點集區（在客戶訂用帳戶中）之間的通訊，可以透過 API 伺服器 VNET 中的[私用連結服務][private-link-service]，以及在 customer AKS 叢集的子網中公開的私用端點，彼此通訊。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

* 您需要 Azure CLI version 2.0.77 或更新版本，以及 aks-preview 0.4.18 擴充功能

## <a name="current-supported-regions"></a>目前支援的區域
* 美國西部
* 美國西部 2
* 美國東部 2
* 加拿大中部
* 北歐
* 西歐
* 澳大利亞東部

## <a name="install-latest-aks-cli-preview-extension"></a>安裝最新的 AKS CLI preview 擴充功能

若要使用私人叢集，您需要*aks-preview* CLI 擴充功能版本0.4.18 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> 當您在訂用帳戶上註冊功能時，目前無法取消註冊該功能。 啟用一些預覽功能之後，預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [*已註冊*]。 您可以使用[az feature list][az-feature-list]命令來檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

當狀態為 [已註冊] 時，請使用[az provider register][az-provider-register]命令重新整理*microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>建立私用 AKS 叢集

#### <a name="default-basic-networking"></a>預設基本網路 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
位置--啟用-私人-叢集是私人叢集的強制旗標 

#### <a name="advanced-networking"></a>Advanced 網路  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
位置--啟用-私人-叢集是私人叢集的強制旗標 

## <a name="steps-to-connect-to-the-private-cluster"></a>連接到私人叢集的步驟
API 伺服器端點沒有公用 IP 位址。 因此，使用者將需要在虛擬網路中建立 Azure 虛擬機器，並連接到 API 伺服器。 中的步驟

* 取得認證以連接到叢集

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* 在與 AKS 叢集相同的 VNET 中建立 VM，或在不同的 VNET 中建立 VM，並對此 VNET 和 AKS 叢集 VNET 進行對等互連
* 如果您在不同的 VNET 中建立 VM，您必須設定此 VNET 與私人 DNS 區域之間的連結
    * 移至入口網站中的 MC_ * 資源群組 
    * 按一下私人 DNS 區域 
    * 在左窗格中選取 [虛擬網路] 連結
    * 建立新連結以將 VM 的 VNET 新增至私人 DNS 區域 *（需要幾分鐘的時間，DNS 區域連結才會變成可用）*
    * 回到入口網站中的 MC_ * 資源群組
    * 在右窗格中選取 [虛擬網路]。 虛擬網路名稱的格式會是 aks-vnet-*。
    * 在左窗格中選取 [對等互連]
    * 按一下 [新增] 並新增 VM 的虛擬網路，並建立對等互連。
    * 移至您擁有 VM 的 Vnet，然後按一下 [對等互連] 並選取 [AKS] 虛擬網路，並建立對等互連。 如果 AKS 虛擬網路上的位址範圍和 VM 的虛擬網路衝突，則對等互連將會失敗。 如需虛擬網路對等互連的詳細資訊，請參閱這[份檔][virtual-network-peering]。
* 透過 SSH 連線到 VM
* 安裝 Kubectl 工具並執行 Kubectl 命令

## <a name="dependencies"></a>相依性  
* 僅限標準 LB-不支援基本負載平衡器  

## <a name="limitations"></a>限制 
* 相同的[Azure 私人連結服務限制][private-link-service]適用于私人叢集、Azure 私人端點和虛擬網路服務端點目前在相同的 VNET 中不受支援
* 不支援私用叢集中的虛擬節點來微調私人 Azure VNET 中的私用 ACI 實例
* 不支援使用私人叢集的現成 Azure DevOps 整合
* 如果客戶需要啟用 ACR 以使用私用 AKS，則必須使用代理程式叢集 VNET 來對等互連 ACR 的 VNET
* 目前不支援 Azure Dev Spaces
* 不支援將現有的 AKS 叢集轉換成私人叢集  
* 刪除或修改客戶子網中的私用端點，會導致叢集停止運作 
* 目前不支援容器即時資料的 Azure 監視器
* 目前不支援攜帶您自己的 DNS


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

