---
title: SAP HANA Azure 虛擬機器存放裝置設定 |Microsoft Docs
description: 儲存體 VM 的建議可在其中部署的 SAP HANA。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735507"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 提供不同類型的儲存體，適用於執行 SAP HANA 的 Azure Vm。 可以考慮 SAP HANA 部署清單，例如 Azure 儲存體類型： 

- 標準的 SSD 磁碟機 (SSD)
- 進階固態硬碟 (SSD)
- 公開預覽狀態的強力 SSD，且尚不支援生產環境 SAP 應用程式

若要深入了解這些磁碟類型，請參閱本文[選取的磁碟類型](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

**建議：Azure 寫入加速器搭配使用 Azure 進階儲存體和用於部署的 Azure 受控磁碟**

在內部部署世界中，您很少需要在意 I/O 子系統和其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建置 Azure 基礎結構，您應該留意這些需求的一部分。 某些系統會要求的最小輸送量特性會導致需要：

- 在 啟用讀取/寫入**hana/log** 250 MB/秒 1 MB I/O 大小
- 針對 **/hana/data** 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動
- 針對 **/hana/data** 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，它是**強制**利用 Azure 進階磁碟，如 **/hana/data**並**hana/log**磁碟區。 為了達到如 SAP 想要的 **/hana/log** 和 **/hana/data** 最小輸送量，您必須使用 MDADM 或 LVM 在多個 Azure 進階儲存體磁碟上建置 RAID 0。 然後使用 RAID 磁碟區作為 **/hana/data** 和 **/hana/log** 磁碟區。 

**建議：等量磁碟的 RAID 0 建議大小為使用：**

- 針對 **/hana/log** 使用 64 KB或 128 KB
- 針對 **/hana/log** 使用 32 KB

> [!NOTE]
> 您不需要使用 RAID 磁碟區設定任何備援層級，因為 Azure 進階和標準儲存體會保存三個 VHD 的映像。 RAID 磁碟區的使用方式單純是用來設定會提供足夠 I/O 輸送量的磁碟區。

RAID 下的 Azure VHD 數目累計，是從 IOPS 和儲存體輸送量端累計。 因此，如果您將 RAID 0 放在 3 x P30 Azure 進階儲存體磁碟上，它應該會給您單一 Azure 進階儲存體 P30 磁碟的 3 倍 IOPS 和 3 倍儲存體輸送量。

下列快取建議假設 SAP HANA 的 I/O 特性如下所示：

- 幾乎沒有任何針對 HANA 資料檔案的讀取工作負載。 例外是在資料載入 HANA 時，HANA 執行個體重新啟動之後的大型 I/O。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 因此，讀取快取大多不合理，因為在大部分的情況下，必須完整讀取所有資料檔案磁碟區。
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點是非同步的，且不會造成任何使用者交易延遲。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 不過，損毀復原應該不是例外狀況
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外是在執行交易記錄備份、損毀復原，或 HANA 執行個體重新啟動階段的大型 I/O。  
- 對於 SAP HANA 重做記錄檔的主要負載是寫入。 根據工作負載的性質，您的 I/O 可以小到 4 KB，而在其他情況下，I/O 大小也可以是 1 MB 或更大。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有寫入都必須以可靠的方式保存在磁碟上

**建議：由 SAP HANA 這些觀察到的 I/O 模式，結果應該類似設定使用 Azure 進階儲存體之不同磁碟區快取：**

- **/hana/data**：無快取
- **/hana/log**：無快取 - M 系列例外 (請參閱本文件後面部分)
- **/hana/shared**：讀取快取


當調整 VM 大小或決定 VM 時，也請注意整體 VM I/O 輸送量。 [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中說明整體虛擬機器儲存體輸送量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 透過 Linux 廠商和 SAP 的一般建議是重新設定的磁碟區的 I/O 排程器模式**cfq**模式**noop**模式。 詳細資料記載於 [SAP 附註編號 1984798](https://launchpad.support.sap.com/#/notes/1984787) \(英文\)。 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>使用適用於 Azure M 系列虛擬機器的 Azure Write Accelerator 的生產儲存體解決方案
Azure 寫入加速器是專門為 Azure M 系列 VM 推出的功能。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此， **/hana/data**並**hana/log**會使用 Azure 寫入加速器支援不同的磁碟區**hana/log**只使用磁碟區。 

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

**建議：針對實際執行案例的建議的設定看起來如下：**

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | /hana/log | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 VM 類型所不尚未由 Microsoft 所提供給大眾。 請檢查不同的建議磁碟區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 調整大小的更多的 vhd，比列出增加 IOPS 的磁碟區和 Azure 虛擬機器類型的限制內的 I/O 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 所以至少必須將組成 **/hana/log** 磁碟區的 Azure 進階儲存體磁碟部署為受控磁碟。

Azure 寫入加速器可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其上限。 目前的上限是：

- M128xx 和 M416xx vm 16 個 Vhd
- M64xx 和 M208xx VM 的 8 個 Vhd
- M32xx VM 上限 4 個 VHD

如需如何啟用 Azure Write Accelerator 的詳細指示，可以在[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中找到。

Azure Write Accelerator 的詳細資料和限制可以在相同文件中找到。

**建議：您必須使用以組成 /hana/log 磁碟區的磁碟的寫入加速器**


## <a name="cost-conscious-azure-storage-configuration"></a>節省成本的 Azure 儲存體組態
下表顯示要裝載在 Azure Vm 上的 SAP HANA 的客戶也使用的 VM 類型的設定。 可能有某些可能不符合 SAP HANA 所有的最小的儲存體篩選條件，或未正式支援 SAP HANA 與 sap 的 VM 類型。 但是目前這些虛擬機器似乎可以針對非生產案例正常執行。 **/Hana/data**並**hana/log**會結合到一個磁碟區。 如此一來 Azure 寫入加速器的使用方式可能會根據 IOPS 的限制。

> [!NOTE]
> SAP 支援的情況下，檢查是否特定 VM 類型支援 SAP hana 中的 sap [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。

> [!NOTE]
> 從成本意識的解決方案，如先前建議的變更是將從移動[Azure 標準 HDD 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)更好的執行，且更可靠[Azure 標準 SSD 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 VM 類型所不尚未由 Microsoft 所提供給大眾。 根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要對 **/hana/backup** 磁碟區 (已調整大小來保持備份為記憶體磁碟區的兩倍) 進行變更，您可以自由調整。   
請檢查不同的建議磁碟區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 調整大小的更多的 vhd，比列出增加 IOPS 的磁碟區和 Azure 虛擬機器類型的限制內的 I/O 輸送量。 

> [!NOTE]
> 上述組態「不會」受益於 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因為它是使用 Azure 進階儲存體和 Azure 標準儲存體的混合。 不過，為了最佳化成本而選擇了這個選項。 您必須針對以上列為 Azure 標準儲存體 (Sxx) 的所有磁碟，選擇進階儲存體，讓 VM 組態符合 Azure 單一 VM SLA 的規範。


> [!NOTE]
> 所述磁碟組態建議是以 SAP 對其基礎結構提供者所表示的最低需求為目標。 在實際的客戶部署和工作負載案例中，還是會遇到這些建議仍未能提供足夠功能的情形。 這些情況可能是客戶在 HANA 重新啟動之後需要更快的資料重新載入速度，或者備份組態需要儲存體的更高頻寬。 其他案例包括 **/hana/log**，其中 5000 IOPS 對於特定工作負載來說不足。 因此請將這些建議當作起點，並且根據工作負載的需求來調整。
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Azure 的 SAP HANA 的強力的 SSD 儲存體組態
Microsoft 在過程中引入新的 Azure 儲存體類型稱為[Azure 強力 SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/)。 到目前為止所提供的 Azure 儲存體和強力的 SSD 最大的差別是，磁碟功能不受限於磁碟的大小不再。 身為客戶中，您可以定義這些功能的強力的 SSD:

- 範圍從 4 GiB 到 65536 GiB 的磁碟大小
- 160 個 IOPS 的 IOPS 範圍從 100 IOPS （取決於 VM 類型的最大值）
- 儲存體輸送量從 300 MB/秒 2,000 MB/秒

如需詳細資料查詢文章[宣布強力 SSD – 新一代的 Azure 磁碟技術 （預覽）](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD 可讓您定義可滿足您的大小、 IOPS 和磁碟輸送量範圍的單一磁碟。 而不是建構滿足 IOPS 和儲存體的輸送量需求的磁碟區使用 LVM 或 MDADM 在 Azure 進階儲存體上的邏輯磁碟區管理員。 您可以執行 設定混合 UltraSSD 和進階儲存體之間。 如此一來，您可以在 限制的效能關鍵 /hana/data 和 /hana/log 磁碟區的使用量 UltraSSD 的並涵蓋使用 Azure 進階儲存體的其他磁碟區

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | hana/data 磁碟區 | hana/data I/O 輸送量 | hana/資料 IOPS | /hana/log volume | hana/log I/O 輸送量 | hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GB | 1000 MB/秒 | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1,000 MB/秒 |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1,000 MB/秒 | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2,000 MB/秒 |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2,000 MB/秒 | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1,000 MB/秒 | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1,000 MB/秒 | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2,000 MB/秒 | 7200 GB | 1500M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

M416xx_v2 VM 類型所不尚未由 Microsoft 所提供給大眾。 所列的值要作為起始點，而且需要根據實際需求進行評估。 Azure 的強力 SSD 的優點是可調整而不需要關閉 VM 的 IOPS 和輸送量的值，或暫停的工作負載套用至系統。   

> [!NOTE]
> 到目前為止，使用 UltraSSD 的儲存體的儲存體快照集無法使用。 這會封鎖使用 Azure 備份服務的 VM 快照集的使用方式

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱

- [Azure 虛擬機器的 SAP HANA 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。