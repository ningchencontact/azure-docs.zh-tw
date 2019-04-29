---
title: Azure 存储可伸缩性和性能目标 - 存储帐户
description: 了解 Azure 存储帐户的可伸缩性和性能目标，包括容量、请求速率以及入站和出站带宽。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: e3e0e9ae4a1939aad9ab2ae42a1b51b1b00e2462
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101458"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>存储帐户的 Azure 存储可伸缩性和性能目标

本文會詳細說明 Azure 儲存體帳戶的延展性和效能目標。 列於此處的延展性和效能目標都是高階目標，但仍可達成。 在所有情況下，您的儲存體帳戶所達到的要求率和頻寬取決於已儲存物件的大小、使用的存取模式、應用程式執行的工作負載類型。

請務必測試您的服務，以判斷效能是否達到您的要求。 如果可能，請避免流量率突增，確保流量在不同分割之間妥善分散。

當您的應用程式達到分割區可處理的工作負載限制時，Azure 儲存體會開始傳回錯誤碼 503 (伺服器忙碌) 或錯誤碼 500 (作業逾時) 回應。 如果發生 503 錯誤，請考慮將您的應用程式修改為針對重試使用指數輪詢原則。 指數輪詢讓分割的負載減少，也能減輕該分割流量的尖峰。

## <a name="storage-account-scale-limits"></a>儲存體帳戶的規模限制

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>高级性能存储帐户缩放限制

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>儲存體資源提供者縮放限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 儲存體擴展目標

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 檔案擴展目標

如需 Azure 檔案服務和 Azure 檔案同步的擴展目標與效能目標的詳細資訊，請參閱 [Azure 檔案服務延展性和效能目標](../files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>進階檔案調整目標

有三種類別的限制時應考量的進階檔案： 儲存體帳戶、 共用和檔案。

例如︰單一共用可達到 100,000 IOPS 和單一檔案可以調整最多 5,000 個 IOPS。 因此，比方說，如果您有一個共用中的三個檔案，您可以從共用取得的最大 IOPs 是 15000。

#### <a name="premium-file-share-limits"></a>進階檔案共用限制

> [!IMPORTANT]
> 儲存體帳戶限制套用到所有的共用。 最多調整儲存體帳戶的最大值才可達成，如果沒有每個儲存體帳戶只能有一個共用。

|領域  |目標  |
|---------|---------|
|佈建的最小大小                        |100 GiB      |
|最大可佈建大小                        |100 TiB      |
|最小的大小增加/減少    |1 GiB      |
|基準 IOPS    |每 GiB，最多 100,000 個 1 IOPS|
|負載平衡的 IOPS    |3 個 x IOPS 每 GiB，最多 100,000 個|
|輸出速率         |60 MiB/秒 + $0.06 * 佈建 GiB        |
|輸入速率| 40 MiB/秒 + 0.04 * 佈建 GiB |
|快照集的最大數目        |200       |

#### <a name="premium-file-limits"></a>進階檔案限制

|領域  |目標  |
|---------|---------|
|大小                  |1 TiB         |
|每個檔案的最大 IOPS     |5,000         |
|並行控制代碼    |2,000         |

### <a name="azure-file-sync-scale-targets"></a>Azure 檔案同步擴展目標

Azure 檔案同步的設計目標是無限制的使用方式，但無限制的使用方式不一定行得通。 下表指出 Microsoft 的測試界限，也指出哪些目標是固定限制：

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure 佇列儲存體擴展目標

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure 資料表儲存體擴展目標

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>請參閱

- [儲存體定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)
- [Azure 訂用帳戶和服務限制、配額與限制](../../azure-subscription-service-limits.md)
- [Azure 儲存體複寫](../storage-redundancy.md)
- [Microsoft Azure 儲存體效能與延展性檢查清單](../storage-performance-checklist.md)