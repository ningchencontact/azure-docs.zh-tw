---
title: Azure 上的 SAP Hana 作業 | Microsoft Docs
description: 部署在 Azure 虛擬機器上之 SAP HANA 系統的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 959a483d293caa45180c946e92ac824fc56db084
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195034"
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure 上的 SAP Hana 作業指南
本文件提供已部署在 Azure 原生虛擬機器 (VM) 上之 SAP Hana 系統的作業指引。 這份文件並非用以取代標準 SAP 文件，包含下列內容：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安裝指南](https://service.sap.com/instguides)
- [SAP 附註](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>先決條件
若要使用本指南，您需要下列 Azure 元件的基本知識：

- [Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 網路和虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 儲存體](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

若要了解有關 SAP NetWeaver 及 Azure 上其他 SAP 元件的詳細資訊，請參閱 [Azure 文件](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。

## <a name="basic-setup-considerations"></a>基本設定考量
下列各節說明在 Azure VM 上部署 SAP HANA 系統的基本安裝考量。

### <a name="connect-into-azure-virtual-machines"></a>連線到 Azure 虛擬機器
如 [Azure 虛擬機器規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中所述，有兩種基本方法可連線到 Azure VM：

- 透過網際網路和跳接 VM 上的公用端點進行連線，或是在執行 SAP Hana 的 VM 上進行連線。
- 透過 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 進行連線。

生產案例需要透過 VPN 或 ExpressRoute 的站對站連線能力。 饋送至使用 SAP 軟體之生產案例中的非生產案例也需要這種類型的連線。 下圖顯示跨站台連線能力的範例：

![跨站台連線能力](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>選擇 Azure VM 類型
可以用於生產案例的 Azure VM 類型會列在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 針對非實際執行案例，有更多種類的原生 Azure VM 類型可供使用。

>[!NOTE]
> 針對非生產案例，請使用 [SAP 附註 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中所列的 VM 類型。 如需了解適用於生產案例的 Azure 虛擬機器使用方式，請在 SAP 發佈的[認證 IaaS 平台清單](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看經 SAP HANA 認證的虛擬機器。

使用下列方式在 Azure 中部署 VM：

- Azure 入口網站。
- Azure PowerShell Cmdlet。
- Azure CLI。

您也可以透過 [SAP Cloud Platform](https://cal.sap.com/)，將完整安裝的 SAP Hana 平台部署到 Azure VM 服務上。 [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) 中會敘述安裝程序，或者使用[這裡](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)發行的自動化安裝。

### <a name="choose-azure-storage-type"></a>選擇 Azure 儲存體類型
Azure 針對執行 SAP Hana 的 Azure VM，提供兩種儲存體類型：

- [Azure 標準儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure 進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 在整體情況允許的情況下，請利用 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)部署。

如需儲存體類型清單及其在 IOPS 和儲存體輸送量中的 SLA ，請檢閱[受控磁碟的 Azure 文件](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>設定 Azure 虛擬機器的儲存體

目前您購買的 SAP HANA 設備是用於內部部署，所以您不需要在意 I/O 子系統及其功能，原因是設備廠商必須確認最低儲存體需求符合 SAP HANA。 當您自行建置 Azure 基礎結構時，您也應該對這些需求有些了解，以了解我們在下列章節中建議的組態需求。 或者，針對您要設定虛擬機器來執行 SAP HANA 的情況。 要求的某些特性會導致以下需求：

- 在 /hana/log 上啟用 250MB/秒、最少具有 1 MB I/O 大小的讀取/寫入磁碟區
- 針對 /hana/data 啟用至少 400MB/秒、16MB 和 64MB I/O 大小的讀取活動
- 針對 /hana/data 啟用至少 250MB/秒、16MB 和 64MB I/O 大小的寫入活動

指定低儲存體延遲對於 DBMS 系統相當重要，即使是像 SAP HANA 將資料保存在記憶體內部也一樣。 儲存體中的關鍵路徑通常是在 DBMS 系統之交易記錄寫入的周圍。 但像是寫入儲存點或是在損毀復原之後載入記憶體內部資料之類的作業也很重要。 因此，針對 /hana/data 和 /hana/log 磁碟區必須使用 Azure 進階磁碟。 為了達到如 SAP 想要的 /hana/log 和 /hana/data 最小輸送量，您必須使用 MDADM 或 LVM 在多個 Azure 進階儲存體磁碟上建置 RAID 0，然後使用 RAID 磁碟區作為 /hana/data 和 /hana/log 磁碟區。 對於 RAID 0 的等量磁碟大小，建議使用：

- 針對 /hana/data 使用 64K 或 128K
- 針對 /hana/log 使用 32K

> [!NOTE]
> 您不需要使用 RAID 磁碟區設定任何備援層級，因為 Azure 進階和標準儲存體會保存三個 VHD 的映像。 RAID 磁碟區的使用方式單純是用來設定會提供足夠 I/O 輸送量的磁碟區。

RAID 下的 Azure VHD 數目累計，是從 IOPS 和儲存體輸送量端累計。 因此，如果您將 RAID 0 放在 3 x P30 Azure 進階儲存體磁碟上，它應該會給您單一 Azure 進階儲存體 P30 磁碟的 3 倍 IOPS 和 3 倍儲存體輸送量。

請勿在用於 /hana/data 和 /hana/log 的磁碟上設定進階儲存體快取。 建置這些磁碟區的所有磁碟應該將這些磁碟的快取設定為「無」。

當調整 VM 大小或決定 VM 時，也請注意整體 VM I/O 輸送量。 [記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)一文中說明整體虛擬機器儲存體輸送量。

#### <a name="cost-conscious-azure-storage-configuration"></a>節省成本的 Azure 儲存體組態
下表顯示客戶經常用來在 Azure 虛擬機器上裝載 SAP HANA 的虛擬機器類型組態。 可能有部分虛擬機器類型無法符合 SAP HANA 的所有最小準則。 但是目前這些虛擬機器似乎可以針對非生產案例正常執行。 

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。



| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要對 /hana/backup 磁碟區 (已調整大小來保持備份為記憶體磁碟區的兩倍) 進行變更，您可以自由調整。   
請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 /hana/data 和 /hana/log 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。 

> [!NOTE]
> 上述組態「不會」受益於 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，因為它是使用 Azure 進階儲存體和 Azure 標準儲存體的混合。 不過，為了最佳化成本而選擇了這個選項。


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>符合單一虛擬機器 SLA 而獲益的 Azure 儲存體組態
如果您想要從 [Azure 虛擬機器的單一虛擬機器 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) 獲益，您必須單獨使用 Azure 進階儲存體 VHD。

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GB | 768 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GB | 384 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


根據 [SAP TDI 儲存體白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，包含 3 個 P20 的較小 VM 類型所建議使用的磁碟，在空間建議方面超過磁碟區大小。 不過，已如資料表中顯示進行選擇，為 SAP Hana 提供足夠的磁碟輸送量。 如果您需要對 /hana/backup 磁碟區 (已調整大小來保持備份為記憶體磁碟區的兩倍) 進行變更，您可以自由調整。  
請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 /hana/data 和 /hana/log 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>儲存體解決方案，使用適用於 Azure M 系列虛擬機器的 Azure Write Accelerator
Azure Write Accelerator 是專門為 M 系列虛擬機器推出的功能。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 針對 SAP HANA，Write Accelerator 只支援用於 /hana/log 磁碟區。 因此目前顯示的組態需要變更。 主要變更是 /hana/data 與 /hana/log 之間的備份，以便只針對 /hana/log 磁碟區使用 Azure Write Accelerator。 

> [!IMPORTANT]
> Azure M 系列虛擬機器的 SAP HANA 憑證是用於 /hana/log 磁碟區的 Azure Write Accelerator 所專用。 因此，在 Azure M 系列虛擬機器上部署生產案例的 SAP HANA 時，預期會針對 /hana/log 磁碟區使用 Azure Write Accelerator 進行設定。  

> [!NOTE]
> 針對生產案例，請在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中檢查 SAP 是否有支援 SAP HANA 的特定虛擬機器類型。

建議的組態看起來如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | /hana/log | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

請針對不同的建議磁碟區，檢查儲存體輸送量是否符合您想要執行的工作負載。 如果工作負載需要更多 /hana/data 和 /hana/log 磁碟區，您需要增加 Azure 進階儲存體 VHD 的數目。 使用比列出數目更多的 VHD 來調整磁碟區大小，會增加在 Azure 虛擬機器類型限制內的 IOPS 和 I/O 輸送量。

Azure Write Accelerator 只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 所以至少必須將組成 /hana/log 磁碟區的 Azure 進階儲存體磁碟部署為受控磁碟。

Azure Write Accelerator 可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其限制。 目前的上限是：

- M128xx VM 上限 16 個 VHD
- M64xx VM 上限 8 個 VHD

如需如何啟用 Azure Write Accelerator 的詳細指示，可以在[寫入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)一文中找到。

Azure Write Accelerator 的詳細資料和限制可以在相同文件中找到。


### <a name="set-up-azure-virtual-networks"></a>設定 Azure 虛擬網路
當您透過 VPN 或 ExpressRoute 對 Azure 站對站連線時，必須至少有一個 [Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)，可透過虛擬閘道連線到 VPN 或 ExpressRoute 線路。 虛擬閘道存在於 Azure 虛擬網路的子網路中。 若要安裝 SAP HANA，您可以在虛擬網路內建立兩個其他的子網路。 一個子網路會裝載執行 SAP HANA 執行個體的 VM。 另一個子網路會執行裝載 SAP HANA Studio 或其他管理軟體的 Jumpbox 或管理 VM。

當您安裝執行 SAP HANA 的 VM 時，VM 將會需要：

- 已安裝兩個虛擬 NIC：其中一個 NIC 會連線到管理子網路，另一個 NIC 則會從內部部署網路或其他網路，連線到 Azure VM 中的 SAP Hana 執行個體。
- 已針對這兩個虛擬 NIC 部署的靜態私人 IP 位址。

如需以不同方法指派 IP 位址的概觀，請參閱 [Azure 中的 IP 位址類型及配置方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 

針對執行 SAP HANA 的虛擬機器，您應該使用指派的靜態 IP 位址。 原因是 HANA 的某些組態屬性會參考 IP 位址。

[Azure 網路安全性群組 (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 是用來將路由傳送至 SAP HANA 執行個體或 Jumpbox 的流量進行導向。 NSG 是與 SAP HANA 子網路和管理子網路相關聯。

下圖顯示 SAP HANA 粗略部署結構描述的概觀：

![SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking.PNG)


若要在不具站台對站台連線能力的情況下，於 Azure 中部署 SAP HANA，請透過公用 IP 位址存取 SAP HANA 執行個體。 IP 位址必須指派給執行 Jumpbox VM 的 Azure VM。 在這個基本案例中，部署會仰賴 Azure 的內建 DNS 服務來解析主機名稱。 在使用公眾對應 IP 位址的較複雜部署中，Azure 內建的 DNS 服務特別重要。 使用 Azure NSG 來限制開啟連接埠或 IP 位址範圍，可使用公眾對應 IP 位址的資產連線至 Azure 子網路。 下圖顯示不需要透過站對站連線部署 SAP HANA 的概略結構描述：
  
![不具站台對站台連線能力的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>在 Azure VM 上部署 SAP HANA 的作業
下列各節說明關於在 Azure VM 上部署 SAP HANA 系統的一些作業。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 上的備份和還原作業
下列文件說明如何備份及還原您的 SAP HANA 部署：

- [SAP HANA 備份概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 檔案層級備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP Hana 儲存體快照集基準](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>啟動及重新啟動包含 SAP HANA 的 VM
Azure 公用雲端的重要功能是您僅需支付運算的分鐘數。 例如，當您關閉執行 SAP HANA 的 VM 時，您只需要支付這段時間的儲存體成本。 當您在初始部署時指定 VM 的靜態 IP 位址，就可使用另一項功能。 當您重新啟動具有 SAP HANA 的 VM 時，VM 會使用其先前的 IP 位址來重新啟動。 


### <a name="use-saprouter-for-sap-remote-support"></a>使用 SAPRouter 以取得 SAP 遠端支援
如果您有內部部署位置與 Azure 之間的站對站連線，且您是執行 SAP 元件，則您可能已在執行 SAProuter。 在此情況下，請完成下列項目以取得遠端支援：

- 維護在 SAProuter 組態中裝載 SAP HANA 之 VM 的私用和靜態 IP 位址。
- 設定裝載 HANA VM 的子網路之 NSG，以允許透過 TCP/IP 通訊埠 3299 的流量。

如果您要透過網際網路連線到 Azure，且您沒有 SAP 路由器可供包含 SAP HANA 的 VM 使用，就需要安裝此元件。 在管理子網路中，將 SAProuter 安裝在不同的 VM 中。 下圖顯示不需要透過站對站連線、而需要 SAProuter 部署 SAP HANA 的概略結構描述：

![不具站台對站台連線能力和 SAProuter 的 SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking3.PNG)

請務必在不同的 VM 上安裝 SAPRouter，而非安裝在 Jumpbox VM 上。 不同的 VM 必須有靜態 IP 位址。 若要將您的 SAProuter 連線至 SAP 所裝載的 SAProuter，請連絡 SAP 以索取 IP 位址。 (由 SAP 裝載的 SAProuter 是您在 VM 上安裝之 SAProuter 執行個體的對應項目)。使用 SAP 提供的 IP 位址來設定 SAProuter 執行個體。 在組態集中，唯一必要的連接埠是 TCP 通訊埠 3299。

如需有關如何設定和維護透過 SAProuter 的遠端連線詳細資訊，請參閱 [SAP 文件](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>在 Azure 原生 VM 上具有 SAP Hana 的高可用性
如果您是執行 SUSE Linux 12 SP1 或更新版本，可以使用 STONITH 裝置建立 Pacemaker 叢集。 您可以使用裝置設定 SAP HANA 組態，該組態是使用包含 HANA 系統複寫以及自動容錯移轉的同步複寫。 如需設定程序的詳細資訊，請參閱 [Azure 虛擬機器的 SAP HANA 高可用性指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
