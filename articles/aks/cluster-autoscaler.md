---
title: 使用 Azure Kubernetes Service (AKS) 中的叢集自動調整程式
description: 了解如何使用叢集自動調整程式，根據 Azure Kubernetes Service (AKS) 叢集中的應用程式需求，自動調整叢集。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 877d0a17b9ff06e9b9ac2c843c1847c9cb9726e4
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018720"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>預覽-自動調整叢集，以符合 Azure Kubernetes Service 上的應用程式需求（AKS）

為了符合 Azure Kubernetes Service (AKS) 中的應用程式需求，您可能需要調整執行工作負載的節點數目。 叢集自動調整程式元件可以監看叢集中由於資源限制而無法調度的 Pod。 當偵測到問題時，節點集區中的節點數目會增加，以符合應用程式需求。 也會定期檢查節點是否缺少執行的 Pod，然後視需要減少節點數目。 自動相應增加或相應減少 AKS 叢集中節點數目的功能，可讓您執行有效率、符合成本效益的叢集。

本文示範如何啟用和管理 AKS 叢集中的叢集自動調整程式。 叢集自動調整程式只能在 AKS 叢集的預覽中進行測試。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

本文會要求您執行 Azure CLI 版本2.0.65 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要使用叢集自動調整程式，您需要*aks-preview* CLI 擴充功能版本0.4.12 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>限制

當您建立和管理使用叢集自動調整程式的 AKS 叢集時，適用下列限制：

* 無法使用 HTTP 應用程式路由附加元件。

## <a name="about-the-cluster-autoscaler"></a>關於叢集自動調整程式

為了適應不斷變化的應用程式需求，例如工作日和晚上之間或在週末，叢集通常需要一種自動調整的方法。 AKS 叢集可以透過下列兩種方式之一進行擴充：

* **叢集自動調整程式**會監看由於資源限制而無法在節點上進行排程的 Pod。 接著，叢集會自動增加節點數目。
* **水平 Pod 自動調整程式**會在 Kubernetes 叢集中使用計量伺服器，以監視 Pod 的資源需求。 如果服務需要更多資源，Pod 數目會自動增加以符合需求。

![叢集自動調整程式和水平 Pod 自動調整程式通常會共同運作，以支援所需的應用程式需求](media/autoscaler/cluster-autoscaler.png)

然後，水平 Pod 自動調整程式和叢集自動調整程式皆可視需要減少 Pod 和節點數目。 當一段時間內有未使用的容量時，叢集自動調整程式會減少節點數目。 叢集自動調整程式所要移除的節點，其上的 Pod 會安全地排程在叢集中的其他位置。 如果 Pod 無法移動，叢集自動調整程式可能無法相應減少，如下列情況所示：

* Pod 會直接建立，而且不受控制器物件支援，例如部署或複本集。
* Pod 中斷預算 (PDB) 限制太多，而且不允許低於特定閾值的 Pod 數目。
* 如果排程在不同節點上，則 Pod 會使用節點選取器或無法接受的反親和性。

如需叢集自動調整程式如何無法相應減少的詳細資訊，請參閱[哪些類型的 pod 可能會讓叢集自動調整程式無法移除節點？][autoscaler-scaledown]

叢集自動調整程式會使用啟動參數來處理調整事件和資源閾值之間的時間間隔。 這些參數由 Azure 平台定義，而且目前未公開供您調整。 如需叢集自動調整程式所使用之參數的詳細資訊，請參閱[什麼是叢集自動調整程式參數？][autoscaler-parameters]。

叢集和水準 pod autoscalers 可以搭配使用，而且通常會同時部署在叢集中。 當兩者組合時，水平 Pod 自動調整程式著重於執行符合應用程式需求的 Pod 數目。 叢集自動調整程式著重於執行支援排程 Pod 所需的節點數目。

