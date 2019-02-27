---
title: Azure 至 Azure 複寫持續性問題的 Azure Site Recovery 疑難排解 | Microsoft Docs
description: 對於複寫 Azure 虛擬機器進行災害復原時發生的錯誤和問題進行疑難排解
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299593"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>針對 Azure 至 Azure VM 複寫的持續性問題疑進行難排解

本文說明當您在區域間複寫及復原 Azure 虛擬機器時的 Azure Site Recovery 常見問題。 它也會說明如何進行疑難排解。 如需受支援組態的詳細資訊，請參閱[複寫 Azure VM 的支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

Azure Site Recovery 會以一致方式將資料從來源區域複寫到災害復原區域，且每隔 5 分鐘建立損毀一致復原點。 如果 Site Recovery 長達 60 分鐘無法建立復原點，則以此資訊對您提出警示：

錯誤訊息：「在過去 60 分鐘沒有可供 VM 使用的損毀一致復原點。」</br>
錯誤識別碼：153007 </br>

下列各節說明原因和解決方案。

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>來源虛擬機器上的高資料變更率
如果來源虛擬機器上的資料變更率高於支援的限制，則 Azure Site Recovery 會引發事件。 若要檢查問題是否由於高變換率造成，請移至 [複寫的項目] > [VM] > [事件 - 過去 72 小時]。
您應會看到 [資料變更率超過支援的限制] 事件：

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果您選取此事件，應會看到確切的磁碟資訊：

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供 Azure Site Recovery 限制。 上述限制是以我們的測試為基礎，但無法涵蓋所有可能的應用程式 I/O 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。 

有兩個需要考量的限制：每個磁碟的資料變換率和每部虛擬機器的資料變換率。 例如，我們可以查看下表中的進階 P20 磁碟。 Site Recovery 可為每個磁碟處理 5 MB/秒的變換率，而每部 VM 最多五個這類磁碟，因為每部 VM 有 25 MB/秒的整體變換率限制。

**複寫儲存體目標** | **來源磁碟的平均 I/O 大小** |**來源磁碟的平均資料變換率** | **來源磁碟資料的每日整體資料變換率**
---|---|---|---
標準儲存體 | 8 KB | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB  | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |  每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |10 MB/秒 | 每個磁碟 842 GB

### <a name="solution"></a>解決方法
Azure Site Recovery 的資料變更率限制是以磁碟類型為基礎。 若要知道此問題為重複發生或暫時發生，請尋找受影響虛擬機器的資料變更率。 移至來源虛擬機器，在 [監視] 底下尋找計量，並新增如此螢幕擷取畫面所示的計量：

![尋找資料變更率的三步驟程序](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 選取 [新增計量]，然後新增 [OS 磁碟寫入位元組/秒] 和 [資料磁碟寫入位元組/秒]。
2. 監視如螢幕擷取畫面所示的激增。
3. 檢視發生於 OS 磁碟和所有資料磁碟的整體寫入作業。 這些計量可能不會提供給您每個磁碟層級的資訊，但表示資料變換率的整體模式。

如果峰值是偶發性資料高載，且資料變更率超過 10 MB/s (適用於進階儲存體) 和 2 MB/s (適用於標準儲存體) 的情況只持續一段時間便下降，複寫便可趕上進度。 但如果變換率大多時候超過支援的限制，請考慮下列其中一個選項 (可能的話)：

* **排除會導致高資料變更率的磁**碟：您可以使用 [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) 排除磁碟。
* **變更災害復原儲存磁碟層**：只有磁碟資料變換率小於 10 MB/秒時，才可能使用此選項。 假設有採用 P10 磁碟的 VM，其資料變換率大於 8 MB/秒，但小於 10 MB/秒。 如果客戶可以在保護期間將 P30 磁碟用於目標儲存體，則可解決此問題。

## <a name="Network-connectivity-problem"></a>網路連線問題

### <a name="network-latency-to-a-cache-storage-account"></a>快取儲存體帳戶的網路延遲
Site Recovery 會將複寫的資料傳送到快取儲存體帳戶。 如果將資料從虛擬機器上傳到快取儲存體帳戶的速度低於 3 秒 4 MB，您可能會看到網路延遲。 

若要檢查是否有延遲相關問題，請使用 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 來從虛擬機器將資料上傳至快取儲存體資料庫。 如果延遲很高，請檢查您是否使用網路虛擬設備 (NVA) 來控制來自 VM 的輸出網路流量。 如果所有複寫流量都會通過 NVA，系統就可能會對該設備進行節流。 

建議您在虛擬網路中為「儲存體」建立一個網路服務端點，這樣複寫流量就不會流向 NVA。 如需詳細資訊，請參閱[網路虛擬設定組態](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>網路連線
若要使 Site Recovery 複寫正常運作，VM 需要特定 URL 或 IP 範圍的輸出連線能力。 如果您的 VM 位於防火牆後方，或使用網路安全性群組 (NSG) 規則控制輸出連線能力，您可能會遇到下列其中一個問題。 若要確保所有 URL 皆已連線，請參閱 [Site Recovery URL 的輸出連線能力](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 