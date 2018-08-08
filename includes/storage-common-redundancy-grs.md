---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399952"
---
異地備援儲存體 (GRS) 設計為將您的資料複寫到與主要區域相隔數百英哩遠的次要區域，以在指定年份為物件提供至少 99.99999999999999% (16 9's) 的持久性。 如果您的儲存體帳戶已啟用 GRS，即使發生主要區域因全區中斷或嚴重損壞而無法復原的情況，您的資料仍會是永久性。

如果您選擇 GRS，您有兩個相關的選項可供選擇：

* GRS 會將您的資料複寫到次要區域中的另一個資料中心，但如果 Microsoft 起始從主要到次要區域的容錯移轉，該資料則為唯讀。 
* 讀取權限異地備援儲存體 (RA-GRS) 是以 GRS 為基礎。 RA-GRS 會將您的資料複寫到次要區域中的另一個資料中心，也會為您提供從次要區域讀取的選項。 使用 RA-GRS，不論 Microsoft 是否起始從主要到次要的容錯移轉，您都可以從次要區域讀取。 

對於已啟用 GRS 或 RA-GRS 的儲存體帳戶，會使用本地備援儲存體 (LRS) 先複寫所有資料。 更新會先認可到主要位置，並使用 LRS 進行複寫。 接著會使用 GRS，以非同步的方式將更新複寫到次要區域。 當資料寫入次要位置時，也會使用 LRS 在該位置中複寫。 

主要和次要區域會管理分散在儲存體縮放單位內不同容錯網域和升級網域之間的複本。 儲存體縮放單位是資料中心內的基本複寫單位。 這個層級的複寫是由 LRS 提供；如需詳細資訊，請參閱[本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](../articles/storage/common/storage-redundancy-lrs.md)。

當您決定要使用的複寫選項時，請記住下列幾點：

* 區域備援儲存體 (ZRS) 會利用同步複寫提供高可用性，而且在某些情況下可能是比 GRS 或 RA-GRS 更好的選擇。 如需有關 ZRS 的詳細資訊，請參閱 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)。
* 由於非同步方式複寫會涉及到延遲，在發生地區性災難的情況下，如果資料無法從主要區域復原，則很有可能會遺失尚未複寫到次要地區的變更。
* 根據 GRS，除非 Microsoft 起始容錯移轉至次要區域，否則複本無法使用。 如果 Microsoft 能夠起始容錯移轉到次要區域，您將會在完成容錯移轉之後具有該資料的讀取和寫入權限。 如需詳細資訊，請參閱[災害復原指南](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 如果應用程式需要從次要區域讀取，請啟用 RA-GRS。

## <a name="read-access-geo-redundant-storage"></a>讀取權限異地備援儲存體

讀取權限異地備援儲存體 (RA-GRS) 可為儲存體帳戶提供最大的可用性。 除了跨兩個區域異地複寫之外，RA-GRS 還提供對次要位置中資料的唯讀存取權。

啟用次要區域資料的唯讀權限時，您的資料能在儲存體帳戶的次要端點以及主要端點中使用。 次要端點與主要端點類似，但會在帳戶名稱中附加尾碼 `–secondary` 。 例如，如果 Blob 服務的主要端點是 `myaccount.blob.core.windows.net`，則次要端點會是 `myaccount-secondary.blob.core.windows.net`。 主要和次要端點會有相同的儲存體帳戶存取金鑰。

使用 RA-GRS 時要記得考量：

* 您的應用程式必須管理在使用 RA-GRS 時，與哪一個端點進行互動。
* 由於非同步方式複寫會涉及延遲，如果資料無法從主要區域復原，則很有可能會遺失尚未複寫到次要區域的變更，例如發生地區性災難時。
* 您可以查看儲存體帳戶的上次同步處理時間。 上次同步處理時間是 GMT 日期/時間值。 在上次同步處理時間之前完成的所有主要位置寫入都已成功寫入到次要位置，這表示現在已經可以從次要位置讀取這些資料。 在上次同步處理時間之後完成的主要位置寫入可能已可讀取，也可能無法讀取。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)、或 Azure 儲存體用戶端程式庫之一查詢這個值。
* 如果 Microsoft 可起始容錯移轉到次要區域，您將會在完成容錯移轉之後具有該資料的讀取和寫入權限。 如需詳細資訊，請參閱[災害復原指南](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 如需如何切換到次要區域的詳細資訊，請參閱[如果 Azure 儲存體發生中斷怎麼辦](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* RA-GRS 適用於高可用性目的。 如需延展性方面的指引，請檢閱[效能檢查清單](../articles/storage/common/storage-performance-checklist.md)。
* 如需有關 RA-GRS 高可用性的設計建議，請參閱[使用 RA-GRS 儲存體設計高可用性應用程式](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md)。

## <a name="what-is-the-rpo-and-rto-with-grs"></a>RPO 和使用 GRS 的 RTO 為何？
**復原點目標 (RPO)：** 在 GRS 和 RA-GRS 中，儲存體服務會以非同步方式將資料從主要位置異地複寫到次要位置。 在主要區域發生區域性重大災難時，Microsoft 會容錯移轉至次要區域。 發生容錯移轉時，可能會遺失尚未異地複寫的最近變更。 可能遺失資料的分鐘數稱為 RPO，這是資料還可以復原的時間點。 Azure 儲存體的 RPO 通常低於 15 分鐘，但目前並沒有關於異地複寫時間長短的 SLA。

**復原時間目標 (RTO)：** RTO 是執行容錯移轉並使儲存體帳戶恢復上線的測量標準。 執行容錯移轉的時間包括下列動作：

   * Microsoft 判斷是否可以在主要位置復原資料、或是否需要容錯移轉所需的時間。
   * 變更主要 DNS 項目以指向次要位置，來執行儲存體帳戶的容錯移轉。

   Microsoft 慎重負責保存您的資料。 如果有機會在主要區域復原資料，Microsoft 會延遲容錯移轉，著重於復原您的資料。 未來的服務版本可讓您在帳戶層級觸發容錯移轉，以便讓您可以自行控制 RTO。

## <a name="paired-regions"></a>配對的區域 

建立儲存體帳戶時，您可以為帳戶選取主要區域。 配對的次要區域會視主要區域而定，且無法變更。 如需 Azure 支援區域的最新資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對的區域](../articles/best-practices-availability-paired-regions.md)。