> [!NOTE]
> 當您使用叢集自動調整程式時，會停用手動調整。 可讓叢集自動調整程式判斷所需的節點數目。 如果您想要手動調整叢集，[請停用叢集自動調整程式](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>建立 AKS 叢集並啟用叢集自動調整程式

如果您需要建立 AKS 叢集，請使用[az AKS create][az-aks-create]命令。 若要在叢集的節點集區上啟用和設定叢集自動調整程式，請使用 *--enable-cluster-自動調整程式*參數，並指定節點 *--min-count*和 *--max 計數*。

> [!IMPORTANT]
> 叢集自動調整程式是一項 Kubernetes 元件。 雖然 AKS 叢集會將虛擬機器擴展集用於節點，但請勿手動在 Azure 入口網站中或使用 Azure CLI 啟用或編輯擴展集自動調整的設定。 請讓 Kubernetes 叢集自動調整程式管理所需的調整設定。 如需詳細資訊，請參閱[我可以修改節點資源群組中的 AKS 資源嗎？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

下列範例會建立 AKS 叢集，其中包含虛擬機器擴展集所支援的單一節點集區。 它也會在叢集的節點集區上啟用叢集自動調整程式，並設定最少*1*個和最多*3*個節點：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> 如果您在執行時`az aks create`指定 *--kubernetes-version* ，該版本必須符合或超過 [[開始之前](#before-you-begin)] 區段中所述的最低版本號碼。

建立叢集和設定叢集自動調整程式設定需要幾分鐘的時間。

## <a name="change-the-cluster-autoscaler-settings"></a>變更叢集自動調整程式設定

> [!IMPORTANT]
> 如果您的訂用帳戶上已啟用*多個代理程式*集區功能，請跳至[使用多個代理程式組件區自動調整一節](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。 啟用多個代理程式組件區的叢集需要`az aks nodepool`使用命令集來變更節點集區特定屬性`az aks`，而不是。 下列指示假設您未啟用多個節點集區。 若要檢查您是否已啟用它， `az feature  list -o table`請執行並`Microsoft.ContainerService/multiagentpoolpreview`尋找。

在上一個步驟中，若要建立 AKS 叢集或更新現有的節點集區，叢集自動調整程式的最小節點計數設定為*1*，而節點計數上限設定為*3*。 隨著應用程式需求的變化，您可能需要調整叢集自動調整程式節點計數。

若要變更節點計數，請使用[az aks update][az-aks-update]命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上述範例會將*myAKSCluster*中單一節點集區上的叢集自動調整程式更新為最少*1*個和最多*5*個節點。

> [!NOTE]
> 在預覽期間，您無法設定目前節點集區所設定的最小節點計數。 例如，如果您目前的最小計數已設為 *1*，則無法將最小計數更新為 *3*。

監視應用程式和服務的效能，並調整叢集自動調整程式節點計數，以符合所需的效能。

## <a name="disable-the-cluster-autoscaler"></a>停用叢集自動調整程式

如果您不想再使用叢集自動調整程式，您可以使用[az aks update][az-aks-update]命令來停用它，並指定 *--disable-cluster-自動調整程式*參數。 當叢集自動調整程式停用時，不會移除節點。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

您可以使用[az aks scale][az-aks-scale]命令，在停用叢集自動調整程式之後，手動調整叢集。 如果您使用水準 pod 自動調整程式，該功能會繼續在叢集自動調整程式停用的情況下執行，但如果所有節點資源都在使用中，pod 可能會無法排程。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新啟用已停用的叢集自動調整程式

如果您想要在現有叢集上重新啟用叢集自動調整程式，您可以使用[az aks update][az-aks-update]命令來重新啟用它，並指定 *--enable-cluster-自動調整程式*參數。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>使用已啟用多個節點集區的叢集自動調整程式

叢集自動調整程式可以與 [多節點集區[預覽] 功能](use-multiple-node-pools.md)一起使用。 遵循該檔，以瞭解如何啟用多個節點集區，並將其他節點集區新增至現有的叢集。 同時使用這兩個功能時，您會在叢集中的每個個別節點集區上啟用叢集自動調整程式，並且可以將唯一的自動調整規則傳遞給每個。

下列命令假設您已遵循本檔稍早的[初始指示](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)，而您想要將現有節點集區的最大計數從*3*更新為*5*。 使用[az aks nodepool update][az-aks-nodepool-update]命令來更新現有節點集區的設定。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

您可以使用[az aks nodepool update][az-aks-nodepool-update]來停用叢集自動調整程式，並`--disable-cluster-autoscaler`傳遞參數。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>後續步驟

本文示範如何自動調整 AKS 節點數目。 您也可以使用水平 Pod 自動調整程式，自動調整執行應用程式的 Pod 數目。 如需使用水準 pod 自動調整程式的步驟，請參閱[在 AKS 中調整應用程式][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
