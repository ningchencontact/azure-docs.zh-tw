---
title: Azure Container Registry SKU
description: 比較 Azure Container Registry 所提供的不同服務層級。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: f36b206ff015511dea7369617febe9220282bbe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069048"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) 具有多個服務層級 (稱為SKU)。 這些 SKU 會提供可預測的定價，以及數個可符合您在 Azure 之私用 Docker 登錄容量和使用模式的選項。

| SKU | 受控 | 描述 |
| --- | :-------: | ----------- |
| **基本** | 是 | 適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 「 基本 」 登錄具有與 Standard 和 Premium 相同的程式設計功能 (例如 Azure Active Directory[驗證整合](container-registry-authentication.md#individual-login-with-azure-ad)，[映像刪除][container-registry-delete]，並[webhook][container-registry-webhook])。 不過，內含儲存體和映像輸送量最適合較低的使用方式情節。 |
| **標準** | 是 | 「標準」登錄提供與「基本」相同的功能，並且提高內含儲存體和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。 |
| **高級** | 是 | 「進階」登錄提供最多的內含儲存體和並行作業，可啟用大量情節。 除了更高的映像輸送量，Premium 會新增功能，包括[異地複寫][ container-registry-geo-replication]管理單一登錄，跨多個區域，並針對[內容信任](container-registry-content-trust.md)映像標記簽署，並[防火牆和虛擬網路 （預覽）](container-registry-vnet.md)來限制存取登錄。 |
|  傳統 (*2019 年 4 月之後無法使用*) | 否 | 此 SKU 已啟用 Azure 中 Azure Container Registry 服務的初始版本。 Azure 在訂用帳戶中建立的儲存體帳戶支援「傳統」登錄，這會限制 ACR 提供更高層級功能的能力，例如提高輸送量和異地複寫。 |

> [!IMPORTANT]
> 经典注册表 SKU 即将**弃用**，**2019 年 4 月**之后将不可用。 我們建議所有新的登錄使用 Basic、 Standard 或 Premium。 应在 2019 年 4 月之前升级所有现有的经典注册表。 如需升級的資訊，請參閱[傳統登錄升級][container-registry-upgrade]。

基本、 標準和進階 Sku (共同稱為*受控登錄*) 全部都會提供相同的程式設計功能。 它們也所有受惠[映像儲存體][ container-registry-storage]完全由 Azure 管理。 選擇較高層級的 SKU 可提供更多的效能和延展性。 由於有多個服務層級，您可以一開始先使用「基本」，再隨著登錄使用量的增加而轉換為「標準」和「進階」。

## <a name="sku-feature-matrix"></a>SKU 功能對照表

下表詳述「基本」、「標準」和「進階」服務層級的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>變更 SKU

您可以使用 Azure CLI 或在 Azure 入口網站中變更登錄的 SKU。 您可以自由地改用其他受控 SKU，只要您所要改用的目標 SKU 具有所需的最大儲存容量即可。 當您從傳統切換至其中一個受控 Sku 時，就無法改回傳統 」 是單向轉換，請將它。

### <a name="azure-cli"></a>Azure CLI

若要在 Azure CLI 中改用其他 SKU，請使用 [az acr update][az-acr-update] 命令。 例如，若要改用進階：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站的容器登錄 [概觀]  中，選取 [更新]  ，然後從 SKU 下拉式清單中選取新的 **SKU**。

![在 Azure 入口網站中更新容器登錄 SKU][update-registry-sku]

如果您有傳統登錄，就無法在 Azure 入口網站中選取受控 SKU。 相反地，您必須先[升級][ container-registry-upgrade]為受控登錄。

## <a name="pricing"></a>價格

如需每個 Azure Container Registry SKU 的定價資訊，請參閱[容器登錄定價][container-registry-pricing]。

如需資料傳輸定價的詳細資訊，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 

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
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
