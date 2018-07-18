---
title: Azure Container Registry SKU
description: 比較 Azure Container Registry 所提供的不同服務層。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: a8dcc6fc60b80a19c4edebd57fdad5bb10cfdd0b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165209"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) 具有多個服務層 (稱為SKU)。 這些 SKU 會提供可預測的定價，以及數個可符合您在 Azure 之私用 Docker 登錄容量和使用模式的選項。

| SKU | 受控 | 說明 |
| --- | :-------: | ----------- |
| **基本** | yes | 適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 「基本」登錄具有與「標準」和「進階」相同的程式設計功能 (Azure Active Directory 驗證整合、映像刪除和 Webhook)；不過，它具有大小和使用方式上的條件約束。 |
| **標準** | yes | 「標準」登錄提供與「基本」相同的功能，並且提高儲存體限制和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。 |
| **高級** | yes | 「進階」登錄提供較高的條件約束限制 (例如儲存體和並行作業)，以啟用大量案例。 除了更高的映像輸送量容量之外，「進階」也會新增[異地複寫][container-registry-geo-replication]這類功能，來在多個區域上管理單一登錄，並針對每個部署維護網路關閉登錄。 |
| 傳統 | 否 | 「傳統」登錄 SKU 已啟用 Azure 中 Azure Container Registry 服務的初始版本。 Azure 在訂用帳戶中建立的儲存體帳戶支援「傳統」登錄，這會限制 ACR 提供更高層級功能的能力，例如提高輸送量和異地複寫。 其功能有限，因此我們計劃在未來取代「傳統 SKU」。 |

選擇較高層級的 SKU 可提供更多的效能和級別，不過，所有受控 SKU 都會提供相同的程式設計功能。 由於有多個服務層，您可以一開始先使用「基本」，再隨著登錄使用量的增加而轉換為「標準」和「進階」。

> [!NOTE]
> 因為計劃取代「傳統」登錄 SKU，所以建議所有新登錄都使用「基本」、「標準」或「進階」。 如需有關轉換現有「傳統」登錄的資訊，請參閱[將傳統登錄升級][container-registry-upgrade]。
>

## <a name="managed-vs-unmanaged"></a>受控和非受控的比較

基本、標準和進階 SKU 統稱為「受控」登錄，傳統登錄則為「非受控」登錄。 兩者之間的主要差異是容器映像的儲存方式。

### <a name="managed-basic-standard-premium"></a>受控 (基本、標準、進階)

受控登錄可從完全由 Azure 管理的映像儲存體中得到好處。 也就是說，儲存映像的儲存體帳戶不會顯示在您的 Azure 訂用帳戶中。 使用其中一個受控登錄 SKU 可獲得幾個好處，[Azure Container Registry 中的容器映像儲存體][container-registry-storage]中會有這方面的深入討論。 本文著重在受控登錄 SKU 和其功能。

### <a name="unmanaged-classic"></a>非受控 (傳統)

傳統登錄之所以屬於「非受控」登錄，是因為支援傳統登錄的儲存體帳戶位於「您的」Azure 訂用帳戶內。 因此，您必須負責管理容器映像儲存所在的儲存體帳戶。 在使用非受控登錄時，您無法隨著需求的變更改用不同的 SKU (除非您[升級][container-registry-upgrade]為受控登錄)，而且也無法使用受控登錄所提供的數個功能 (例如，容器映像刪除、[異地複寫][container-registry-geo-replication]和 [Webhook][container-registry-webhook])。

如需將傳統登錄升級至其中一個受控 SKU 的詳細資訊，請參閱[將傳統登錄升級][container-registry-upgrade]。

## <a name="sku-feature-matrix"></a>SKU 功能對照表

下表詳述「基本」、「標準」和「進階」服務層的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>變更 SKU

您可以使用 Azure CLI 或在 Azure 入口網站中變更登錄的 SKU。 您可以自由地改用其他受控 SKU，只要您所要改用的目標 SKU 具有所需的最大儲存容量即可。 如果您從「傳統」改為使用其中一個受控 SKU，就無法改回使用「傳統」，因為這個轉換是單向的。

### <a name="azure-cli"></a>Azure CLI

若要在 Azure CLI 中改用其他 SKU，請使用 [az acr update][az-acr-update] 命令。 例如，若要改用進階：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站的容器登錄 [概觀] 中，選取 [更新]，然後從 SKU 下拉式清單中選取新的 **SKU**。

![在 Azure 入口網站中更新容器登錄 SKU][update-registry-sku]

如果您有傳統登錄，就無法在 Azure 入口網站中選取受控 SKU。 相反地，您必須先[升級][container-registry-upgrade]為受控登錄 (請參閱[從傳統變更](#changing-from-classic))。

## <a name="changing-from-classic"></a>從傳統變更

在將非受控傳統登錄移轉至基本、標準或進階的受控 SKU 其中之一時，另有需要納入考量的事項。 如果傳統登錄包含大量映像，且大小達好幾 GB，移轉程序會花點時間。 此外，`docker push` 作業會停用，直到移轉完成為止。

如需將傳統登錄升級至其中一個受控 SKU 的詳細資訊，請參閱[將傳統容器登錄升級][container-registry-upgrade]。

## <a name="pricing"></a>價格

如需每個 Azure Container Registry SKU 的定價資訊，請參閱[容器登錄定價][container-registry-pricing]。

## <a name="next-steps"></a>後續步驟

**Azure Container Registry 藍圖**

請瀏覽 GitHub 上的 [ACR 藍圖][acr-roadmap]，以尋找服務中即將推出之功能的相關資訊。

**Azure Container Registry UserVoice**

在 [ACR UserVoice][container-registry-uservoice] 中提交並票選新功能建議。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
