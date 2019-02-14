---
title: 使用 Azure Kubernetes Service (AKS) 中的叢集自動調整程式
description: 了解如何使用叢集自動調整程式，根據 Azure Kubernetes Service (AKS) 叢集中的應用程式需求，自動調整叢集。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: bfdea1d5380750ec23964cd8564db9b3a9539f15
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754636"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>自動調整叢集以符合 Azure Kubernetes Service (AKS) 的應用程式需求

為了符合 Azure Kubernetes Service (AKS) 中的應用程式需求，您可能需要調整執行工作負載的節點數目。 叢集自動調整程式元件可以監看叢集中由於資源限制而無法調度的 Pod。 偵測到問題時，節點數目會增加以符合應用程式需求。 也會定期檢查節點是否缺少執行的 Pod，然後視需要減少節點數目。 自動相應增加或相應減少 AKS 叢集中節點數目的功能，可讓您執行有效率、符合成本效益的叢集。

本文示範如何啟用和管理 AKS 叢集中的叢集自動調整程式。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

本文需要您執行 Azure CLI 2.0.55 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

支援叢集自動調整程式的 AKS 叢集必須使用虛擬機器擴展集，並執行 Kubernetes 版本 *1.12.4* 或更新版本。 此擴展集支援處於預覽狀態。 若要加入並建立使用擴展集的叢集，請使用 [az extension add][az-extension-add] 命令，安裝 *aks-preview* Azure CLI 擴充功能，如以下範例所示：

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 當您安裝 *aks-preview* 擴充功能時，您建立的每個 AKS 叢集會使用擴展集預覽部署模型。 若要退出並建立一般完全受支援的叢集，請使用 `az extension remove --name aks-preview` 移除擴充功能。

## <a name="about-the-cluster-autoscaler"></a>關於叢集自動調整程式

為了適應不斷變化的應用程式需求，例如工作日和晚上之間或在週末，叢集通常需要一種自動調整的方法。 AKS 叢集可以透過下列兩種方式之一進行擴充：

* **叢集自動調整程式**會監看由於資源限制而無法在節點上進行排程的 Pod。 接著，叢集會自動增加節點數目。
* **水平 Pod 自動調整程式**會在 Kubernetes 叢集中使用計量伺服器，以監視 Pod 的資源需求。 如果服務需要更多資源，Pod 數目會自動增加以符合需求。

![叢集自動調整程式和水平 Pod 自動調整程式通常會共同運作，以支援所需的應用程式需求](media/autoscaler/cluster-autoscaler.png)

然後，水平 Pod 自動調整程式和叢集自動調整程式皆可視需要減少 Pod 和節點數目。 當一段時間內有未使用的容量時，叢集自動調整程式會減少節點數目。 叢集自動調整程式所要移除的節點，其上的 Pod 會安全地排程在叢集中的其他位置。 如果 Pod 無法移動，叢集自動調整程式可能無法相應減少，如下列情況所示：

* Pod 會直接建立，而且不受控制器物件支援，例如部署或複本集。
* Pod 中斷預算 (PDB) 限制太多，而且不允許低於特定閾值的 Pod 數目。
* 如果排程在不同節點上，則 Pod 會使用節點選取器或無法接受的反親和性。

如需更多叢集自動調整程式可能無法相應減少的相關資訊，請參閱[哪些類型的 Pod 可以防止叢集自動調整程式移除節點？][autoscaler-scaledown]

叢集自動調整程式會使用啟動參數來處理調整事件和資源閾值之間的時間間隔。 這些參數由 Azure 平台定義，而且目前未公開供您調整。 如需叢集自動調整程式使用哪些參數的相關資訊，請參閱[什麼是叢集自動調整程式參數？][autoscaler-parameters]。

兩個自動調整程式可以搭配運作，而且通常會同時部署在一個叢集中。 當兩者組合時，水平 Pod 自動調整程式著重於執行符合應用程式需求的 Pod 數目。 叢集自動調整程式著重於執行支援排程 Pod 所需的節點數目。

