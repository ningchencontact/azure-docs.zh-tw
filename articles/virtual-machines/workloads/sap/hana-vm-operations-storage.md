---
title: SAP Hana Azure 虛擬機器儲存體設定 |Microsoft Docs
description: 已部署 SAP Hana 之 VM 的儲存體建議。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 45d8844a34c5b7d9f36099304c1619e09d39305c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099621"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虛擬機器儲存體設定

Azure 提供不同類型的儲存體, 適用于執行 SAP Hana 的 Azure Vm。 可視為 SAP Hana 部署的 Azure 儲存體類型清單, 例如: 

- 標準 SSD 磁片磁碟機 (SSD)
- 進階固態硬碟 (SSD)
- [Ultra 磁片](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

若要瞭解這些磁片類型, 請參閱[選取磁片類型一](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)文

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。 

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

**建議搭配 Azure 寫入加速器使用 Azure 進階儲存體並使用 Azure 受控磁碟進行部署**

在內部部署環境中, 您很少需要在意 i/o 子系統及其功能。 原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建立 Azure 基礎結構時, 您應該注意其中一些需求。 要求的部分最低輸送量特性會導致需要:

- 在 250 MB/秒的 **/hana/log**上啟用讀取/寫入 (具有 1 mb i/o 大小)
- 針對 **/hana/data** 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動
- 針對 **/hana/data** 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 等 DBMS 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此, 您**必須**利用適用于 **/Hana/data**和 **/Hana/log**磁片區的 Azure Premium 磁片。 為了達到如 SAP 想要的 **/hana/log** 和 **/hana/data** 最小輸送量，您必須使用 MDADM 或 LVM 在多個 Azure 進階儲存體磁碟上建置 RAID 0。 然後使用 RAID 磁碟區作為 **/hana/data** 和 **/hana/log** 磁碟區。 

**建議做為 RAID 0 的等量大小, 建議使用:**

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

**建議由於 SAP Hana 觀察到的 i/o 模式, 因此使用 Azure 進階儲存體的不同磁片區快取應該設定如下:**

- **/hana/data**：無快取
- **/hana/log**：無快取 - M 系列例外 (請參閱本文件後面部分)
- **/hana/shared**：讀取快取


當調整 VM 大小或決定 VM 時，也請注意整體 VM I/O 輸送量。 [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中說明整體虛擬機器儲存體輸送量。

## <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 透過 Linux 廠商和 SAP 的一般建議是將磁片區的 i/o 排程器模式從**cfq**模式重新設定為**noop**模式。 詳細資料記載於 [SAP 附註編號 1984798](https://launchpad.support.sap.com/#/notes/1984787) \(英文\)。 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>適用于 Azure M 系列虛擬機器的 Azure 寫入加速器的生產儲存體解決方案
Azure 寫入加速器是專門為 Azure M 系列 VM 推出的功能。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 **/hana/log** 磁碟區。 因此, **/hana/data**和 **/hana/log**是不同的磁片區, 只有支援 **/hana/log**磁片區的 Azure 寫入加速器。 

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是專用於 **/hana/log** 磁碟區的 Azure Write Accelerator。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 **/hana/log** 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

**建議生產案例的建議設定如下所示:**

| VM SKU | RAM | 最大 VM I/O<br /> 輸送量 | /hana/data | /hana/log | HANA/shared | /root volume | /usr/sap | hana/backup |
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

M416xx_v2 VM 類型尚未由 Microsoft 提供給公眾使用。 檢查不同建議磁片區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用超過列出的 Vhd 來調整磁片區大小, 會增加 Azure 虛擬機器類型限制內的 IOPS 和 i/o 輸送量。

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 所以至少必須將組成 **/hana/log** 磁碟區的 Azure 進階儲存體磁碟部署為受控磁碟。

Azure 寫入加速器可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其上限。 目前的上限是：

- 適用于 M128xx 和 M416xx VM 的16個 Vhd
- 8個適用于 M64xx 和 M208xx VM 的 Vhd
- M32xx VM 上限 4 個 VHD

如需如何啟用 Azure Write Accelerator 的詳細指示，可以在[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中找到。

Azure Write Accelerator 的詳細資料和限制可以在相同文件中找到。

**建議您需要將寫入加速器用於形成/hana/log 磁片區的磁片**


## <a name="cost-conscious-azure-storage-configuration"></a>節省成本的 Azure 儲存體組態
下表顯示客戶也用來在 Azure Vm 上裝載 SAP Hana 之 VM 類型的設定。 可能有一些 VM 類型可能無法符合 SAP Hana 的所有最小儲存準則, 或未正式支援 SAP SAP Hana。 但是目前這些虛擬機器似乎可以針對非生產案例正常執行。 **/Hana/data**和 **/hana/log**會合並成一個磁片區。 因此, Azure 寫入加速器的使用量可能會成為 IOPS 方面的限制。

> [!NOTE]
> 針對 SAP 支援的案例, 請檢查[IAAS 的 sap 檔](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中是否支援 sap 所 SAP Hana 的特定 VM 類型。

> [!NOTE]
> 針對符合成本效益的解決方案, 先前的建議變更是從[Azure 標準 HDD 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)移到更好的效能, 並更可靠地執行[azure 標準 SSD 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| VM SKU | RAM | 最大 VM I/O<br /> 輸送量 | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
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


M416xx_v2 VM 類型尚未由 Microsoft 提供給公眾使用。 根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要對 **/hana/backup** 磁碟區 (已調整大小來保持備份為記憶體磁碟區的兩倍) 進行變更，您可以自由調整。   
檢查不同建議磁片區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 **/hana/data** 和 **/hana/log** 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用超過列出的 Vhd 來調整磁片區大小, 會增加 Azure 虛擬機器類型限制內的 IOPS 和 i/o 輸送量。 

> [!NOTE]
> 上述組態「不會」受益於 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因為它是使用 Azure 進階儲存體和 Azure 標準儲存體的混合。 不過，為了最佳化成本而選擇了這個選項。 您必須針對以上列為 Azure 標準儲存體 (Sxx) 的所有磁碟，選擇進階儲存體，讓 VM 組態符合 Azure 單一 VM SLA 的規範。


> [!NOTE]
> 所述磁碟組態建議是以 SAP 對其基礎結構提供者所表示的最低需求為目標。 在實際的客戶部署和工作負載案例中，還是會遇到這些建議仍未能提供足夠功能的情形。 這些情況可能是客戶在 HANA 重新啟動之後需要更快的資料重新載入速度，或者備份組態需要儲存體的更高頻寬。 其他案例包括 **/hana/log**，其中 5000 IOPS 對於特定工作負載來說不足。 因此請將這些建議當作起點，並且根據工作負載的需求來調整。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>適用于 SAP Hana 的 Azure Ultra 磁片儲存體設定
Microsoft 正在推出新的 Azure 儲存體類型 (稱為[Azure Ultra 磁片](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk))。 目前為止所提供的 Azure 儲存體與 Ultra 磁片之間的最大差異, 在於磁片功能不會再系結到磁片大小。 身為客戶, 您可以為 Ultra 磁片定義這些功能:

- 磁片的大小, 範圍從 4 GiB 到 65536 GiB
- IOPS 的範圍從 100 IOPS 到 160K IOPS (最大值也取決於 VM 類型)
- 從 300 MB/秒到 2000 MB/秒的儲存體輸送量

Ultra 磁片可讓您定義單一磁片, 以滿足您的大小、IOPS 和磁片輸送量範圍。 不是在 Azure 進階儲存體上使用邏輯磁片區管理員 (例如 LVM 或 MDADM) 來建立可滿足 IOPS 和儲存體輸送量需求的磁片區。 您可以在 Ultra 磁片與進階儲存體之間執行設定混合。 因此, 您可以將 Ultra 磁片的使用量限制在效能關鍵/hana/data 和/hana/log 磁片區, 並使用 Azure 進階儲存體

> [!IMPORTANT]
> Ultra 磁片尚未出現在所有 Azure 區域中, 也尚未支援所有 VM 類型。 如需可用 Ultra 磁片和支援哪些 VM 系列的詳細資訊, 請參閱[Azure 中有哪些磁片類型可供使用？](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk)一文。

| VM SKU | RAM | 最大 VM I/O<br /> 輸送量 | /hana/data 磁片區 | /hana/data i/o 輸送量 | /hana/data IOPS | /hana/log 磁片區 | /hana/log i/o 輸送量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1200 MB/秒 | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GB | 1000 MB/秒 | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1000 MB/秒 |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1000 MB/秒 | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2000 MB/秒 |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2000 MB/秒 | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 7200 GB | 1500M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

M416xx_v2 VM 類型尚未由 Microsoft 提供給公眾使用。 列出的值是一個起點, 而且需要針對真正的需求進行評估。 Azure Ultra 磁片的優點是可以調整 IOPS 和輸送量的值, 而不需要關閉 VM 或停止套用至系統的工作負載。   

> [!NOTE]
> 到目前為止, 無法使用具有 Ultra 磁片儲存體的儲存體快照集。 這會封鎖使用 Azure 備份服務的 VM 快照集

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱：

- [適用于 Azure 虛擬機器的 SAP Hana 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。