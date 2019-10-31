---
title: Azure 區塊 Blob 儲存體效能層級-Azure 儲存體
description: Azure blob 儲存體的效能層級。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063248"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Azure 區塊 Blob 儲存體效能層級

當企業部署效能敏感的雲端原生應用程式時，請務必在不同的效能層級上具有符合成本效益的資料儲存選項。

Azure 區塊 blob 儲存體提供兩個不同的效能層級：

- Premium：已針對高交易速率和單一數位一致儲存體延遲進行優化
- 標準：已針對高容量和高輸送量進行優化

下列考慮適用于不同的效能層級：

- 標準效能適用于所有[Azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。 [[選取區域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)] 中提供 Premium 效能。
- 高階效能針對具有高交易率的應用程式提供優化的定價，以協助降低這些工作負載的[總儲存成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。
- 若要取得區塊 blob 的 premium 效能，您必須使用 BlockBlobStorage 帳戶類型。
- 標準效能適用一般用途 v1、一般用途 v2 和 Blob 儲存體帳戶。
- Premium 和 standard 效能都支援[高輸送量區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)。 高輸送量區塊 blob 適用于高於 256 KiB 的 premium 效能。 高輸送量區塊 blob 適用于高於 4 MiB Put 區塊或放置 Blob 大小的標準效能。
- Premium 效能目前僅適用于本機多餘儲存體（LRS）。

## <a name="premium-performance"></a>Premium 效能

Premium 性能區塊 blob 儲存體可透過高效能硬體提供資料。 資料會儲存在固態硬碟（Ssd）上，其已針對低延遲而優化。 相較于傳統硬碟，Ssd 提供更高的輸送量。

Premium 效能區塊 blob 儲存體非常適合需要快速且一致回應時間的工作負載。 最適合執行許多小型交易的工作負載。

## <a name="standard-performance"></a>標準效能

標準效能支援不同的[存取層](storage-blob-storage-tiers.md)，以最符合成本效益的方式儲存資料。 它已針對大型資料集上的高容量和高輸送量進行優化。

## <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 儲存體生命週期管理提供豐富、以規則為基礎的原則：

- Premium-在其生命週期結束時使資料過期
- 標準-將資料轉換到最佳存取層，並在其生命週期結束時使資料過期

若要深入瞭解，請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。

儲存在 premium 區塊 blob 儲存體帳戶中的資料無法在經常性、非經常性和封存層之間移動。 不過，您可以從區塊 blob 儲存體帳戶將 blob 複製到*不同*帳戶中的經常性存取層。 使用[Put Block FROM URL](/rest/api/storageservices/put-block-from-url) API 或[AzCopy v10](../common/storage-use-azcopy-v10.md)將資料複製到不同的帳戶。 **來自 URL API 的 Put 區塊**會同步複製伺服器上的資料。 只有在所有資料從源伺服器位置移至目的地位置之後，呼叫才會完成。

## <a name="next-steps"></a>後續步驟

評估 GPv2 和 Blob 儲存體帳戶中的經常性存取、非經常性存取和封存

- [依照區域檢查經常性存取、非經常性存取和封存的可用性](https://azure.microsoft.com/regions/#services)
- [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
- [瞭解如何從封存層解除凍結 blob 資料](storage-blob-rehydration.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md)
- [依照區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
