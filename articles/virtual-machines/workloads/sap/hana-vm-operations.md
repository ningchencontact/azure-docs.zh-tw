---
title: SAP HANA 在 Azure 上的基礎結構設定和作業 | Microsoft Docs
description: 部署在 Azure 虛擬機器上之 SAP HANA 系統的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699321"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA 在 Azure 上的基礎結構設定和作業
本文章提供有關如何設定 Azure 基礎結構和操作 Azure 的原生虛擬機器 (Vm) 部署的 SAP HANA 系統的指引。 這篇文章也包含 SAP HANA 相應放大 M128s VM sku 的組態資訊。 這篇文章不旨在取代標準 SAP 文件，其中包含下列內容：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安裝指南](https://service.sap.com/instguides)
- [SAP 附註](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>必要條件
若要使用本指南，您需要下列 Azure 元件的基本知識：

- [Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 網路和虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 儲存體](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

若要了解有關 SAP NetWeaver 及 Azure 上其他 SAP 元件的詳細資訊，請參閱 [Azure 文件](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。

## <a name="basic-setup-considerations"></a>基本設定考量
下列各節說明在 Azure VM 上部署 SAP HANA 系統的基本安裝考量。

### <a name="connect-to-azure-virtual-machines"></a>連接到 Azure 虛擬機器
中所述[Azure 虛擬機器規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)，兩個基本的方法用來連接到 Azure Vm:

- 透過網際網路與 JumpBox VM 上的公用端點連線，或在 VM 上執行 SAP HANA。
- 透過 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 進行連線。

生產案例需要透過 VPN 或 ExpressRoute 的站對站連線能力。 饋送到實際執行案例的 SAP 軟體的使用位置的非生產案例也需要此類型的連線。 下圖顯示跨站台連線能力的範例：

![跨站台連線能力](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>選擇 Azure VM 類型
要用於實際執行案例的 Azure VM 類型所述[IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 非商業執行環境的情況下，較多的原生 Azure VM 類型使用。

>[!NOTE]
> 非商業執行環境的情況下，使用中所列的 VM 類型[SAP 附註 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 將 Azure Vm 用於生產案例，請檢查經 SAP HANA 認證的 Vm，在 SAP 發佈[認證 IaaS 平台清單](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。

若要部署 Azure 中的 Vm，請使用：

- Azure 入口網站。
- Azure PowerShell Cmdlet。
- Azure CLI。

您也可以部署的完整安裝的 SAP HANA 平台上的 Azure VM 服務，透過[SAP 雲端平台](https://cal.sap.com/)。 如需安裝程序資訊，請參閱[部署 SAP S/4HANA 或 BW/4HANA，在 Azure 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。 如需有關發行的自動化，請參閱[GitHub 上的本文](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)。

### <a name="choose-an-azure-storage-type"></a>選擇 Azure 儲存體類型
Azure 提供兩種類型的儲存體，適用於執行 SAP HANA 的 Azure Vm:  

- 標準的硬碟機 (Hdd)
- Premium 固態硬碟 (Ssd)

若要深入了解這些磁碟類型，請參閱[選取 磁碟類型](../../windows/disks-types.md)。

在 Azure 標準儲存體和進階儲存體，azure 會提供兩種部署方法的 Vhd。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。

如需儲存體類型和其在 IOPS 和儲存體輸送量的 Sla 的清單，請參閱 <<c0> [ 受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="configure-the-storage-for-azure-virtual-machines"></a>設定 Azure 虛擬機器的儲存體

您可能永遠不會導致 I/O 子系統和其功能的相關因為設備廠商，確保已符合 SAP HANA 的最小的儲存體需求。 當您自行建置 Azure 基礎結構時，您需要留意這些需求的一部分。 您也應該了解下列各節中建議的組態需求。 要將虛擬機器設定的情況下為 SAP HANA 上執行，您可能需要：

- 啟用至少 250MB/秒 1 MB I/O 大小在 /hana/log 上的讀取/寫入磁碟區。
- 啟用至少 400MB/秒的 /hana/data 16 MB 和 64MB I/O 大小的讀取的活動。
- 啟用至少 250MB/秒的 /hana/data 16 MB 和 64MB I/O 大小的寫入活動。

儲存體的低延遲是很重要的 DBMS 系統的即使像 SAP HANA DBMS 會保留在記憶體資料。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但作業，例如寫入儲存點，或載入記憶體中資料之後當機復原也可能很重要。 

使用 Azure 進階磁碟 /hana/data 和 /hana/log 磁碟區是必要的。 若要達到的 /hana/log 和 /hana/data 為 sap 所需的最小輸送量，請使用 mdadm 或邏輯磁碟區管理員 (LVM) 多個 Azure 進階儲存體磁碟上建置 RAID 0。 也使用 RAID 磁碟區作為 /hana/data 和 /hana/log 磁碟區。 我們建議您使用下列的等量磁碟區大小的 RAID 0:

- 64 KB 或 128 KB 的/hana/data
- Hana/log 32 KB

> [!NOTE]
> 您不需要使用 RAID 磁碟區，因為 Azure 進階和標準儲存體保留三個 VHD 映像設定任何備援層級。 使用 RAID 磁碟區單純是用來設定磁碟區，提供足夠 I/O 輸送量。

RAID 下的 Azure Vhd 數目是從 IOPS 和儲存體輸送量端累計。 如果您將 RAID 0 放透過 3 x P30 Azure 進階儲存體磁碟時，它可讓您 3 倍 IOPS 和單一 Azure 進階儲存體 P30 磁碟的儲存體輸送量快三次。

下列快取的建議假設適用於 SAP HANA 的 I/O 特性：

- 沒有針對 HANA 資料檔案不需要任何加工讀取工作負載。 例外狀況是大型 I/o 的 HANA 執行個體，或將資料載入 HANA 的重新啟動之後。 針對資料檔案讀取較大 I/O 的另一種情況可能是 HANA 資料庫備份。 如此一來，讀取快取沒有任何意義，因為在大部分情況下，所有的資料檔案磁碟區必須完整閱讀。
- 當 HANA 儲存點和 HANA 損毀復原時，對資料檔案的寫入會發生高載。 寫入儲存點為非同步，且不保留任何使用者交易。 因為系統必須再次快速回應，所以在損毀復原期間的資料寫入效能極為重要。 損毀復原應該相當例外的情況。
- 幾乎不會從 HANA 重做檔案進行任何讀取。 例外狀況是大型 I/o，當您執行交易記錄備份、 損毀復原或 HANA 執行個體的重新啟動階段。  
- 主要負載與針對 SAP HANA 重做記錄檔是寫入。 根據工作負載的本質，您可以有 I/o 為 4 KB，或在其他情況下，I/o 的小型 1 MB 或以上的大小。 對 SAP HANA 重做記錄的寫入延遲效能極為重要。
- 所有 writes 必須都保存在磁碟中可靠的方式。

因為由 SAP HANA 這些觀察到的 I/O 模式，使用 Azure 進階儲存體之不同磁碟區快取設定：

- **hana/data**:無快取。
- **hana/log**:無快取，並發生例外狀況的 M 系列 Vm （請參閱稍後在本文中）。
- **hana/共用**:讀取快取。


此外，請記住當您調整大小或決定 VM 上的整體 VM I/O 輸送量。 VM 儲存體輸送量中說明整體[記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)。

#### <a name="linux-io-scheduler-mode"></a>Linux I/O 排程器模式
Linux 有數個不同的 I/O 排程模式。 從 Linux 廠商和 SAP 的一般建議是設定以外的位置的磁碟區的 I/O 排程器模式**cfq**模式**noop**模式。 如需詳細資訊，請參閱 < [SAP 附註 #1984798](https://launchpad.support.sap.com/#/notes/1984787)。 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>儲存體解決方案，適用於 Azure 寫入加速器與 M 系列虛擬機器
 寫入加速器是即將推出適用於 Azure M 系列 Vm 專用的 Azure 功能。 功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 /hana/log 磁碟區。 基於這個理由，就必須變更到目前為止所顯示的組態。 主要變更是 /hana/data 和 /hana/log 之間的備份，以便針對 /hana/log 磁碟區只使用寫入加速器。 

> [!IMPORTANT]
> SAP HANA 認證適用於 Azure M 系列虛擬機器是專為 hana/log 磁碟區的寫入加速器。 如此一來，在 Azure 上實際執行案例的 SAP HANA 部署 M 系列虛擬機器會使用適用於 hana/Write Accelerator 來設定記錄磁碟區。

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

下表顯示建議的設定。

| VM SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data | /hana/log | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1,000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

請檢查不同的建議磁碟區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更高的磁碟區為 /hana/data 和 /hana/log，增加 Azure 進階儲存體 Vhd 的數目。 調整大小的更多的 vhd，比列出增加 IOPS 的磁碟區和 Azure 虛擬機器類型的限制內的 I/O 輸送量。

寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 有提供，形成 /hana/log 磁碟區的 Azure 進階儲存體磁碟，必須部署為受控磁碟中。

有一些限制的 Azure 進階儲存體每個寫入加速器可以支援的虛擬機器的 Vhd。 目前的上限是：

- 16 個 Vhd m128xx VM。
- 8 個 Vhd m64xx VM。
- 4 個 Vhd M32xx VM。

如需有關如何啟用寫入加速器的詳細資訊，請參閱 <<c0> [ 寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。

如需寫入加速器的限制和詳細資訊，請參閱相同的文件。


#### <a name="cost-conscious-azure-storage-configuration"></a>成本意識的 Azure 儲存體組態
下表顯示客戶經常用來在 Azure 虛擬機器上裝載 SAP HANA 的虛擬機器類型組態。 可能有某些不符合所有的最小準則適用於 SAP HANA 的 VM 類型。 也可能會有某些不正式支援 SAP HANA 與 SAP 的 VM 類型。 到目前為止，這些 Vm 也有正常執行非生產案例。 

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。


| VM SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 mdadm 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2,000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GB | 1000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1,000 GiB | 1000 MB/秒 | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 GiB | 1000 MB/秒 | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


建議使用較小的 VM 類型使用 3 個 x P20 超過提供的空間建議方面的磁碟區的磁碟[經 SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)。 選擇資料表中顯示已對 SAP hana 提供足夠的磁碟輸送量。 如果您需要變更**hana/備份**磁碟區，已調整大小以保留代表兩倍的記憶體磁碟區的備份，放心地進行調整。 

請檢查不同的建議磁碟區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更高的磁碟區為 /hana/data 和 /hana/log，增加 Azure 進階儲存體 Vhd 的數目。 調整大小的更多的 vhd，比列出增加 IOPS 的磁碟區和 Azure 虛擬機器類型的限制內的 I/O 輸送量。 

> [!NOTE]
> 先前的組態不受益[Azure 虛擬機器的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)因為它會使用 Azure 進階儲存體及 Azure 標準儲存體的混合。 選取項目已選擇可將成本最佳化。 選擇以 Azure 標準儲存體 (Sxx)，讓 VM 設定遵守 Azure 的單一 VM SLA 列出資料表中的所有磁碟的進階儲存體。


> [!NOTE]
> 磁碟組態的建議事項為目標的 SAP 讓其基礎結構提供者的最低需求。 在實際的客戶部署和工作負載案例中，這些建議中未提供足夠的功能，在某些情況下。 例如，客戶需要更快的重新載入資料的 HANA 重新啟動之後，或備份組態需要較高的頻寬，儲存體。 其他情況包括的 /hana/log，其中 5,000 IOPS 未滿足特定的工作負載。 使用這些建議作為起點，並採用它們根據工作負載的需求。
>  

### <a name="set-up-azure-virtual-networks"></a>設定 Azure 虛擬網路
Azure 透過 VPN 或 Azure ExpressRoute 的站對站連線時，您必須至少一個 Azure 的虛擬網路透過 VPN 或 ExpressRoute 線路的虛擬閘道連線。 在簡單的部署中，可以過裝載 SAP HANA 執行個體的 Azure 虛擬網路子網路中部署虛擬閘道。 

若要安裝 SAP HANA，建立兩個 Azure 虛擬網路內的其他子網路。 一個子網路會裝載執行 SAP HANA 執行個體的 VM。 JumpBox 或管理 Vm 來裝載 SAP HANA Studio、 其他管理軟體或應用程式軟體，則會執行另一個子網路。

> [!IMPORTANT]
> 設定[Azure 網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)中 SAP 應用程式和 DBMS 層的 SAP NetWeaver-之間的通訊路徑，Hybris 或 S/4HANA 為基礎的 SAP 系統不支援。 這項限制是基於功能和效能的考量。 SAP 應用程式層與 DBMS 層之間的通訊路徑必須是一個直接的帳戶。 這項限制不包含[應用程式安全性群組 (ASG) 和網路安全性的群組 (NSG) 規則](https://docs.microsoft.com/azure/virtual-network/security-overview)如果 ASG 和 NSG 規則允許直接通訊路徑。 
>
> 其他情況下，不支援網路虛擬設備的位置是： 
>
> - 代表 Linux Pacemaker 的 Azure Vm 之間的通訊路徑叢集節點和 SBD 裝置中所述[適用於 SUSE Linux Enterprise Server for SAP 應用程式上的 Azure Vm 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)。
> - Azure Vm 和 Windows Server 向外延展檔案伺服器之間的通訊路徑設定中所述的最多[Windows 容錯移轉叢集上叢集 SAP ASCS/SCS 執行個體，在 Azure 中使用檔案共用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)。 
>
> 網路虛擬設備在通訊路徑中的可以輕鬆地 double 的兩個通訊合作夥伴之間的網路延遲。 它們也可以限制在 SAP 應用程式層與 DBMS 層之間的重要路徑中的輸送量。 在某些客戶情況下，網路虛擬設備可能會導致失敗的 Pacemaker Linux 叢集。 這些是 Linux Pacemaker 叢集節點間通訊通訊的地方 SBD 裝置透過網路虛擬設備的情況。  
> 

> [!IMPORTANT]
> 另一個所設計的*不*支援會隔離到不同 Azure 虛擬網路的 SAP 應用程式層與 DBMS 層[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)彼此。 我們建議使用不同的 Azure 虛擬網路中使用而不是在 Azure 虛擬網路內的子網路隔離的 SAP 應用程式層和 DBMS 層。 
>
>如果您決定不要遵循建議，並改為隔離到不同的虛擬網路的兩個層，必須是兩個虛擬網路[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 
>
> 請注意，網路兩個之間的流量[對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虛擬網路受到傳輸成本。 SAP 應用程式層和 DBMS 層之間交換包含多個 tb 的大型資料磁碟區。 如果 SAP 應用程式層和 DBMS 層隔離出來兩個對等互連的 Azure 虛擬網路之間，您可以累積大量的成本。 

當您安裝執行 SAP HANA 的 VM 時，VM 將會需要：

- 已安裝的兩個虛擬 Nic。 一個 NIC 會連線到管理子網路。 另一個 NIC 會從內部部署網路或其他網路連線到 Azure VM 中的 SAP HANA 執行個體。
- 已針對這兩個虛擬 NIC 部署的靜態私人 IP 位址。

> [!NOTE]
> 指派靜態 IP 位址，透過 Azure，表示將它們指派給個別的虛擬 Nic。 請勿將客體 OS 內的靜態 IP 位址指派給虛擬 nic。 某些 Azure 服務，例如 Azure 備份依賴在最主要的虛擬 NIC 設定 DHCP，不適用於靜態 IP 位址。 如需詳細資訊，請參閱 <<c0> [ 疑難排解 Azure 虛擬機器備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 若要將多個靜態 IP 位址指派至 VM，請指派多個虛擬 Nic 的 vm。
>
>

會持續的部署，請在 Azure 中建立的虛擬資料中心網路架構。 此架構中，建議您連接到內部部署至不同的 Azure 虛擬網路的 Azure 虛擬網路閘道的區隔。 此個別的虛擬網路會裝載會保留在內部部署環境或網際網路的所有流量。 您可以使用這種方法，來部署軟體稽核和記錄檔流量在個別的中樞虛擬網路中，輸入在 Azure 中的虛擬資料中心。 如此一來，您有一個虛擬網路所有的軟體和組態相關 ingoing 和傳出的流量，您的 Azure 部署到該主機。


如需有關虛擬資料中心的方法和相關的 Azure 虛擬網路設計的詳細資訊，請參閱： 

- [Azure 虛擬資料中心：網路觀點](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)。
- [Azure 虛擬資料中心和 enterprise 控制平面](https://docs.microsoft.com/azure/architecture/vdc/)。


>[!NOTE]
>使用中樞虛擬網路和支點虛擬網路之間流動的流量[Azure 虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)額外受限於[成本](https://azure.microsoft.com/pricing/details/virtual-network/)。 根據這些成本，您可能需要執行嚴格的中樞輪輻網路設計和執行多個之間的兩難[Azure ExpressRoute 閘道](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)您以略過虛擬網路對等互連連線到支點。 
>
> Azure ExpressRoute 閘道引進額外[成本](https://azure.microsoft.com/pricing/details/vpn-gateway/)太。 您也可能會遇到第三方軟體，用來記錄、 稽核和監視網路流量的額外的成本。 請考慮透過額外的 ExpressRoute 閘道和軟體授權所建立的成本與對等互連的虛擬網路的交換資料的成本。 您可能會決定上一個虛擬網路內的微型分割為隔離單元，而不是虛擬網路中使用子網路。


如需您可以用來指派 IP 位址的不同方法的概觀，請參閱 < [IP 位址類型及配置方法，在 Azure 中的](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 

針對執行 SAP HANA 的 Vm，使用指派的靜態 IP 位址。 原因是 HANA 的某些組態屬性會參考 IP 位址。

[Azure Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)用來將流量路由傳送至 SAP HANA 執行個體或 JumpBox。 Nsg 和最終[應用程式安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)與 SAP HANA 子網路和管理子網路相關聯。

下圖顯示遵循中樞和支點虛擬網路架構的 SAP HANA 粗略部署結構描述的概觀：

![SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking.PNG)

若要部署在 Azure 中的 SAP HANA，不需要透過站對站連線，防護從公用網際網路的 SAP HANA 執行個體，並隱藏正向 proxy 後方。 在這個基本案例中，部署會仰賴 Azure 的內建 DNS 服務，以解析主機名稱。 在使用公眾對應 IP 位址的較複雜部署中，Azure 內建的 DNS 服務特別重要。 使用 Azure Nsg 並[Azure 網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)監視到您的 Azure 虛擬網路架構，在 Azure 中從網際網路路由。 

下圖顯示如何不需要在中樞和支點虛擬網路架構中的站對站連線部署 SAP HANA 的概略結構描述：
  
![不具站台對站台連線能力的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking2.PNG)
 

另一個說明如何使用 Azure 的網路虛擬設備來控制和監視的存取，從網際網路而不使用中樞和支點虛擬網路架構，請參閱[部署高可用性的網路虛擬設備](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>設定 SAP HANA 相應放大的 Azure 基礎結構
Microsoft 有一個已通過認證的 SAP HANA 相應放大組態的 M 系列 VM SKU。 VM 類型 M128s 已通過認證，最多 16 個節點相應放大。 如需在 Azure Vm 上的 SAP HANA 向外延展認證變更，請參閱[認證 IaaS 平台清單](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。

用來部署向外延展組態，在 Azure Vm 中的最小 OS 版本是：

- SUSE Linux 12 SP3。
- Red Hat Linux 7.4。

16 節點向外延展認證：

- 一個節點是主要節點。
- 最多 15 個節點都是背景工作節點。

>[!NOTE]
>在 Azure VM 向外延展部署中，不可能使用待命節點。
>

有兩個原因為何，您無法設定待命節點：

- Azure 目前沒有任何原生的 NFS 服務。 如此一來，必須設定 NFS 共用的協力廠商功能協助。
- 無第三方 NFS 組態可滿足儲存體延遲準則適用於 SAP HANA，與他們在 Azure 上部署的解決方案。

如此一來，針對 /hana/data 和 /hana/log 磁碟區不能共用。 不共用這些磁碟區的單一節點時，會防止使用向外延展組態中的 SAP HANA 待命節點。

下圖顯示在向外延展組態中的單一節點的基本設計：

![單一節點的相應放大基本概念](media/hana-vm-operations/scale-out-basics.PNG)

SAP HANA 相應放大的 VM 節點基本設定看起來像這樣：

- /Hana/shared，為您建置高可用性的 NFS 叢集根據 SUSE Linux 12 SP3。 此叢集中裝載 /hana/shared NFS 共用您的向外延展組態和 SAP NetWeaver 或 BW/4HANA 中央服務。 如需如何建置這類組態資訊，請參閱[SUSE Linux Enterprise Server 上的 Azure Vm 上 nfs 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)。
- 所有其他的磁碟區的不同節點之間不共用，並不根據 NFS。 稍後這篇文章會提供安裝組態和使用非共用的 /hana/data 和 /hana/log 的向外延展 HANA 安裝的步驟。

>[!NOTE]
>如圖中所示的高可用性 NFS 叢集，目前僅支援搭配 SUSE Linux 使用。 以 Red Hat 為基礎的高可用性 NFS 解決方案，日後才建議使用。

調整大小的磁碟區的節點是向上延展，除了 /hana/shared 一樣。 下表會顯示建議的大小和類型 M128s VM sku。

| VM SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data | /hana/log | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


請檢查不同的建議磁碟區的儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更高的磁碟區為 /hana/data 和 /hana/log，增加 Azure 進階儲存體 Vhd 的數目。 調整大小的更多的 vhd，比列出增加 IOPS 的磁碟區和 Azure 虛擬機器類型的限制內的 I/O 輸送量。 也適用於組成 /hana/log 磁碟區的磁碟的寫入加速器。
 

定義的公式，向外延展 hana/共用磁碟區大小為每四個背景工作角色節點的單一背景工作節點的記憶體大小，請參閱 < [SAP HANA TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)。

假設您需要 SAP HANA 向外延展認證的 M128s Azure VM 中使用約 2 TB 的記憶體時，SAP 建議會摘要如下：

- 一個主要節點和最多四個背景工作角色節點，/hana/shared 磁碟區大小為 2 TB。
- 一個主要節點和五個和 8 個背景工作角色節點，/hana/shared 磁碟區大小是 4 TB。
- 一個主要節點和 9 至 12 個背景工作節點，/hana/shared 磁碟區大小會是 6 TB。
- 一個主要節點和 12 至 15 背景工作角色節點，/hana/shared 磁碟區大小是 8 TB。

其他重要的設計中的向外延展的 SAP HANA VM 的單一節點組態的圖形顯示的是含有子網路組態的虛擬網路。 SAP 強烈建議從 HANA 節點間通訊的用戶端和應用程式向流量分隔開來。 

為了達成此目標，請將兩個不同的虛擬 Nic 附加至 VM，圖形中所示。 這兩個虛擬 Nic 位於不同子網路，並有兩個不同的 IP 位址。 然後，您會控制的路由規則的流量使用 Nsg 或使用者定義的路由。

特別是在 Azure 中，有任何的方法和方法，以強制執行服務和配額的特定虛擬 Nic 的品質。 如此一來，面對用戶端和應用程式和內部節點通訊的區隔未開啟任何優先順序一個流量資料流之間進行的機會。 相反地，區隔會保持在防護的向外延展組態的內部節點通訊的安全性。  

>[!IMPORTANT]
>SAP 強烈建議您區隔的用戶端和應用程式和內部節點流量這篇文章中所述的網路流量。 建議您將架構放就地，先前的圖形中所示。
>

下圖顯示從網路的觀點來看的最小所需的網路架構：

![單一節點的相應放大基本概念](media/hana-vm-operations/scale-out-networking-overview.PNG)

目前支援的限制是 15 個背景工作節點，除了一個主要節點。

下圖顯示從儲存體觀點來看的儲存體架構：


![單一節點的相應放大基本概念](media/hana-vm-operations/scale-out-storage-overview.PNG)

/Hana/shared 磁碟區位於高可用性的 NFS 共用設定。 所有其他的磁碟機在本機掛接到個別的 Vm。 

### <a name="highly-available-nfs-share"></a>高可用性 NFS 共用
到目前為止，高可用性的 NFS 叢集使用 SUSE Linux 只。 安裝程式的資訊，請參閱[SUSE Linux Enterprise Server 上的 Azure Vm 上 nfs 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)。 如果您不共用之 NFS 叢集以執行 SAP HANA 執行個體的 Azure 虛擬網路外部的任何其他 HANA 組態，請將它安裝在相同的虛擬網路。 將它安裝在自己的子網路，並確定可以存取並不是所有的任意流量子網路。 相反地，限制執行 /hana/shared 磁碟區流量的 IP 位址的 VM 子網路的流量。

關於 /hana/shared 流量路由傳送的 HANA 向外延展 VM 虛擬 NIC，建議事項為：

- 因為 /hana/shared 流量為一般程度，請透過虛擬 NIC 指派給用戶端網路中的最小設定路由。
- 最後，針對至 hana/共用流量，部署在您用來部署 SAP HANA 相應放大組態的虛擬網路中的第三個子網路。 指派子網路中裝載的第三個虛擬 NIC。 使用第三個虛擬 NIC 和相關聯的 IP 位址的流量到 NFS 共用。 接著，您可以套用不同的存取權和路由規則。

>[!IMPORTANT]
>可能透過路由傳送網路流量之間具有 SAP HANA 的方式向外延展部署的 Vm 和任何情況下的高可用性 NFS[網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)或類似的虛擬設備。 Azure Nsg 都沒有這類裝置。 請檢查您的路由規則，以確定網路虛擬設備或類似的虛擬設備，會繞道當他們存取的高可用性的 NFS 共用從執行 SAP HANA 的 Vm。
> 

如果您想要共用之 SAP HANA 組態之間的高可用性 NFS 叢集，請將所有這些 HANA 組態移至相同的虛擬網路。 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>在 Azure 中安裝 SAP HANA 向外延展
若要安裝相應放大 SAP 組態，請遵循下列一般步驟：

- 部署新的或新的 Azure 虛擬網路基礎結構。
- 使用 Azure 受管理的進階儲存體磁碟區，以部署新的 Vm。
- 部署新的或調整現有的高可用性 NFS 叢集。
- 調整網路路由，以確定，比方說，Vm 之間的內部節點通訊不透過路由傳送[網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)。 這也適用於 Vm 和高可用性的 NFS 叢集之間的流量。
- 安裝 SAP HANA 主要節點。
- 調整 SAP HANA 主要節點的組態參數。
- 繼續安裝 SAP HANA 背景工作角色節點。

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>在向外延展組態中安裝 SAP HANA
部署您的 Azure VM 基礎結構，並完成所有其他的準備工作。 現在，若要安裝 SAP HANA 相應放大組態，請遵循下列步驟：

- 安裝 SAP HANA 主要節點，根據 SAP 的文件。
- *安裝之後，變更 global.ini 檔案，並將參數新增到 'basepath_shared = 否' 至 global.ini*。 此參數可讓在節點之間向外延展而不需要共用的 /hana/data 和 /hana/log 磁碟區中執行的 SAP HANA。 如需詳細資訊，請參閱 < [SAP 附註 #2080991](https://launchpad.support.sap.com/#/notes/2080991)。
- 您將 global.ini 參數變更之後，重新啟動 SAP HANA 執行個體。
- 新增其他背景工作角色節點。 如需詳細資訊，請參閱 < [SAP HANA 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html)。 指定在安裝期間或之後使用，比方說，本機 hdblcm，SAP HANA 節點間通訊的內部網路。 如需詳細資訊，請參閱 < [SAP 附註 #2183363](https://launchpad.support.sap.com/#/notes/2183363)。 

遵循此安裝程式常式中，您已安裝相應放大組態會使用非共用的磁碟執行 /hana/data 和 /hana/log。 Hana/共用磁碟區以放置在高可用性 NFS 共用。


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Azure 虛擬機器的 SAP HANA 動態分層 2.0

除了 Azure M 系列 Vm 上的 SAP HANA 認證，Microsoft Azure 上也支援 SAP HANA 動態分層 2.0 (DT 2.0)。 如需 SAP HANA 動態分層文件的連結，請參閱"連結至 DT 2.0 文件 」 在本文稍後。 在安裝產品，或操作，例如，透過在 Azure VM 的 SAP HANA Cockpit 中沒有差異，但則必須在 Azure 上的官方支援有一些重要的項目。 下列各節說明這些關鍵點。 

SAP HANA DT 2.0 不支援 SAP BW 或 S4HANA。 主要使用案例現在是原生的 HANA 應用程式。


### <a name="overview"></a>概觀

下圖概述 DT 2.0 支援的 Microsoft Azure 上。 請遵循這些必要的需求，來符合正式認證：

- DT 2.0 必須安裝在專用的 Azure VM 上。 它可能無法執行相同的 VM 執行 SAP HANA 上。
- SAP HANA 和 DT 2.0 Vm 必須部署在相同的 Azure 虛擬網路內。
- SAP HANA 和 DT 2.0 Vm 必須部署使用 Azure 加速網路已啟用。
- DT 2.0 Vm 的儲存體類型必須是 Azure 進階儲存體。
- 多個 Azure 磁碟必須附加至 DT 2.0 VM。
- 建立軟體 RAID 或等量磁碟區，透過 LVM 或 mdadm，需要跨 Azure 磁碟使用等量。

下列各節提供詳細的說明。

![SAP HANA DT 2.0 架構概觀](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 專用的 Azure VM

Azure IaaS 上 DT 2.0 支援只在專用 VM 上。 DT 2.0 不允許在相同的 HANA 執行個體執行所在的 Azure VM 上執行。 一開始，可以用兩個 VM 類型，來執行 SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

如需 VM 類型的描述，請參閱[記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)。

提供基本概念的 DT 2.0 時，也就是有關卸載暖資料以節省成本，合理使用對應的 VM 大小。 沒有任何嚴格的規則，如需可能的組合。 這取決於特定客戶工作負載。

下表顯示建議的設定。

| SAP HANA VM 類型 | DT 2.0 VM 類型 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


使用支援的 DT 2.0 Vm，例如 M64 32ms 和 E32sv3，SAP HANA 認證 M 系列虛擬機器的所有組合都都有可能的。


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure 網路功能與 SAP HANA DT 2.0

在專用的 VM 上安裝 DT 2.0 需要 DT 2.0 VM 與 SAP HANA VM，使用 10 GB 的最小值之間的網路輸送量。 如此一來，就一定要放在相同的 Azure 虛擬網路內的所有 Vm，並啟用 Azure 加速網路。

如需有關 Azure 加速網路的詳細資訊，請參閱 <<c0> [ 建立有加速網路的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

### <a name="vm-storage-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 的 VM 儲存體

DT 2.0 最佳作法指引，根據最小的磁碟 IO 輸送量會是 50 MB/秒，每個實體核心。 查看 DT 2.0 支援的兩個 Azure VM 類型的規格是最大的磁碟 IO 輸送量限制的 vm:

- **E32sv3**: 768 MB/秒，取消快取，這表示 48 MB/秒，每個實體核心的比例
- **M64 32ms**:1000 MB/秒，取消快取，這表示 62.5 MB/秒，每個實體核心的比例

就必須將多個 Azure 磁碟附加至 DT 2.0 VM 及建立軟體 RAID 在 OS 層級使用等量，達到每部 VM 的磁碟輸送量最大限制。 單一的 Azure 磁碟就不能達到的最大的 VM 限制的輸送量。 Azure 進階儲存體，才能執行 DT 2.0。 

- 如需可用的 Azure 磁碟類型的詳細資訊，請參閱 <<c0> [ 選取為 Azure IaaS Windows Vm 的磁碟類型](../../windows/disks-types.md)。
- 如需如何建立透過 mdadm 的軟體 RAID 的詳細資訊，請參閱[Linux 上設定軟體 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)。
- 如需如何設定 LVM 建立等量磁碟區的最大輸送量的詳細資訊，請參閱[在 Azure 中 Linux VM 上設定 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)。

根據大小需求，有不同的選項，以連線到 VM 的最大輸送量。 以下是可能的資料磁碟區磁碟組態，可讓每個 DT 2.0 VM 類型達到 VM 輸送量上限。 E32sv3 VM 會被視為較小的工作負載的項目層級。 如果不是速度不夠快，可能必須調整 M64 32ms VM 的大小。

因為 M64 32ms VM 有大量的記憶體，IO 負載可能無法達到此限制，特別是針對讀取密集的工作負載。 根據客戶特定的工作負載可能足以使用較少的磁碟等量磁碟區集合中。 為了安全起見，下列磁碟設定被選為保證的最大輸送量。


| VM SKU | 磁碟設定 1 | 磁碟設定 2 | 磁碟組態 3 | 磁碟組態 4 | 磁碟設定為 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


特別是如果工作負載是讀取密集，開啟 Azure 主機快取的 「 唯讀 」 設定，對於資料磁碟區的資料庫軟體的建議可能會提高 IO 效能。 針對交易記錄檔中，Azure 主機磁碟快取必須是"none"。 

我們建議您針對記錄檔磁碟區大小的起始點是資料大小的 15%的啟發學習法。 若要建立的記錄磁碟區，使用不同的 Azure 磁碟類型，根據成本和輸送量需求。 高 I/O 輸送量的記錄磁碟區，則需要。 

如果您使用的 VM 輸入 M64 32ms，我們建議您啟用[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)。 寫入加速器是 Azure 的功能，可提供最佳的磁碟寫入延遲，交易記錄檔。 它是僅適用於 M 系列。 有一些要考慮，例如每個 VM 類型的磁碟數目上限的項目。 如需有關寫入加速器的詳細資訊，請參閱[啟用寫入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


下表顯示一些範例，以協助您調整記錄磁碟區大小。

| 資料磁碟區大小和磁碟類型 | 記錄磁碟區和磁碟類型設定 1 | 記錄磁碟區和磁碟類型設定 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


類似於 SAP HANA 向外延展，/hana/shared 目錄必須共用 SAP HANA VM 和 DT 2.0 VM 之間。 我們建議您透過使用專用的 Vm，使其做為高可用性的 NFS 伺服器使用相同的架構，與 SAP HANA 向外延展。 若要提供共用的備份磁碟區，使用相同的設計。 但它已啟動，您可以決定需要高可用性時，或者它是否足以使用足夠的儲存容量中的專用的 VM，做為備份伺服器。



### <a name="links-to-dt-20-documentation"></a>DT 2.0 文件的連結 

- [SAP HANA 的動態分層安裝與更新指南](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA 動態分層教學課程和資源](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA 動態分層的概念證明](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>在 Azure VM 上部署 SAP HANA 的作業
下列各節說明關於在 Azure VM 上部署 SAP HANA 系統的一些作業。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 上的備份和還原作業
下列文件說明如何備份及還原您的 SAP HANA 部署：

- [SAP HANA 備份概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 檔案層級備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP Hana 儲存體快照集基準](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>啟動及重新啟動包含 SAP HANA 的 VM
Azure 公用雲端的重要功能是您僅需支付運算的分鐘數。 例如，當您關閉執行 SAP HANA 的 VM 時，您只需要支付儲存體成本在這段期間。 當您在初始部署時指定 VM 的靜態 IP 位址，就可使用另一項功能。 當您重新啟動具有 SAP HANA 的 VM 時，VM 會使用其先前的 IP 位址來重新啟動。 


### <a name="use-saprouter-for-sap-remote-support"></a>使用 SAPRouter 以取得 SAP 遠端支援
如果您有內部部署位置與 Azure 之間的站對站連線，而且您正在執行的 SAP 元件，您可能已在執行 SAProuter。 在此情況下，請遵循下列步驟進行遠端支援：

- 維護在 SAProuter 組態中裝載 SAP HANA 之 VM 的私用和靜態 IP 位址。
- 設定裝載 HANA VM 的子網路之 NSG，以允許透過 TCP/IP 通訊埠 3299 的流量。

如果您連接至 Azure，透過網際網路，而且您沒有 SAP 路由器的 SAP HANA 的 VM 使用，安裝此元件。 在管理子網路中不同的 VM 上安裝 SAProuter 安裝。 

下圖顯示不需要透過站對站連線、而需要 SAProuter 部署 SAP HANA 的概略結構描述：

![不具站台對站台連線能力和 SAProuter 的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking3.PNG)

請務必在不同的 VM，而不是在 JumpBox VM 上安裝 SAProuter 安裝。 不同的 VM 必須有靜態 IP 位址。 若要連接您的 SAProuter 由 SAP 裝載的 SAProuter，請連絡 SAP 以 IP 位址。 由 SAP 裝載的 SAProuter 是您在 VM 上安裝的 SAProuter 執行個體的對應項目。 使用 SAP 提供的 IP 位址來設定 SAProuter 執行個體。 在組態集中，唯一必要的連接埠是 TCP 通訊埠 3299。

如需有關如何設定和維護透過 SAProuter 的遠端連線的詳細資訊，請參閱 < [SAP 文件](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>使用 Azure 的原生 Vm 上的 SAP HANA 的高可用性
如果您執行 SUSE Linux Enterprise Server for SAP Applications 12 SP1 或更新版本時，您可以使用 STONITH 裝置建立 Pacemaker 叢集。 若要設定使用包含 HANA 系統複寫以及自動容錯移轉的同步複寫的 SAP HANA 組態中使用的裝置。 如需有關安裝程序的詳細資訊，請參閱[適用於 Azure 的虛擬機器的 SAP HANA 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
