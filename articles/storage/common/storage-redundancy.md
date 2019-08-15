---
title: Azure 儲存體中的資料備援 | Microsoft Docs
description: 系統會 複製Microsoft Azure 儲存體帳戶中的資料，以維持持久性和高可用性。 冗余選項包括本地存放裝置 (LRS)、區域冗余儲存體 (ZRS)、異地多餘儲存體 (GRS)、讀取權限異地多餘儲存體 (RA-GRS)、異地區域冗余儲存體 (切換) (預覽), 以及讀取權限異地區域-多餘儲存體 (RA-切換) (預覽)。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: a4c624cf06a0e56b30b71c80a6b4a5ad48cb31a9
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016400"
---
# <a name="azure-storage-redundancy"></a>Azure 儲存體備援

Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性。 「Azure 儲存體」會複製您的資料，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 您可以選擇在相同資料中心內、在相同地區的不同資料中心內、或跨不同區域複寫您的資料。

複寫可確保您的儲存體帳戶符合[儲存體的服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) 即使遇到失敗時也一樣。 請參閱 SLA 以取得 Azure 儲存體持續性和可用性保證的相關資訊。

Azure 儲存體會定期驗證使用迴圈冗余檢查 (CRCs) 所儲存之資料的完整性。 如果偵測到資料損毀, 則會使用多餘的資料修復。 Azure 儲存體也會計算所有網路流量的總和檢查碼, 以在儲存或抓取資料時偵測損毀的資料封包。

## <a name="choosing-a-redundancy-option"></a>選擇備援選項

建立儲存體帳戶時，您可以選取下列其中一個備援選項：

* [本地備援儲存體 (LRS)](storage-redundancy-lrs.md)
* [區域備援儲存體 (ZRS)](storage-redundancy-zrs.md)
* [異地備援儲存體 (GRS)](storage-redundancy-grs.md)
* [讀取權限異地備援儲存體 (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)
* [異地區域-多餘儲存體 (切換)](storage-redundancy-gzrs.md)
* [讀取權限異地區域-多餘儲存體 (RA-切換)](storage-redundancy-gzrs.md)

下表快速簡要說明針對所指定類型的事件 (或具有類似影響的事件)，每個複寫策略將為您提供的持久性和可用性範圍。

| 狀況                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | 切換/RA-切換                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| 資料中心內的節點無法供使用                                                                 | 是                             | 是                              | 是                                  | 是                                  |
| 整個資料中心 (區域或非區域) 變成無法供使用                                           | 否                              | yes                              | 是                                  | 是                                  |
| 全區域服務中斷                                                                                     | 否                              | 否                               | yes                                  | 是                                  |
| 在全區域服務無法供使用的情況下對資料 (位於遠端、異地複寫區域) 進行讀取存取 | 否                              | 否                               | 是 (使用 RA-GRS)                                   | 是 (使用 RA-切換)                                 |
| 設計為可在指定的一年中讓物件持久性達到 \_\_                                          | 至少 99.999999999% (11 個 9) | 至少 99.9999999999% (12 個 9) | 至少 99.99999999999999% (16 個 9) | 至少 99.99999999999999% (16 個 9) |
| 支援的儲存體帳戶類型                                                                   | GPv2、GPv1、Blob                | GPv2                             | GPv2、GPv1、Blob                     | GPv2、GPv1、Blob                     |
| 讀取要求的可用性 SLA | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | 至少 99.99% (非經常性存取層為 99.9%) |
| 寫入要求的可用性 SLA | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) | 至少 99.9% (非經常性存取層為 99%) |

系統會複寫儲存體帳戶中的所有資料, 包括區塊 blob 和附加 blob、分頁 blob、佇列、資料表和檔案。 雖然 ZRS 需要一般用途 v2 儲存體帳戶, 但會複寫所有類型的儲存體帳戶。

如需每個備援選項的定價資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。 

如需 Azure 儲存體持續性和可用性保證的相關資訊，請參閱 [Azure 儲存體 SLA](https://azure.microsoft.com/support/legal/sla/storage/)。

> [!NOTE]
> Azure 進階儲存體僅支援本機多餘的儲存體 (LRS)。

## <a name="changing-replication-strategy"></a>變更複寫策略

您可以使用 [ [Azure 入口網站](https://portal.azure.com/)]、[ [Azure Powershell](storage-powershell-guide-full.md)]、[ [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)] 或其中一個[Azure 儲存體用戶端程式庫](https://docs.microsoft.com/azure/index#pivot=sdkstools), 來變更儲存體帳戶的複寫原則。 變更儲存體帳戶的複寫類型並不會造成停機。

   > [!NOTE]
   > 目前, 您無法使用 Azure 入口網站或 Azure 儲存體用戶端程式庫, 將您的帳戶轉換成 ZRS、切換或 RA-切換。 若要將您的帳戶遷移至 ZRS, 請參閱[建立高可用性 Azure 儲存體應用程式的區域冗余儲存體 (ZRS)](storage-redundancy-zrs.md) , 以取得詳細資料。 若要遷移切換或 RA 切換, 請參閱[異地區域冗余儲存體以取得高可用性和最大持久性 (預覽)](storage-redundancy-zrs.md) , 以取得詳細資料。
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>變更我帳戶的複寫策略是否會產生任何費用？

這取決於您的轉換途徑。 從最低到最昂貴、Azure 儲存體的冗余供應專案 LRS、ZRS、GRS、RA-GRS、切換和 RA-切換排序。 例如,*從*LRS 到任何其他類型的複寫都會產生額外費用, 因為您要移至更複雜的複製層級。 遷移*至*GRS 或 RA-GRS 會產生輸出頻寬費用, 因為您的資料 (在您的主要區域中) 已複寫到您的遠端次要區域。 這項費用是初始設定時的一次性成本。 複製資料之後, 就不會有任何進一步的遷移費用。 您只需支付複寫現有資料的任何新或更新的費用。 如需有關頻寬費用的詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)找到。

如果您將儲存體帳戶從 GRS 遷移至 LRS, 則不會產生額外費用, 但會從次要位置刪除您的複寫資料。

如果您將儲存體帳戶從 GRS 到 GRS 或 LRS 遷移, 則該帳戶會以 RA-GRS 計費, 超過其轉換日期的30天。

## <a name="see-also"></a>另請參閱

- [本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](storage-redundancy-lrs.md)
- [區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式](storage-redundancy-zrs.md)
- [異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫](storage-redundancy-grs.md)
- [異地區域-多餘儲存體 (切換), 以取得高可用性和最大持久性 (預覽)](storage-redundancy-gzrs.md)
- [Azure 儲存體的延展性與效能目標](storage-scalability-targets.md)
- [使用 RA-GRS 儲存體設計高可用性應用程式](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure 儲存體的重複選項和讀取權限異地多餘儲存體](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP 文件 - Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) \(英文\)
