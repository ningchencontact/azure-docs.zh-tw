---
title: Azure 儲存體延展性和效能目標-儲存體帳戶
description: 深入了解延展性和效能目標，包括容量、 要求率以及輸入和輸出頻寬，Azure 儲存體帳戶。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: a61c2577f79f32f97fe2ef10a2ac67fdbbc1feda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073745"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure 儲存體延展性和效能目標儲存體帳戶

本文會詳細說明 Azure 儲存體帳戶的延展性和效能目標。 列於此處的延展性和效能目標都是高階目標，但仍可達成。 在所有情況下，您的儲存體帳戶所達到的要求率和頻寬取決於已儲存物件的大小、使用的存取模式、應用程式執行的工作負載類型。

請務必測試您的服務，以判斷效能是否達到您的要求。 如果可能，請避免流量率突增，確保流量在不同分割之間妥善分散。

當您的應用程式達到分割區可處理的工作負載限制時，Azure 儲存體會開始傳回錯誤碼 503 (伺服器忙碌) 或錯誤碼 500 (作業逾時) 回應。 如果發生 503 錯誤，請考慮將您的應用程式修改為針對重試使用指數輪詢原則。 指數輪詢讓分割的負載減少，也能減輕該分割流量的尖峰。

## <a name="storage-account-scale-limits"></a>儲存體帳戶的規模限制

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>進階效能儲存體帳戶調整限制

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>儲存體資源提供者縮放限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 儲存體擴展目標

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 檔案擴展目標

如需 Azure 檔案服務和 Azure 檔案同步的擴展目標與效能目標的詳細資訊，請參閱 [Azure 檔案服務延展性和效能目標](../files/storage-files-scale-targets.md)。

> [!IMPORTANT]
> 儲存體帳戶限制套用到所有的共用。 最多調整儲存體帳戶的最大值才可達成，如果沒有每個儲存體帳戶只能有一個共用。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>進階檔案調整目標

有三種類別的限制時應考量的進階檔案： 儲存體帳戶、 共用和檔案。

例如: 單一共用可達到 100,000 IOPS 和單一檔案可以調整最多 5,000 個 IOPS。 因此，比方說，如果您有一個共用中的三個檔案，您可以從共用取得的最大 IOPs 是 15000。

#### <a name="premium-file-share-limits"></a>進階檔案共用限制

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

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