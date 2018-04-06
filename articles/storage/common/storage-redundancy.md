---
title: Azure 儲存體中的資料複寫 | Microsoft Docs
description: 系統會 複製Microsoft Azure 儲存體帳戶中的資料，以維持持久性和高可用性。 複寫選項包括本地備援儲存體 (LRS)、區域備援儲存體 (ZRS)、異地備援儲存體 (GRS) 和讀取權限異地備援儲存體 (RA-GRS)。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Azure 儲存體複寫

Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性。 複寫會複製您的資料，使其在暫時性硬體故障時受到保護，讓您的應用程式保持正常運作。 

您可以選擇在相同資料中心內、在相同區域的不同資料中心內、或跨區域複寫您的資料。 如果您跨多個資料中心或跨區域複寫資料，也能在單一位置發生嚴重失敗時保護資料。

複寫可確保您的儲存體帳戶符合[儲存體的服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) 即使遇到失敗時也一樣。 請參閱 SLA 以取得 Azure 儲存體持續性和可用性保證的相關資訊。

建立儲存體帳戶時，您可以選取下列其中一個複寫選項：

* [本地備援儲存體 (LRS)](#locally-redundant-storage)
* [區域備援儲存體 (ZRS)](#zone-redundant-storage)
* [異地備援儲存體 (GRS)](#geo-redundant-storage)
* [讀取權限異地備援儲存體 (RA-GRS)](#read-access-geo-redundant-storage)

在建立儲存體帳戶時，本地備援儲存體 (LRS) 是預設選項。

下表為 LRS、ZRS、GRS、RA-GRS 之間的差異快速概觀。 本文的後續各節會更詳細說明每一種複寫。

| 複寫策略 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 可跨多個資料中心複寫資料。 |否 |yes |yes |yes |
| 可從次要位置及主要位置讀取資料。 |否 |否 |否 |yes |
| 設計為可提供指定一年中物件具有 ___ 持久性。 |至少 99.999999999% (11 個 9)|至少 99.9999999999% (12 個 9)|至少 99.99999999999999% (16 個 9)|至少 99.99999999999999% (16 個 9)|

如需不同備援選項的定價資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/) 。

> [!NOTE]
> 進階儲存體僅支援本地備援儲存體 (LRS)。 如需進階儲存體的相關資訊，請參閱 [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../../virtual-machines/windows/premium-storage.md)。
>

## <a name="locally-redundant-storage"></a>本地備援儲存體
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>區域備援儲存體
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>傳統 ZRS 帳戶

現有的 ZRS 功能是指傳統 ZRS。 傳統 ZRS 帳戶僅適用於一般用途 (general-purpose V1) 儲存體帳戶中的區塊 Blob。 

傳統 ZRS 會以非同步方式在一至兩個區域內的資料中心間複寫資料。 除非 Microsoft 開始容錯移轉至次要區域，否則可能無法使用複本。 

傳統 ZRS 帳戶無法與 LRS、GRS 或 RA-GRS 帳戶互相轉換。 傳統 ZRS 帳戶也不支援計量或記錄。   

一旦在一個區域中推出 ZRS，您便不能在該區域的入口網站中建立傳統 ZRS 帳戶，但您可以透過其他方式建立。  
未來將提供從傳統 ZRS 移轉至 ZRS 的自動移轉程序。

您可以手動將 ZRS 帳戶資料移轉至或移轉出 LRS、ZRS 傳統、GRS 或 RAGRS 帳戶。 您可以使用 AzCopy、Azure 儲存體總管、Azure PowerShell、Azure CLI 或 Azure 儲存體用戶端程式庫之一，執行這項手動移轉。

> [!NOTE]
> 傳統 ZRS 帳戶已計劃在 2021 年 3 月 31 日除役，並進行必要的移轉。 Microsoft 會在除役之前傳送更詳細的資訊給傳統 ZRS 客戶。

其他問題則在[常見問題集](#frequently-asked-questions)一節中說明。 

## <a name="geo-redundant-storage"></a>異地備援儲存體
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>讀取權限異地備援儲存體
讀取權限異地備援儲存體 (RA-GRS) 可為儲存體帳戶提供最大的可用性。 除了跨兩個區域異地複寫之外，RA-GRS 還提供對次要位置中資料的唯讀存取權。

啟用次要區域資料的唯讀權限時，您的資料能在儲存體帳戶的次要端點以及主要端點中使用。 次要端點與主要端點類似，但會在帳戶名稱中附加尾碼 `–secondary` 。 例如，如果 Blob 服務的主要端點是 `myaccount.blob.core.windows.net`，則次要端點會是 `myaccount-secondary.blob.core.windows.net`。 主要和次要端點會有相同的儲存體帳戶存取金鑰。

使用 RA-GRS 時要記得考量：

* 您的應用程式必須管理在使用 RA-GRS 時，與哪一個端點進行互動。
* 由於非同步方式複寫會涉及延遲，如果資料無法從主要區域復原，則很有可能會遺失尚未複寫到次要區域的變更，例如發生地區性災難時。
* 如果 Microsoft 可起始容錯移轉到次要區域，您將會在完成容錯移轉之後具有該資料的讀取和寫入權限。 如需詳細資訊，請參閱[災害復原指南](../storage-disaster-recovery-guidance.md)。
* RA-GRS 適用於高可用性目的。 如需延展性方面的指引，請檢閱[效能檢查清單](../storage-performance-checklist.md)。

## <a name="frequently-asked-questions"></a>常見問題集

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1.我要如何變更儲存體帳戶的異地複寫類型？

   您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure Powershell](storage-powershell-guide-full.md) 或 Azure 儲存體用戶端程式庫之一，變更儲存體帳戶的異地複寫類型。

   > [!NOTE]
   > ZRS 帳戶無法轉換成 LRS 或 GRS。 同樣地，現有 LRS 或 GRS 帳戶無法轉換為 ZRS 帳戶。
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2.變更儲存體帳戶的複寫類型，是否會造成停機時間？

   不，變更儲存體帳戶的複寫類型，不會造成停機時間。

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3.變更儲存體帳戶的複寫類型，是否會產生額外費用？

   是。 如果您將儲存體帳戶從 LRS 變更為 GRS (或 RA-GRS)，對於涉及將現有資料從主要位置複製到次要位置的輸出，會產生額外費用。 複製初始資料之後，從主要位置異地複寫到次要位置將不會產生額外的輸出費用。 如需有關頻寬費用的詳細資料，請參閱 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)找到。

   如果您要從 GRS 變更為 LRS，將沒有額外費用，但會從次要位置刪除資料。

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4.RA-GRS 可以給我什麼協助？

   GRS 儲存體可將資料從主要區域複寫到與主要區域距離數百英哩的次要區域。 有了 GRS，即使發生主要區域因全區中斷或嚴重損壞而無法復原的情況，您的資料仍會是永久性。 RA-GRS 儲存體提供 GRS 複寫，並增加了從次要位置讀取資料的功能。 如需有關高可用性的設計建議，請參閱[使用 RA-GRS 儲存體設計高可用性應用程式](../storage-designing-ha-apps-with-ragrs.md)。

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5.是否有辦法可以知道將資料從主要區域複寫到次要區域所需花費的時間？

   如果您使用 RA-GRS 儲存體，可查閱儲存體帳戶的上次同步處理時間。 上次同步處理時間是 GMT 日期/時間值。 在上次同步處理時間之前完成的所有主要位置寫入都已成功寫入到次要位置，這表示現在已經可以從次要位置讀取這些資料。 在上次同步處理時間之後完成的主要位置寫入可能已可讀取，也可能無法讀取。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)、或 Azure 儲存體用戶端程式庫之一查詢這個值。

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6.如果主要區域發生中斷，如何切換為次要區域？

   如需詳細資訊，請參閱[如果 Azure 儲存體發生中斷怎麼辦](../storage-disaster-recovery-guidance.md)。

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7.RPO 和使用 GRS 的 RTO 為何？

   **復原點目標 (RPO)：**在 GRS 和 RA-GRS 中，儲存體服務會以非同步方式將資料從主要位置異地複寫到次要位置。 在主要區域發生區域性重大災難時，Microsoft 會容錯移轉至次要區域。 發生容錯移轉時，可能會遺失尚未異地複寫的最近變更。 可能遺失資料的分鐘數稱為 RPO，這是資料還可以復原的時間點。 Azure 儲存體的 RPO 通常低於 15 分鐘，但目前並沒有關於異地複寫時間長短的 SLA。

   **復原時間目標 (RTO)：**RTO 是執行容錯移轉並使儲存體帳戶恢復上線的測量標準。 執行容錯移轉的時間包括下列動作：

   * Microsoft 判斷是否可以在主要位置復原資料、或是否需要容錯移轉所需的時間。
   * 變更主要 DNS 項目以指向次要位置，來執行儲存體帳戶的容錯移轉。

   Microsoft 慎重負責保存您的資料。 如果有機會在主要區域復原資料，Microsoft 會延遲容錯移轉，著重於復原您的資料。 未來的服務版本可讓您在帳戶層級觸發容錯移轉，以便讓您可以自行控制 RTO。

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8.ZRS 支援哪些 Azure 儲存體物件？ 
區塊 Blob、分頁 Blob (除了那些支援 VM 磁碟的 Blob)、資料表、檔案、佇列。 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9.ZRS 也包含異地複寫嗎？ 
目前 ZRS 不支援異地複寫。 如果您的案例基於災害復原目的需要跨區域複寫，請改用 GRS 或 RA-GRS 儲存體帳戶。   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10.若一個以上的 ZRS 區域關閉，會發生什麼事？ 
當第一個區域關閉時，ZRS 會繼續在區域中剩餘的兩個區域寫入資料複本。 如果第二個區域關閉，便無法再讀取和寫入，直到至少兩個區域再度可使用才會恢復。 

## <a name="next-steps"></a>後續步驟
* [使用 RA-GRS 儲存體設計高可用性應用程式](../storage-designing-ha-apps-with-ragrs.md)
* [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)
* [關於 Azure 儲存體帳戶](../storage-create-storage-account.md)
* [Azure 儲存體的延展性與效能目標](storage-scalability-targets.md)
* [Microsoft Azure 儲存體備援選項和讀取權限異地備援儲存體 ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP 文件：Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
