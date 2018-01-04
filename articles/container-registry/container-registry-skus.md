---
title: Azure Container Registry SKU
description: "比較 Azure 容器登錄中可用的不同服務層。"
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: 15179fa3e3567f92a5eae69ba9a684addc3138dd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) 具有多個服務層 (稱為SKU)。 這些 Sku 提供可預測的定價和幾個選項來對齊到您在 Azure 中的私用 Docker 登錄容量和使用模式。

| SKU | 受控 | 說明 |
| --- | :-------: | ----------- |
| **基本** | 是 | 適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 「基本」登錄具有與「標準」和「進階」相同的程式設計功能 (Azure Active Directory 驗證整合、映像刪除和 Webhook)；不過，它具有大小和使用方式上的條件約束。 |
| **標準** | 是 | 「標準」登錄提供與「基本」相同的功能，並且提高儲存體限制和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。 |
| **高級** | 是 | 「進階」登錄提供較高的條件約束限制 (例如儲存體和並行作業)，以啟用大量案例。 除了更高的映像的輸送量容量 Premium 加入等功能[地理複寫][ container-registry-geo-replication]管理單一登錄到多個區域，維護每個網路關閉登錄部署。 |
| 傳統 | 否 | 「傳統」登錄 SKU 已啟用 Azure 中 Azure Container Registry 服務的初始版本。 Azure 在訂用帳戶中建立的儲存體帳戶支援「傳統」登錄，這會限制 ACR 提供更高層級功能的能力，例如提高輸送量和異地複寫。 其功能有限，因此我們計劃在未來取代「傳統 SKU」。 |

選擇較高層級的 SKU 提供更多的效能和擴充，不過，所有受管理的 Sku 提供相同的程式設計功能。 多個服務層時，您可以開始使用 Basic、，然後將轉換成 Standard 和 Premium，為您登錄使用量增加。

> [!NOTE]
> 因為計劃取代「傳統」登錄 SKU，所以建議所有新登錄都使用「基本」、「標準」或「進階」。 關於轉換現有的傳統登錄資訊，請參閱[升級傳統登錄][container-registry-upgrade]。
>

## <a name="managed-vs-unmanaged"></a>Managed 和 unmanaged 的比較

Basic、 Standard 和 Premium Sku 統稱為*管理*登錄，並做為傳統登錄*unmanaged*。 兩者之間的主要差異是您的容器映像的儲存方式。

### <a name="managed-basic-standard-premium"></a>管理 （Basic、 Standard、 Premium）

受管理的登錄備份 azure 受管理的 Azure 儲存體帳戶。 亦即，儲存您的映像的儲存體帳戶不在您的 Azure 訂用帳戶內。 有幾個好處獲得使用其中一種 managed 登錄 Sku、 中深入討論[升級傳統登錄][container-registry-upgrade]。 本文著重在受管理的登錄 Sku 和其功能。

### <a name="unmanaged-classic"></a>未受管理的 （傳統）

傳統的登錄 「 unmanaged"，這表示支援傳統登錄儲存體帳戶位於*您*Azure 訂用帳戶。 因此，您必須負責管理您的容器映像儲存所在的儲存體帳戶。 與 unmanaged 登錄，您不可以切換 Sku 隨著需求的變更 (以外[升級][ container-registry-upgrade] managed 登錄)，而且受管理登錄的數個功能 （例如，無法使用容器映像刪除、[地理複寫][ container-registry-geo-replication]和[webhook][container-registry-webhook])。

如需升級至其中一個受管理的 Sku 的傳統登錄的詳細資訊，請參閱[升級傳統登錄][container-registry-upgrade]。

## <a name="sku-feature-matrix"></a>SKU 的功能矩陣

下表詳述「基本」、「標準」和「進階」服務層的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>變更 SKU

您可以變更登錄的 SKU 使用 Azure CLI，或在 Azure 入口網站。 您可以自由地之間移動 managed Sku，只要的 SKU 切換到具有所需的最大儲存容量。 如果您從傳統切換到其中一個受管理的 Sku，您不能移至傳統-它是單向的轉換。

### <a name="azure-cli"></a>Azure CLI

若要在 Azure CLI Sku 之間移動，請使用[az acr 更新][ az-acr-update]命令。 例如，若要切換至 Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 入口網站

在容器登錄中**概觀**在 Azure 入口網站中，選取**更新**，然後選取 新**SKU** SKU 下拉式清單中。

![在 Azure 入口網站中更新容器登錄 SKU][update-registry-sku]

如果您有傳統登錄，您無法選取在 Azure 入口網站中的受管理的 SKU。 相反地，您必須先[升級][ container-registry-upgrade]到受管理的登錄 (請參閱[從傳統變更](#changing-from-classic))。

## <a name="changing-from-classic"></a>從傳統變更

還有其他考量，若要移轉到其中一個受管理的 Basic、 Standard 或 Premium Sku 的未受管理的傳統登錄時納入考量。 如果傳統登錄包含大量的映像，且許多 gb 的大小，移轉程序可能需要一些時間。 此外，`docker push`作業停用，直到移轉完成為止。

如需升級至其中一個受管理的 Sku 的傳統登錄的詳細資訊，請參閱[升級傳統容器登錄中][container-registry-upgrade]。

## <a name="pricing"></a>價格

如需定價資訊，在每個 Azure 容器登錄 Sku 上的，請參閱[容器登錄中定價][container-registry-pricing]。

## <a name="next-steps"></a>後續步驟

**Azure Container Registry 藍圖**

請瀏覽[ACR 藍圖][ acr-roadmap] GitHub 服務中尋找即將推出的功能的相關資訊上。

**Azure Container Registry UserVoice**

送出並票選中的新功能建議[ACR UserVoice][container-registry-uservoice]。

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
[container-registry-webhook]: container-registry-webhook.md
