---
title: Azure 至 Azure 複寫問題和錯誤的 Azure Site Recovery 疑難排解 | Microsoft Docs
description: 對於複寫 Azure 虛擬機器進行嚴重損壞修復時發生的錯誤和問題進行疑難排解
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9a32ac1ae71cb7bd89c4252157c3a5cd395b2694
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842334"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>針對 Azure 至 Azure VM 持續複寫問題進行疑難排解

本文說明從一個區域將 Azure 虛擬機器複寫和復原到另一個區域時，關於 Azure Site Recovery 的常見問題，並解說如何進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。


## <a name="recovery-points-not-getting-generated"></a>未產生復原點

錯誤訊息：在過去 60 分鐘沒有可供 VM 使用的損毀一致復原點。</br>
錯誤識別碼：153007 </br>

Azure Site Recovery 會以一致方式將資料從來源區域複寫到災害復原區域，且每隔 5 分鐘建立損毀一致點。 如果 Site Recovery 長達 60 分鐘無法建立復原點，則會對使用者提出警示。 以下是可能導致此錯誤的原因：

**原因 1：[來源虛擬機器上的高資料變更率](#high-data-change-rate-on-the-source-virtal-machine)**    
**原因 2：[網路連線問題](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>原因和解決方案

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>來源虛擬機器上的高資料變更率
如果來源虛擬機器上的資料變更率高於支援的限制，則 Azure Site Recovery 會引發事件。 若要檢查問題是否由於高變換率造成，請移至 複寫的項目 > VM > 按一下 事件 - 過去 72 小時。
您應會看到如以下螢幕擷取畫面所示的 [資料變更率超過支援的限制] 事件。

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果您按一下此事件，應會看到如以下螢幕擷取畫面所示的確切磁碟資訊。

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供 Azure Site Recovery 限制。 上述限制是以我們的測試為基礎，但無法涵蓋所有可能的應用程式 I/O 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。 我們也應該注意有兩個需要考量的限制：每個磁碟資料變換率和每個虛擬機器資料變換率。
例如，如果我們查看下表中的進階 P20 磁碟，則 Site Recovery 可為每個磁碟處理 5 MB/s 變換率，而每部 VM 最多五個這類磁碟，因為每部 VM 有 25 MB/秒整體變換率的限制。

**複寫儲存體目標** | **平均來源磁碟 I/O 大小** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB  | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |  每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |10 MB/秒 | 每個磁碟 842 GB

### <a name="solution"></a>解決方法
我們必須了解 Azure Site Recovery 的資料變更率限制是以磁碟類型為基礎。 若要知道此問題為重複發生或暫時發生，請務必尋找受影響虛擬機器的資料變更率模式。
若要尋找受影響虛擬機器的資料變更率。 移至來源虛擬機器 > [監視] 底下的計量，並新增如下所示的計量。

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 按一下 [新增計量]，然後新增 [OS 磁碟寫入位元組/秒] 和 [資料磁碟寫入位元組/秒]。
2. 監視如螢幕擷取畫面所示的激增。
3. 它會顯示發生於 OS 磁碟和所有資料磁碟的整體寫入作業。 現在這些計量可能不會告訴您每個磁碟層級資訊，但是整體資料變換率模式的良好指標。

在如上所示的情況下，如果是偶發性的資料高載，資料變更率超過 10 MBps (適用於進階儲存體) 和 2 MBps (適用於標準儲存體) 的情況只持續一段時間便下降，複寫便可趕上進度。 不過，如果變換率大多時候超過支援的限制，則應該考慮下列其中一個選項 (可能的話)：

**選項 1：** 排除會導致高資料變更率的磁碟： </br>
您目前可以使用 [Site Recovery Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) 排除磁碟

**選項 2：** 變更災害復原儲存磁碟層： </br>
只有磁碟資料變換率小於 10 MB/秒 時，才可能使用此選項。 假設有採用 P10 磁碟的 VM，其資料變換率大於 8 MB/秒，但小於 10 MB/秒。 如果客戶可以在保護期間教 P30 磁碟用於目標儲存體，則可解決此問題。

### <a name="Network-connectivity-issue"></a>網路連線問題

#### <a name="network-latency-to-cache-storage-account-"></a>針對快取儲存體帳戶的網路延遲：
 Site Recovery 會將複寫的資料傳送至快取儲存體帳戶，因此在從虛擬機器將資料上傳至快取儲存體帳戶的速度小於每 3 秒 4 MB 的情況下，便可能會發生該錯誤。 若要檢查是否有任何延遲相關問題，請使用 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 來從虛擬機器將資料上傳至快取儲存體資料庫。<br>
如果延遲很高，請檢查您是否正在使用網路虛擬設備來控制來自 VM 的輸出網路流量。 如果所有複寫流量都會通過 NVA，系統就可能會對該設備進行節流。 建議您在虛擬網路中為「儲存體」建立一個網路服務端點，這樣複寫流量就不會流向 NVA。 請參閱[網路虛擬設備設定](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>網路連線
若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。</br>
請參閱 [Site Recovery URL 的輸出連線能力](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)以確保所有 URL 皆已連線 