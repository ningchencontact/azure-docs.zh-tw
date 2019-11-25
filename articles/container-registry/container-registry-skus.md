---
title: Service tiers and SKUs
description: Learn about the features and limits in the Basic, Standard, and Premium service tiers (SKUs) of Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456280"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) 具有多個服務層級 (稱為SKU)。 這些 SKU 會提供可預測的定價，以及數個可符合您在 Azure 之私用 Docker 登錄容量和使用模式的選項。

| SKU | 描述 |
| --- | ----------- |
| **基本** | 適用於正在學習 Azure Container Registry 之開發人員的成本最佳化進入點。 Basic registries have the same programmatic capabilities as Standard and Premium (such as Azure Active Directory [authentication integration](container-registry-authentication.md#individual-login-with-azure-ad), [image deletion][container-registry-delete], and [webhooks][container-registry-webhook]). 不過，內含儲存體和映像輸送量最適合較低的使用方式情節。 |
| **標準** | 「標準」登錄提供與「基本」相同的功能，並且提高內含儲存體和映像輸送量。 「標準」登錄應該能滿足大部分實際執行案例的需求。 |
| **高級** | 「進階」登錄提供最多的內含儲存體和並行作業，可啟用大量情節。 In addition to higher image throughput, Premium adds features such as [geo-replication][container-registry-geo-replication] for managing a single registry across multiple regions, [content trust](container-registry-content-trust.md) for image tag signing, [firewalls and virtual networks (preview)](container-registry-vnet.md) to restrict access to the registry. |

The Basic, Standard, and Premium SKUs all provide the same programmatic capabilities. They also all benefit from [image storage][container-registry-storage] managed entirely by Azure. 選擇較高層級的 SKU 可提供更多的效能和延展性。 由於有多個服務層級，您可以一開始先使用「基本」，再隨著登錄使用量的增加而轉換為「標準」和「進階」。

## <a name="sku-features-and-limits"></a>SKU features and limits

下表詳述「基本」、「標準」和「進階」服務層級的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>變更 SKU

您可以使用 Azure CLI 或在 Azure 入口網站中變更登錄的 SKU。 You can move freely between SKUs as long as the SKU you're switching to has the required maximum storage capacity. 

### <a name="azure-cli"></a>Azure CLI

To move between SKUs in the Azure CLI, use the [az acr update][az-acr-update] command. 例如，若要改用進階：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

在 Azure 入口網站的容器登錄 [概觀] 中，選取 [更新]，然後從 SKU 下拉式清單中選取新的 **SKU**。

![在 Azure 入口網站中更新容器登錄 SKU][update-registry-sku]

## <a name="pricing"></a>價格

如需每個 Azure Container Registry SKU 的定價資訊，請參閱 [Container Registry 定價][container-registry-pricing]。

如需資料傳輸定價的詳細資訊，請參閱[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。 

## <a name="next-steps"></a>後續步驟

**Azure Container Registry 藍圖**

請瀏覽 GitHub 上的 [ACR Roadmap][acr-roadmap] ACR 藍圖)，以尋找服務中即將推出功能的相關資訊。

**Azure Container Registry UserVoice**

送出並票選 [ACR UserVoice][container-registry-uservoice] 中的新功能建議。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
