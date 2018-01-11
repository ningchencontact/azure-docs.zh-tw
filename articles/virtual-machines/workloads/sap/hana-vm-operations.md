---
title: "在 Azure 上的 SAP HANA 作業 |Microsoft 文件"
description: "操作指南適用於 SAP HANA 系統部署在 Azure 虛擬機器上。"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure 上的 SAP Hana 作業指南
本文件提供作業系統部署在 Azure 的原生虛擬機器 (Vm) 上的 SAP HANA 的指引。 這份文件並非要取代的標準 SAP 文件，其中包含下列內容：

- [SAP 系統管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安裝指南](https://service.sap.com/instguides)
- [SAP 附註](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>必要條件
若要使用本指南，您需要下列 Azure 元件的基本知識：

- [Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 網路和虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 儲存體](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

若要了解有關 SAP NetWeaver 和其他 SAP 元件在 Azure 上的詳細資訊，請參閱[在 Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)區段[Azure 文件](https://docs.microsoft.com/azure/)。

## <a name="basic-setup-considerations"></a>基本設定考量
下列各節說明部署 Azure Vm 上的 SAP HANA 系統的基本安裝考量。

### <a name="connect-into-azure-virtual-machines"></a>連接到 Azure 虛擬機器
如中所述[規劃指南 》 的 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)，有兩種基本的方法，以連接到 Azure Vm:

- 透過網際網路與跳 VM 上的公用端點連線，或是在 VM 上執行 SAP HANA。
- 透過連線[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)。

生產案例，必須透過 VPN 或 ExpressRoute 的站對站連線能力。 這種類型的連接也需要針對非生產案例饋送至生產案例中使用 SAP 軟體的位置。 下圖顯示跨站台連線能力的範例：

![跨站台連線能力](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>選擇 Azure VM 類型
可以用於生產案例中的 Azure VM 類型會列在[IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 非生產案例中，較多的原生的 Azure VM 類型可使用。

>[!NOTE]
>針對非生產案例中，使用所述的 VM 類型[SAP note #1928533](https://launchpad.support.sap.com/#/notes/1928533)。

使用部署在 Azure 中的 Vm:

- Azure 入口網站。
- Azure PowerShell cmdlet。
- Azure CLI。

您也可以部署的完整安裝的 SAP HANA 平台上的 Azure VM 服務，透過[SAP 雲端平台](https://cal.sap.com/)。 安裝程序所述[部署 SAP S/4HANA 或在 Azure 上的 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。

### <a name="choose-azure-storage-type"></a>選擇 Azure 儲存體類型
Azure 提供儲存空間適合於 Azure Vm 執行 SAP HANA 的兩種的類型：

- [Azure 標準儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure 進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure 針對 Azure 標準儲存體和 Azure 進階儲存體上的 VHD，提供兩種部署方法。 如果允許整體案例，利用[Azure 受管理磁碟](https://azure.microsoft.com/services/managed-disks/)部署。

如需儲存體類型和其 Sla 的清單，檢閱[Azure 文件的受管理的磁碟](https://azure.microsoft.com/pricing/details/managed-disks/)。

/Hana/data 和 /hana/log 磁碟區，建議您使用 azure 高階磁碟。 您可以透過多個進階儲存體磁碟建置 LVM RAID，並使用的 RAID 磁碟區為 /hana/data 和 /hana/log 磁碟區。

下表顯示裝載在 Azure Vm 上的 SAP HANA 的客戶經常使用的 VM 類型的設定：

| VM SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data 和 /hana/log<br /> 與 LVM 或 MDADM 等量 | HANA/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GB | 384 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 giB | 768 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 giB | 1200 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 giB | 2000 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 giB | 1000 GB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 giB | 1000 GB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 giB | 2000 GB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 giB | 2000 GB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> 建議使用較小的 VM 類型 3 x P20 oversize 有關根據的空間建議的磁碟區的磁碟[SAP TDI 存放裝置的白皮書](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)。 不過選擇顯示在資料表中已對 SAP hana 提供足夠的磁碟輸送量。 如果您需要較少的 I/O 輸送量時，您可以調整 /hana/data 和 /hana/log Premium 儲存體磁碟的選擇。 也是如此 /hana/backup 磁碟區的大小保持代表兩次記憶體磁碟區的備份大小。 如果您需要較少的空間，然後您可以調整。 也請注意，當調整大小或 vm 決定整體 VM I/O 輸送量。 VM 輸送量記載於文件的整體[記憶體最佳化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)  

> [!NOTE]
> 如果您想要受益於[Azure 虛擬機器的單一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)您需要變更會以標準儲存體 (Sxx) 至進階儲存體 (Pxx) 列出的所有 Vhd。 


### <a name="set-up-azure-virtual-networks"></a>設定 Azure 虛擬網路
當您有站對站連線至 Azure，透過 VPN 或 ExpressRoute 時，您必須至少有一個[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)，其會透過 VPN 或 ExpressRoute 電路虛擬閘道連線。 虛擬閘道存在於 Azure 虛擬網路中的子網路。 若要安裝 SAP HANA，您可以建立兩個虛擬網路內的其他子網路。 一個子網路中裝載的 Vm 執行 SAP HANA 執行個體。 Jumpbox 或管理 Vm 裝載 SAP HANA Studio 或其他管理軟體，則會執行其他子網路。

當您安裝的 Vm 執行 SAP HANA 時，Vm 將會需要：

- 安裝兩個虛擬 Nic： 一個 NIC 連線到管理子網路，並從內部部署網路或其他網路中，連接到 Azure VM 中的 SAP HANA 執行個體的一個 NIC。
- 靜態私人 IP 位址部署這兩個虛擬 nic。

如需指派 IP 位址的不同方法的概觀，請參閱[IP 位址類型和在 Azure 中的配置方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 

[Azure 網路安全性群組 (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)用來將流量路由傳送至 SAP HANA 執行個體或 Jumpbox 導向。 SAP HANA 子網路和管理子網路關聯 Nsg。

下圖顯示 SAP HANA 粗略部署結構描述的概觀：

![SAP Hana 粗略部署結構描述](media/hana-vm-operations/hana-simple-networking.PNG)


若要部署在 Azure 中 SAP HANA，不需要透過站對站連線，存取 SAP HANA 的執行個體，但公用 IP 位址。 必須執行 Jumpbox VM 的 Azure vm 指派的 IP 位址。 在這個基本案例中，部署也取決於 Azure 的內建 DNS 服務，以解決主機名稱。 在公開 IP 位址並使用其中一個更複雜的部署，Azure 內建的 DNS 服務是特別重要的。 使用 Azure Nsg 限制開啟的連接埠或 IP 位址範圍可連線至 Azure 的子網路有公開 IP 位址的資產。 下圖顯示概略的結構描述部署 SAP HANA 不需要透過站對站連線：
  
![SAP hana 粗略部署結構描述，但沒有站對站連接](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>部署 Azure Vm 上的 SAP HANA 的作業
下列各節說明一些與部署在 Azure Vm 上的 SAP HANA 系統相關的作業。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>備份和還原 Azure Vm 上的作業
下列文件說明如何備份和還原您的 SAP HANA 部署：

- [SAP HANA 備份概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 檔案層級備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 儲存快照集進行效能評定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>啟動和重新啟動 Vm 包含 SAP HANA
Azure 公用雲端的重要功能是收費僅適用於您電腦的分鐘。 比方說，當您關閉 VM 執行 SAP HANA，您要支付只儲存體成本在這段時間。 您的 Vm 指定靜態 IP 位址在初始部署時，使用另一項功能。 當您重新啟動 VM 時，SAP HANA 時，在 VM 重新啟動其先前的 IP 位址。 


### <a name="use-saprouter-for-sap-remote-support"></a>SAProuter 用於 SAP 遠端支援
如果您有站對站連線在內部部署位置與 Azure 中，而且您執行 SAP 元件，則您可能已經在執行 SAProuter。 在此情況下，完成遠端支援下列項目：

- 會維護主控 SAP HANA SAProuter 組態中的 VM 的私用和靜態 IP 位址。
- 設定裝載 HANA VM 允許透過 TCP/IP 連接埠 3299 流量子網路的 NSG。

如果您要連線到網際網路，透過 Azure vm 與 SAP HANA 沒有 SAP 路由器，您需要安裝此元件。 管理子網路中的不同 VM 中安裝 SAProuter。 下圖顯示概略的結構描述部署 SAP HANA，不需要透過站對站連線與 SAProuter:

![SAP hana 粗略部署結構描述，而不需要站對站連接和 SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

請務必安裝 SAProuter 不同 VM 中，而不是在 Jumpbox VM。 不同 VM 必須有靜態 IP 位址。 若要連接您 SAProuter SAProuter SAP 所裝載，請連絡的 IP 位址的 SAP。 （由 SAP SAProuter 是您在 VM 上安裝的 SAProuter 執行個體的對應項目）。用於從 SAP 的 IP 位址設定 SAProuter 執行個體。 在組態設定中，只需要連接埠是 TCP 連接埠 3299。

如需有關如何設定及維護透過 SAProuter 的遠端連線的詳細資訊，請參閱[SAP 文件集](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>高可用性與原生的 Azure Vm 上的 SAP HANA
如果您執行 SUSE Linux 12 SP1 或更新版本中，您可以建立 Pacemaker 叢集與 STONITH 裝置。 您可以使用裝置設定與 HANA 系統複寫以及自動容錯移轉使用同步複寫的 SAP HANA 設定。 如需安裝程序的詳細資訊，請參閱[Azure 虛擬機器上的高可用性的 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)。
