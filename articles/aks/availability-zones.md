---
title: 使用 Azure Kubernetes Service (AKS) 中的可用性區域
description: 了解如何建立可將節點分散到可用性區域中 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840676"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>預覽-建立使用可用性區域的 Azure Kubernetes Service (AKS) 叢集

Azure Kubernetes Service (AKS) 叢集中散發資源，例如節點和邏輯區段的基礎 Azure 儲存體的運算基礎結構。 這種部署模型可確保節點會執行跨單一 Azure 資料中心內的個別更新和容錯網域。 部署使用此預設行為的 AKS 叢集提供高層級的可用性，可防止硬體故障，或計劃的維護事件發生。

若要提供較高層級的應用程式的可用性，AKS 叢集中可以分散到可用性區域。 這些區域為指定的區域內的實體上分隔資料中心。 叢集元件會分散到多個區域中，您的 AKS 叢集時能夠容許其中一個這些區域中的失敗。 您的應用程式和管理作業繼續可用，即使整個資料中心有問題。

這篇文章會示範如何建立 AKS 叢集和可用性區域間散佈節點元件。 此功能目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能包括自助、 選擇加入。 它們可供收集從我們的社群的意見及 bug。 在預覽中，這些功能不適用於實際執行環境。 在公開預覽功能底下 '盡力' 支援。 AKS 技術支援小組的協助時可使用營業時間太平洋 」 (PST) 僅限 timezone。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 2.0.66 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要建立使用可用性區域的 AKS 叢集，您需要*aks 預覽*CLI 擴充功能版本 0.4.1 或更高版本。 安裝*aks 預覽*Azure CLI 擴充功能使用[az 延伸模組加入][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>註冊訂用帳戶的功能旗標

若要建立 AKS 叢集的可用性區域，請先啟用您的訂用帳戶上的某些功能旗標。 叢集使用的虛擬機器擴展集來管理部署和 Kubernetes 節點的組態。 *標準*SKU 的 Azure load balancer 也需要提供恢復功能網路元件將流量路由傳送到您的叢集。 註冊*AvailabilityZonePreview*， *AKSAzureStandardLoadBalancer*，並*VMSSPreview*功能旗標使用[az 功能註冊][az-feature-register]命令，在下列範例所示：

> [!CAUTION]
> 當您註冊訂用帳戶上的功能時，您目前無法取消註冊該功能。 啟用某些預覽功能之後，可能會使用預設值，然後在 訂用帳戶中建立的所有 AKS 叢集。 請勿啟用生產訂用帳戶上的預覽功能。 若要測試預覽功能，並收集意見反應中使用不同的訂用帳戶。

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」  。 您可以檢查註冊狀態 using [az 功能清單][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

準備好時，重新整理的註冊*Microsoft.ContainerService*使用的資源提供者[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>限制和區域可用性

AKS 叢集目前可以建立使用可用性區域，在下列區域：

* 美國東部 2
* 北歐
* 東南亞
* 西歐
* 美國西部 2

當您建立 AKS 叢集搭配使用可用性區域時，就會套用下列限制：

* 在建立叢集時，您只能啟用可用性區域。
* 在建立叢集之後，無法更新可用性區域設定。 您也無法更新現有的、 非可用性區域叢集，以使用可用性區域。
* 一旦建立之後，您無法停用 AKS 叢集中的可用性區域。
* 選取的節點大小 (VM SKU) 必須可跨所有可用性區域。
* 需要啟用區域的可用性的叢集使用 Azure Standard Load Balancer 的分配跨越多個區域。
* 您必須使用 Kubernetes 版本 1.13.5 或更新版本，若要部署 Standard Load Balancer。

使用可用性區域的 AKS 叢集必須使用 Azure load balancer*標準*SKU。 預設值*基本*Azure 負載平衡器的 SKU 不支援跨越多個可用性區域的發佈。 如需詳細資訊和限制的標準負載平衡器，請參閱 < [Azure 負載平衡器標準 SKU 預覽限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁碟限制

磁碟區，使用 Azure 受控磁碟目前都不是區域性資源。 重新排定在不同的區域，從其原始區域中的 pod 無法重新附加其先前的磁碟。 建議您執行不需要可能會遇到區域性的永續性儲存體問題的無狀態工作負載。

如果您必須執行具狀態工作負載，請使用 taints 和 tolerations 在您的 pod 規格來告訴 Kubernetes 排程器，為您的磁碟相同的區域中建立的 pod。 或者，使用網路型儲存體，例如可以附加至的 pod 中，為其排程的區域之間的 Azure 檔案。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>叢集的可用性區域： AKS 概觀

「可用性區域」是高可用性供應項目，可保護您的應用程式和資料不受資料中心故障影響。 區域是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 地區內「可用性區域」的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域備援服務會將應用程式和資料複寫至所有「可用性區域」，以防出現單一失敗點。

如需詳細資訊，請參閱 <<c0> [ 什麼是 Azure 中可用性區域？][az-overview]。

使用可用性區域部署 AKS 叢集可以分散在單一區域內的多個區域的節點。 例如，在叢集中 *美國東部 2* 區域可以在所有三個可用性區域中的節點*美國東部 2*。 此分佈的 AKS 叢集資源會改善叢集可用性，因為它們是從特定區域的失敗中恢復。

![在可用性區域的 AKS 節點分佈](media/availability-zones/aks-availability-zones.png)

在區域中斷，節點可以重新平衡以手動方式或使用叢集中自動調整程式。 如果單一區域變成無法使用，您的應用程式繼續執行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>建立 AKS 叢集跨越多個可用性區域

當您建立叢集，使用[az aks 建立][az-aks-create]命令，`--node-zones`參數可讓您定義部署到哪一個區域的代理程式節點。 當您建立叢集，指定 AKS 控制平面元件，為您的叢集也會跨越多個區域中最高可用的設定散佈`--node-zones`參數。

如果您未定義任何預設代理程式集區的區域，當您建立 AKS 叢集時，AKS 控制平面元件，為您的叢集不會使用可用性區域。 您可以新增額外的節點集區 （目前在 AKS 中的預覽） 使用[新增 az aks nodepool][az-aks-nodepool-add]命令，並指定`--node-zones`對於這些新的代理程式節點，但控制平面元件則維持不具有可用性區域感知。 您無法變更區域感知的節點集區或 AKS 控制平面元件一旦部署。

下列範例會建立名為 AKS 叢集中*myAKSCluster*資源群組中名為*myResourceGroup*。 總共*3*節點會建立為區域中的一個代理程式*1*，下列其中一個在*2*，，然後是一個在*3*。 因為這些定義為隸屬於叢集建立程序，則 AKS 控制平面元件也會分散到最高可用的組態中的區域。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

建立 AKS 叢集需要幾分鐘的時間。

## <a name="verify-node-distribution-across-zones"></a>確認在區域之間的節點分佈

備妥叢集時，會列出擴展集以查看它們部署在哪些可用性區域中的代理程式節點。

首先，取得使用 AKS 叢集認證[az aks get-credentials 來取得認證][az-aks-get-credentials]命令：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下來，使用[kubectl 描述][kubectl-describe]命令以列出叢集中的節點。 篩選*failure-domain.beta.kubernetes.io/zone*值如下列範例所示：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下列範例輸出顯示三個節點分散，是在指定的區域和可用性區域，例如*eastus2-1*的第一個可用性區域並*eastus2 2*第二個可用性區域：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

代理程式集區中加入其他節點時，Azure 平台會自動分散的基礎 Vm 指定的可用性區域。

## <a name="next-steps"></a>後續步驟

這篇文章詳細說明如何建立使用可用性區域的 AKS 叢集。 關於高可用性叢集上的其他考量，請參閱[AKS 中的商務持續性和災害復原的最佳做法][best-practices-bc-dr]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