> [!NOTE]
> 當您使用叢集自動調整程式時，會停用手動調整。 可讓叢集自動調整程式判斷所需的節點數目。 如果您想要手動調整叢集，[請停用叢集自動調整程式](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>建立 AKS 叢集並啟用叢集自動調整程式

如果您需要建立 AKS 叢集，請使用 [az aks create][az-aks-create] 命令。 指定符合或高於上一節[開始之前](#before-you-begin)所述之最低版本號碼的 *--kubernetes-version*。 若要啟用和設定叢集自動調整程式，請使用 *--enable-cluster-autoscaler* 參數，並指定節點 *--min-count* 和 *--max-count*。

> [!IMPORTANT]
> 叢集自動調整程式是一項 Kubernetes 元件。 雖然 AKS 叢集會將虛擬機器擴展集用於節點，但請勿手動在 Azure 入口網站中或使用 Azure CLI 啟用或編輯擴展集自動調整的設定。 請讓 Kubernetes 叢集自動調整程式管理所需的調整設定。 如需詳細資訊，請參閱[是否可以修改 MC_ 資源群組中 AKS 資源？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group)

以下範例透過啟用虛擬機器擴展集和叢集自動調整程式來建立 AKS 叢集，並使用最少 *1* 個和最多 *3* 個節點：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.4 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

建立叢集和設定叢集自動調整程式設定需要幾分鐘的時間。

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>在現有的 AKS 叢集上啟用叢集自動調整程式

您可以在符合上一節[開始之前](#before-you-begin)所述需求的現有 AKS 叢集上，啟用叢集自動調整程式。 使用 [az aks update][az-aks-update] 命令，並選擇 *--enable-cluster-autoscaler*，然後指定節點 *--min-count* 和 *--max-count*。 下列範例在使用最少 *1* 個和最多 *3* 個節點的現有叢集上，啟用叢集自動調整程式：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

如果叢集中的最小節點計數大於現有的節點數目，則需要幾分鐘的時間來建立其他節點。

## <a name="change-the-cluster-autoscaler-settings"></a>變更叢集自動調整程式設定

在建立或更新現有 AKS 叢集的先前步驟中，叢集自動調整程式最小節點計數已設為 *1*，最大節點計數已設為 *3*。 隨著應用程式需求的變化，您可能需要調整叢集自動調整程式節點計數。

若要變更節點計數，請使用 [az aks update][az-aks-update] 命令，並指定最小值和最大值。 以下範例將 *--min-count* 設為 *1*，將 *--max-count* 設為 *5*：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> 在預覽期間，您無法將最小節點數目設定高於目前為叢集所設定的數目。 例如，如果您目前的最小計數已設為 *1*，則無法將最小計數更新為 *3*。

監視應用程式和服務的效能，並調整叢集自動調整程式節點計數，以符合所需的效能。

## <a name="disable-the-cluster-autoscaler"></a>停用叢集自動調整程式

如果您不想再使用叢集自動調整程式，可以使用 [az aks update][az-aks-update] 命令來停用該程式。 當叢集自動調整程式停用時，不會移除節點。

若要移除叢集自動調整程式，請指定 *--disable-cluster-autoscaler* 參數，如以下範例所示：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

您可以使用 [az aks scale][az-aks-scale] 命令，手動調整您的叢集。 如果您使用水平 Pod 自動調整程式，該功能會在停用叢集自動調整程式的情況下繼續執行，但如果所有節點資源皆在使用中，Pod 最後可能會無法排程。

## <a name="next-steps"></a>後續步驟

本文示範如何自動調整 AKS 節點數目。 您也可以使用水平 Pod 自動調整程式，自動調整執行應用程式的 Pod 數目。 如需使用水平 Pod 自動調整程式的步驟，請參閱 [在 AKS 中調整應用程式][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
