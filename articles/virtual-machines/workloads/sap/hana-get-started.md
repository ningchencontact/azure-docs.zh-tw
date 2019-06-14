---
title: 快速入門：在 Azure 虛擬機器上手動安裝單一執行個體 SAP HANA | Microsoft Docs
description: 在 Azure 虛擬機器上手動安裝單一執行個體 SAP HANA 的快速入門指南
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60204627"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>快速入門：手動安裝單一執行個體在 「 Azure 虛擬機器 」 上的 SAP HANA
## <a name="introduction"></a>簡介
本指南可協助您設定單一執行個體的 SAP HANA 在 「 Azure 虛擬機器 」 上，當您手動安裝 SAP NetWeaver 7.5 和 SAP HANA 1.0 SP12 時。 本指南的重點是如何部署 SAP HANA on Azure。 它不會取代 SAP 文件。 

> [!NOTE]
> 本指南說明如何將 SAP HANA 部署到 Azure VM。 如需如何將 SAP HANA 部署至 HANA 大型執行個體的資訊，請參閱[使用 SAP Azure 虛擬機器上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。
 
## <a name="prerequisites"></a>必要條件
本指南假設您熟悉這類的基礎結構即服務 (IaaS) 基本知識：
 * 如何部署虛擬機器 (Vm) 或虛擬網路透過 Azure 入口網站或 PowerShell。
 * Azure 跨平台命令列介面 (CLI)，其中包括使用 JavaScript Object Notation (JSON) 範本的選項。

本指南也假設您已熟悉：
* SAP HANA 與 SAP NetWeaver，以及如何加以內部部署安裝。
* 如何安裝及操作 SAP HANA，並在 Azure 上的 SAP 應用程式執行個體。
* 下列概念和程序：
   * 規劃 SAP 部署在 Azure 上，其中包括 Azure 虛擬網路規劃和 Azure 儲存體使用量。 請參閱[SAP NetWeaver 的 Azure 虛擬機器上-規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。
   * 部署原則及在 Azure 中部署 VM 的方式。 請參閱[適用於 SAP 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)。
   * SAP NetWeaver ABAP SAP Central Services (ASCS) /SAP Central Services (SCS)，，加入佇列複寫伺服器 (ERS) 在 Azure 上的高可用性。 請參閱 [Azure VM 上的 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)。
   * 詳細說明如何在 Azure 上的 ASCS/SCS 多重 SID 安裝的效率。 請參閱[建立 SAP NetWeaver 多 SID 組態](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)。 
   * Azure 中以 Linux 驅動的 VM 作為基礎執行 SAP NetWeaver 的準則。 請參閱[在 Microsoft Azure SUSE Linux Vm 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)。 本指南提供適用於 Linux Azure Vm 中的特定設定。 它也會提供有關如何正確地將 Azure 儲存體磁碟連結至 Linux Vm。

可以用於生產案例的 Azure VM 類型會列在 [IAAS 的 SAP 文件](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 非商業執行環境的情況下，較多的原生 Azure VM 類型使用。
如需有關 VM 組態和作業的詳細資訊，請參閱 < [SAP HANA 基礎結構組態和 Azure 上的作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
如需 SAP HANA 高可用性，請參閱[SAP HANA 高可用性 Azure 虛擬機器的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。

如果您想要取得之 SAP HANA 執行個體或 S/4HANA 或 BW/4HANA 系統快速部署，請考慮使用[SAP Cloud Appliance Library](https://cal.sap.com)。 您可以找到有關如何部署範例中，透過在 Azure 上的 SAP Cloud Appliance Library 的 S/4HANA 系統中的文件[本指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。 您需要的就是 Azure 訂用帳戶，可以向 SAP Cloud Appliance Library 的 SAP 使用者。

## <a name="additional-resources"></a>其他資源
### <a name="sap-hana-backup"></a>SAP HANA 備份
如需如何備份 Azure Vm 上的 SAP HANA 資料庫的資訊，請參閱：
* [備份指南適用於 SAP HANA 在 「 Azure 虛擬機器 」 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)。
* [在檔案層級上的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)。
* [儲存體快照集為基礎的 SAP HANA 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)。

### <a name="sap-cloud-appliance-library"></a>SAP 雲端應用裝置程式庫
如需如何使用 SAP Cloud Appliance Library S/4HANA 或 BW/4HANA 部署資訊，請參閱[部署 SAP S/4HANA 或 BW/4HANA，Microsoft Azure 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 支援的作業系統
在 SAP HANA 支援作業系統上的資訊，請參閱[SAP 附註 2235581-SAP HANA:支援的作業系統](https://launchpad.support.sap.com/#/notes/2235581/E)。 Azure VM 僅支援這些作業系統的其中一部份。 支援下列作業系統在 Azure 上部署 SAP HANA︰ 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

如需關於 SAP HANA 和不同 Linux 作業系統的其他 SAP 文件，請參閱：

* [SAP 附註 171356:在 Linux 上的 SAP 軟體：一般資訊](https://launchpad.support.sap.com/#/notes/1984787)。
* [SAP 附註 1944799:SLES 作業系統安裝的 SAP HANA 指導方針](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)。
* [SAP Note 2205917:SAP HANA DB 建議作業系統設定 SLES 12 for SAP 應用程式](https://launchpad.support.sap.com/#/notes/2205917/E)。
* [SAP 附註 1391070:Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)。
* [SAP 附註 2009879 適：Red Hat Enterprise Linux (RHEL) 作業系統的 SAP HANA 指導方針](https://launchpad.support.sap.com/#/notes/2009879)。
* [SAP 附註 2292690:SAP HANA DB:建議作業系統設定，適用於 RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)。

### <a name="sap-monitoring-in-azure"></a>Azure 中的 SAP 監視
如需 Azure 中 SAP 監控資訊：

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)討論 SAP 增強的監視與 Azure Linux Vm 上。 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)討論 Linux 上的 SAPOSCOL 相關資訊。 
* [SAP 附註 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)討論適用於 SAP 主要監視計量，在 Microsoft Azure 上。

### <a name="azure-vm-types"></a>Azure VM 類型
Azure VM 類型和搭配 SAP HANA 的 SAP 支援的工作負載案例所述[SAP 認證的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 

SAP 認證適用於 SAP NetWeaver 或 S/4HANA 應用程式層的 azure VM 類型所述[SAP 附註 1928533:在 Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533/E)。

> [!NOTE]
> 只有 Azure Resource Manager 支援 SAP-Linux-Azure 整合，傳統部署模型並不支援。 

## <a name="manual-installation-of-sap-hana"></a>手動安裝 SAP HANA

> [!IMPORTANT]
> 請確定您選取的作業系統是在您使用的特定 VM 類型上的 SAP HANA 認證的 SAP。 清單中的 SAP HANA 認證的 VM 類型和作業系統版本為這些 VM 類型可以中查閱[SAP HANA 認證的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 請務必按一下 列出，以取得 SAP HANA 支援的 OS 版次的完整清單，適用於特定 VM 類型的 VM 類型的詳細資料。 這份文件中的範例，我們會使用不支援在 M 系列 Vm 上的 SAP HANA 的 sap 的 SUSE Linux Enterprise Server (SLES) OS 版次。
>

本指南說明如何在 Azure VM 上，以下列兩種不同方式手動安裝 SAP HANA：

* 在 「 安裝資料庫執行個體 」 步驟中的分散式 NetWeaver 安裝過程中使用 SAP Software Provisioning Manager (SWPM)。
* 使用 SAP HANA 資料庫生命週期管理員 (HDBLCM) 工具，然後安裝 NetWeaver。

您也可以使用 SWPM 在單一 VM 中安裝所有元件，例如 SAP HANA、 SAP 應用程式伺服器和 ASCS 執行個體。 在此說明的步驟[SAP HANA 部落格公告](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)。 本快速入門指南並未說明此選項，但您必須考量的問題都相同。

在開始安裝之前，我們建議您閱讀 「 準備 Azure Vm 以便手動安裝 SAP HANA"本指南稍後的章節。 當您只使用預設的 Azure VM 設定時，這樣做有助於避免數個可能發生的基本錯誤。

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>當您使用 SAP SWPM 時，SAP HANA 安裝的主要步驟
本節會列出當您使用 SAP SWPM 執行分散式 SAP NetWeaver 7.5 安裝時，適用於手動、單一執行個體 SAP HANA 安裝的主要步驟。 本指南稍後會以螢幕擷取畫面形式更詳細說明個別步驟。

1. 建立包含這兩個測試 VM 的 Azure 虛擬網路。
2. 部署兩個 Azure Vm 的 Azure Resource Manager 模型根據作業系統。 此範例會使用 SUSE Linux Enterprise Server 和 SLES for SAP Applications 12 SP1。 
3. 將兩個 Azure 標準或進階儲存體磁碟，例如 75-GB 或 500-GB 磁碟附加至應用程式伺服器 VM。
4. 將進階儲存體連結至 HANA DB 伺服器 VM。 如需詳細資訊，請參閱本指南稍後的 < 磁碟設定 > 一節。
5. 根據大小或輸送量需求連接多個磁碟。 接著，建立等量磁碟區。 使用邏輯磁碟區管理 (LVM) 或多個裝置管理 (mdadm) 工具在 VM 內的 OS 層級。
6. 在連接的磁碟或邏輯磁碟區上建立 XFS 檔案系統。
7. 在 OS 層級上掛接新的 XFS 檔案系統。 將一個檔案系統用於所有 SAP 軟體。 例如，將其他檔案系統用於 /sapmnt 目錄和備份。 在 SAP HANA DB 伺服器的進階儲存體磁碟上，將 XFS 檔案系統掛接為 /hana 和 /usr/sap。 此程序是為了避免填滿根目錄檔案系統。 根目錄檔案系統上不算大 Linux Azure Vm。 
8. 在 /etc/hosts 檔案中輸入測試 VM 的本機 IP 位址。
9. 在 /etc/fstab 檔案中輸入 **nofail** 參數。
10. 設定 Linux 核心參數，根據您所使用的 Linux 作業系統版本。 如需詳細資訊，請參閱 SAP 附註討論 HANA 和本指南的 < 核心參數 > 一節。
11. 新增交換空間。
12. (選擇性) 在測試 VM 上安裝圖形化桌面。 否則，請使用遠端 SAPinst 安裝。
13. 從 SAP Service Marketplace 下載 SAP 軟體。
14. 在應用程式伺服器 VM 上安裝 SAP ASCS 執行個體。
15. 使用 NFS，在測試 VM 之間共用 /sapmnt 目錄。 應用程式伺服器 VM 為 NFS 伺服器。
16. 安裝資料庫執行個體，其中包含 HANA DB 伺服器 VM 上使用 SWPM。
17. 在應用程式伺服器 VM 上，安裝主要應用程式伺服器 (PAS)。
18. 啟動 SAP 管理主控台 (SAP MC)。 例如，連線到 SAP GUI 或 HANA Studio。

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>當您使用 HDBLCM 時，SAP HANA 安裝的主要步驟
本節會列出當您使用 SAP HDBLCM 執行分散式 SAP NetWeaver 7.5 安裝時，適用於手動、單一執行個體 SAP HANA 安裝的主要步驟。 整份指南會以螢幕擷取畫面形式更詳細說明個別步驟。

1. 建立包含這兩個測試 VM 的 Azure 虛擬網路。
2. 部署 Azure Resource Manager 模型根據作業系統的兩個 Azure Vm。 此範例會使用 SLES 和 SLES for SAP Applications 12 SP1。
3. 將兩個 Azure 標準或進階儲存體磁碟，例如 75-GB 或 500-GB 磁碟附加至應用程式伺服器 VM。
4. 將進階儲存體連結至 HANA DB 伺服器 VM。 如需詳細資訊，請參閱本指南稍後的 < 磁碟設定 > 一節。
5. 根據大小或輸送量需求連接多個磁碟。 使用邏輯磁碟區管理或 mdadm 工具在 VM 內的 OS 層級建立等量磁碟區。
6. 在連接的磁碟或邏輯磁碟區上建立 XFS 檔案系統。
7. 在 OS 層級上掛接新的 XFS 檔案系統。 將一個檔案系統用於所有 SAP 軟體。 例如，將其他檔案系統用於 /sapmnt 目錄和備份。 在 SAP HANA DB 伺服器的進階儲存體磁碟上，將 XFS 檔案系統掛接為 /hana 和 /usr/sap。 此程序是為了協助防止填滿根目錄檔案系統。 根目錄檔案系統上不算大 Linux Azure Vm。
8. 在 /etc/hosts 檔案中輸入測試 VM 的本機 IP 位址。
9. 在 /etc/fstab 檔案中輸入 **nofail** 參數。
10. 設定核心參數，根據您所使用的 Linux 作業系統版本。 如需詳細資訊，請參閱 SAP 附註討論 HANA 和本指南的 < 核心參數 > 一節。
11. 新增交換空間。
12. (選擇性) 在測試 VM 上安裝圖形化桌面。 否則，請使用遠端 SAPinst 安裝。
13. 從 SAP Service Marketplace 下載 SAP 軟體。
14. 在 HANA DB 伺服器 VM 上，建立群組識別碼為 1001 的群組 "sapsys"。
15. 安裝 SAP HANA DB 伺服器 VM 上，藉由使用 HANA 資料庫生命週期管理員。
16. 在應用程式伺服器 VM 上安裝 SAP ASCS 執行個體。
17. 使用 NFS，在測試 VM 之間共用 /sapmnt 目錄。 應用程式伺服器 VM 為 NFS 伺服器。
18. 安裝資料庫執行個體，其中包含 HANA 中，使用 SWPM 在 HANA DB 伺服器 VM 上。
19. 應用程式伺服器 VM 上安裝主要應用程式伺服器。
20. 啟動 SAP MC。 透過 SAP GUI 或 HANA Studio 連線。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>準備 Azure VM 以便手動安裝 SAP HANA
本節包含下列主題：

* OS 更新
* 磁碟設定
* 核心參數
* 檔案系統
* /etc/hosts 檔案
* /etc/fstab 檔案

### <a name="os-updates"></a>OS 更新
安裝其他軟體之前，先檢查 Linux OS 更新和修正程式。 安裝修補程式，您可能會避免呼叫支援人員。

請確定您使用：
* 適用於 SAP 應用程式的 SUSE Linux Enterprise Server。
* 適用於 SAP 應用程式的 Red Hat Enterprise Linux 或適用於 SAP HANA 的 Red Hat Enterprise Linux。 

如果您還沒有這麼做，請與 Linux 廠商提供您 Linux 訂用帳戶登錄 OS 部署。 SUSE 具有 OS 映像為 SAP 應用程式，已經包含服務，且會自動註冊。

以下是如何檢查是否有可用的修補程式適用於 SUSE Linux 使用的範例**zypper**命令：

 `sudo zypper list-patches`

視問題的種類而定，修補程式會依分類和嚴重性歸類。 常用的分類值為： 
- 安全性
- 建議
- 選用
- 功能
- 文件
- yast

常用的嚴重性值為：

- 重要
- 重要事項
- 中度
- 低
- 未指定

**Zypper** 命令只會尋找已安裝套件所需要的更新。 例如，您可以使用此命令：

`sudo zypper patch  --category=security,recommended --severity=critical,important`

您可以新增 `--dry-run` 參數來測試更新，而不需實際更新系統。


### <a name="disk-setup"></a>磁碟設定
Azure 上 Linux VM 中的根目錄檔案系統有大小限制。 因此，您需要將額外的磁碟空間附加至執行 SAP 的 Azure VM。 針對 SAP 應用程式伺服器 Azure Vm，使用 Azure 標準儲存體磁碟可能就足夠。 針對 SAP HANA DBMS Azure Vm，Azure 進階儲存體磁碟用於生產環境和非生產實作都是必要的。

根據[SAP HANA TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，建議使用下列的 Azure 進階儲存體組態： 

| VM SKU | RAM |  /hana/data 和 /hana/log <br /> 與 LVM 或 mdadm 等量 | HANA/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

在建議的磁碟組態中，HANA 資料磁碟區和記錄磁碟區位於相同的 Azure 進階儲存體磁碟，會與 LVM 或 mdadm 等量集上。 它不需要定義任何 RAID 備援層級，因為 Azure 進階儲存體會保留三個備援的磁碟映像。 

若要確定您設定足夠的儲存體，請參閱[SAP HANA TDI 儲存體需求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)並[SAP HANA server 安裝與更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。 也請考慮不同的 Azure 進階儲存體磁碟的不同虛擬硬碟 (VHD) 輸送量的磁碟區中所述[高效能進階儲存體與受控的磁碟 Vm](../../windows/disks-types.md)。 

您可以將更多的進階儲存體磁碟新增至 HANA DBMS Vm，來儲存資料庫或交易記錄備份。

如需用來設定串接的兩個主要工具的詳細資訊，請參閱：

* [Linux 上設定軟體 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [在 Azure 中的 Linux VM 上設定 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

如需有關如何將磁碟連結至作為客體 OS 中執行 Linux 的 Azure Vm 的詳細資訊，請參閱 <<c0> [ 將磁碟新增至 Linux VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

您可以使用 Azure 進階 Ssd，定義磁碟快取模式。 針對存放 /hana/data 和 /hana/log 的等量集，停用磁碟快取。 針對其他磁碟區，也就是磁碟、 將快取模式設定為**ReadOnly**。

若要尋找要用來建立 Vm 的範例 JSON 範本，請參閱[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)。
vm-simple-sles 範本是基本的範本。 它包含儲存體區段與其他 100 GB 的資料磁碟。 使用此範本做為基底。 您可以針對特定的組態採用範本。

> [!NOTE]
> 請務必將 Azure 儲存體磁碟連結中所述，使用 UUID[在 Microsoft Azure SUSE Linux Vm 上執行 SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

在測試環境中，兩個 Azure 標準儲存體磁碟已連結至 SAP 應用程式伺服器 VM，如下列螢幕擷取畫面所示。 一個磁碟會儲存所有 SAP 軟體，例如 NetWeaver 7.5、sap、 SAP GUI 和 SAP HANA 安裝。 第二個磁碟可確保足夠的可用空間可用於其他需求。 比方說，備份和測試資料和 /sapmnt 目錄，也就是 SAP 設定檔，必須在屬於相同 SAP 架構的所有 Vm 之間共用。

![SAP HANA 應用程式伺服器的 [磁碟] 視窗，其中顯示兩個資料磁碟及其大小](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>核心參數
SAP HANA 需要特定 Linux 核心設定。 這些核心設定不屬於標準 Azure 資源庫映像，而必須手動設定。 依據您是使用 SUSE 還是 Red Hat，參數可能不一樣。 先前列出的 SAP 附註會提供這些參數的相關資訊。 在顯示的螢幕擷取畫面中，使用 SUSE Linux 12 SP1。 

SLES for SAP Applications 12 的一般可用性和 SLES for SAP Applications 12 SP1 有新的工具， **-tuned-adm**，可取代舊**sapconf**工具。 **tuned-adm** 有特別的 SAP HANA 設定檔可供其使用。 適用於 SAP HANA 調整系統，請以根使用者身分輸入下列設定檔：

   `tuned-adm profile sap-hana`

如需有關 **tuned-adm** 的詳細資訊，請參閱[關於 tuned-adm 的 SUSE 文件](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)。

在下列螢幕擷取畫面中，您可以看到如何 **-tuned-adm**變更`transparent_hugepage`和`numa_balancing`根據必要的 SAP HANA 設定的值：

![tuned-adm 工具會根據必要的 SAP HANA 設定來變更值](./media/hana-get-started/image005.jpg)

若要永久保留 SAP HANA 核心設定，請在 SLES 12 上使用 **grub2**。 如需詳細資訊**grub2**，請參閱[組態檔結構](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)SUSE 文件的章節。

下列螢幕擷取畫面顯示如何在組態檔中變更核心設定，然後使用 **grub2-mkconfig** 進行編譯：

![在組態檔中變更核心設定，然後使用 grub2-mkconfig 進行編譯](./media/hana-get-started/image006.jpg)

另一個選項是使用 YaST 和 [開機載入器]   > [核心參數]  設定來變更這些設定：

![YaST 開機載入器中的 [核心參數] 設定索引標籤](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>檔案系統
下列螢幕擷取畫面顯示兩個檔案系統，這兩者均建立於這兩個連接之 Azure 標準儲存體磁碟上的 SAP 應用程式伺服器 VM 上。 這兩個檔案系統的 XFS 類型，並掛接至 /sapdata 和 /sapsoftware。

它不一定要如此一來建構檔案系統。 您有其他選項可用來建構磁碟空間。 最重要的考量是防止根目錄檔案系統的可用空間用盡。

![在 SAP 應用程式伺服器 VM 上建立兩個檔案系統](./media/hana-get-started/image008.jpg)

針對 SAP HANA DB VM，資料庫在安裝期間，當您使用 SWPM 使用 SAPinst 和**一般**安裝選項時，一切都已安裝在 /hana 和 /usr/sap 之下。 SAP HANA 記錄備份的預設位置是在 /usr/sap 之下。 同樣地，務必防止根目錄檔案系統儲存體空間不足。 請確定有足夠的可用空間在 /hana 和 /usr/sap 之下之前使用 SWPM 安裝 SAP HANA。

如需 SAP HANA 的標準檔案系統配置的說明，請參閱 < [SAP HANA server 安裝與更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。

![在 SAP 應用程式伺服器 VM 上建立的其他檔案系統](./media/hana-get-started/image009.jpg)

當您在標準 SLES/SLES for SAP Applications 12 Azure 資源庫映像上安裝 SAP NetWeaver 時，則會顯示一則訊息，指出沒有交換空間，如下列螢幕擷取畫面所示。 若要關閉此訊息，您可以使用 **dd**、**mkswap** 和 **swapon** 手動新增分頁檔。 若要了解做法，請搜尋 「 新增分頁檔以手動方式 」 中[使用 YaST partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) SUSE 文件的章節。

另一個選項是使用 Linux VM 代理程式設定交換空間。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](../../extensions/agent-linux.md)。

![快顯訊息，指出沒有足夠交換空間](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 檔案
開始安裝 SAP 前，請確定在 /etc/hosts 檔案中包含 SAP VM 的主機名稱和 IP 位址。 將一個 Azure 虛擬網路內的所有 SAP Vm 的都部署。 然後使用內部 IP 位址，如下所示：

![SAP VM 的主機名稱和 IP 位址會列於 /etc/hosts 檔案中](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 檔案

它加入有幫助**nofail**參數至 fstab 檔案。 如此一來，如果某部分出錯時使用的磁碟，VM 不停止回應的開機程序。 但請記得，可能無法取得額外的磁碟空間，而且程序可能會填滿根目錄檔案系統。 如果遺失 /hana，SAP HANA 將無法啟動。

![將 nofail 參數新增至 fstab 檔案](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 上的圖形化 GNOME 桌面
本節說明如何：

* 安裝 GNOME 桌面和 xrdp 在 SLES 12/SLES for SAP Applications 12。
* 藉由使用 SLES 12/SLES for SAP Applications 12 Firefox 執行 Java 型 SAP MC。

您也可以使用例如 Xterminal 或 VNC，這不本指南中所述的替代方案。

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>安裝 GNOME 桌面和 xrdp 在 SLES 12/SLES for SAP Applications 12
如果您具備 Windows 背景，您可以輕鬆地使用直接在 SAP Linux Vm 圖形化桌面來執行 Firefox、 SAPinst、 SAP GUI、 SAP MC 或 HANA Studio 中。 然後您可以從 Windows 電腦連接到 VM 透過遠端桌面通訊協定 (RDP)。 根據您的公司原則有關加入實際執行環境和非商業執行環境以 Linux 為基礎的系統中的 Gui，您可能想要在伺服器上安裝 GNOME。 請遵循下列步驟，在 Azure SLES 12/SLES 上安裝 GNOME 桌面，for SAP Applications 12 VM。

1. 輸入下列命令，例如在 PuTTY 視窗中，以安裝 GNOME 桌面：

   `zypper in -t pattern gnome-basic`

2. 安裝 xrdp 以允許透過 RDP 連接到 VM：

   `zypper in xrdp`

3. 編輯 /etc/sysconfig/windowmanager，並將預設的視窗管理員設定為 GNOME：

   `DEFAULT_WM="gnome"`

4. 執行 **chkconfig** 以確保 xrdp 會在重新開機後自動啟動：

   `chkconfig -level 3 xrdp on`

5. 如果您有 RDP 連線發生問題，請嘗試重新啟動，比方說，從 PuTTY 視窗中：

   `/etc/xrdp/xrdp.sh restart`

6. 如果上一個步驟中所述的 xrdp 重新啟動無法運作，請檢查 .pid 檔案：

   `check /var/run` 

   尋找 `xrdp.pid`。 如果找到的話，請將它移除，並嘗試重新啟動。

### <a name="start-sap-mc"></a>啟動 SAP MC
安裝 GNOME 桌面之後，請從 Firefox 啟動圖形化 Java 型 SAP MC。 如果執行在 Azure SLES 12/SLES for SAP Applications 12 VM，它可能會顯示錯誤。 因為遺失 Java 瀏覽器外掛程式，就會發生錯誤。

啟動 SAP MC 的 URL 為 `<server>:5<instance_number>13`。

如需詳細資訊，請參閱 <<c0> [ 啟動 web 型 SAP 管理主控台](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)。

下列螢幕擷取畫面顯示遺失 Java 瀏覽器外掛程式時所顯示的錯誤訊息：

![指出遺失 Java 瀏覽器外掛程式的錯誤訊息](./media/hana-get-started/image013.jpg)

解決這個問題的其中一種方式，是使用 YaST 安裝遺失的外掛程式，如下列螢幕擷取畫面所示：

![使用 YaST 安裝遺失的外掛程式](./media/hana-get-started/image014.jpg)

當您重新輸入 SAP 管理主控台 URL 時，系統會要求您啟用外掛程式：

![要求啟用外掛程式的對話方塊](./media/hana-get-started/image015.jpg)

您可能也會收到關於遺失檔案 (javafx.properties) 的錯誤訊息。 與 SAP GUI 7.4 的 Oracle Java 1.8 的需求。 如需詳細資訊，請參閱 < [SAP 附註 2059429](https://launchpad.support.sap.com/#/notes/2059424)。
IBM Java 版本並傳遞與 SLES/SLES for SAP Applications 12 的 openjdk 套件不包含所需的 javafx.properties 檔案。 解決方案是從 Oracle 下載 Java SE 8。

如需與 OpenSUSE 上 openjdk 類似問題的相關資訊，請參閱討論區對話 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)。

## <a name="manual-installation-of-sap-hana-swpm"></a>手動安裝 SAP HANA：SWPM
一系列的螢幕擷取畫面，這一節會說明如何當您使用 SAPinst 使用 SWPM 安裝 SAP NetWeaver 7.5 和 SAP HANA SP12 的主要步驟。 在 NetWeaver 7.5 安裝的一部分，SWPM 也可以將 HANA 資料庫安裝成單一執行個體。

在範例測試環境中，我們會安裝一部進階商業應用程式程式設計 (ABAP) 應用程式伺服器。 下列螢幕擷取畫面所示，我們使用了**分散式系統**選項可在 Azure VM 中安裝 ASCS 和主要應用程式伺服器執行個體。 我們會將 SAP HANA 作為資料庫系統在另一個 Azure VM。

![使用 [分散式系統] 選項安裝 ASCS 和主要應用程式伺服器執行個體](./media/hana-get-started/image012.jpg)

應用程式伺服器 VM 上安裝 ASCS 執行個體之後，它是由 SAP 管理主控台中的綠色圖示來識別。 必須共用 /sapmnt 目錄，其中包含 SAP 設定檔目錄，與 SAP HANA DB 伺服器 VM。 DB 安裝步驟需要存取此資訊。 提供存取的最佳方式是使用可利用 YaST 設定的 NFS。

![顯示應用程式伺服器使用的綠色圖示的 VM 上安裝 ASCS 執行個體的 SAP 管理主控台](./media/hana-get-started/image016.jpg)

應用程式伺服器 VM 上的 /sapmnt 目錄透過 NFS 使用的共用**rw**並**no_root_squash**選項。 預設值為 **ro** 和 **root_squash**，這可能導致安裝資料庫執行個體時發生問題。

![透過 NFS 使用 rw 和 no_root_squash 選項共用 /sapmnt 目錄](./media/hana-get-started/image017b.jpg)

下一步 的螢幕擷取畫面所示，從應用程式伺服器 VM 中的 /sapmnt 共用必須設定在 SAP HANA DB 伺服器 VM 上使用**NFS 用戶端**和 YaST:

![使用 NFS 用戶端來設定 /sapmnt 共用](./media/hana-get-started/image018b.jpg)

若要執行分散式的 NetWeaver 7.5 安裝，亦即**資料庫執行個體**、 登入 SAP HANA DB 伺服器 VM 並啟動 SWPM:

![登入 SAP HANA DB 伺服器 VM，然後啟動 SWPM，藉以安裝資料庫執行個體](./media/hana-get-started/image019.jpg)

選取後**典型**安裝和安裝媒體路徑，請輸入 DB SID、 主機名稱、 執行個體號碼和 DB 系統管理員密碼：

![SAP HANA 資料庫系統管理員登入頁面](./media/hana-get-started/image035b.jpg)

輸入 DBACOCKPIT 結構描述的密碼：

![DBACOCKPIT 結構描述的密碼輸入方塊](./media/hana-get-started/image036b.jpg)

輸入 SAPABAP1 結構描述密碼的問題：

![輸入 SAPABAP1 結構描述密碼的問題](./media/hana-get-started/image037b.jpg)

完成每個工作之後，就會在 DB 安裝程序的每個階段旁邊顯示綠色核取記號。 訊息「執行...資料庫執行個體已完成。」隨即顯示。

![含有確認訊息的完成工作視窗](./media/hana-get-started/image023.jpg)

安裝成功後，SAP 管理主控台也會顯示綠色圖示 DB 執行個體。 它會顯示完整的 SAP HANA 程序，例如 hdbindexserver 和 hdbcompileserver 清單。

![含有 SAP HANA 程序清單的 SAP 管理主控台視窗](./media/hana-get-started/image024.jpg)

下列螢幕擷取畫面會顯示 SWPM 在 HANA 安裝期間於 /hana/shared 目錄下所建立的部分檔案結構。 因為沒有任何選項可以指定不同的路徑，務必使用 SWPM 掛接額外的磁碟空間，安裝 SAP HANA 之前在 /hana 目錄之下。 此步驟中會防止根目錄檔案系統的可用空間不足。

![在 HANA 安裝期間建立 /hana/shared 目錄檔案結構](./media/hana-get-started/image025.jpg)

這個螢幕擷取畫面會顯示 /usr/sap 目錄的檔案結構：

![/usr/sap 目錄檔案結構](./media/hana-get-started/image026.jpg)

分散式 ABAP 安裝的最後一個步驟，是安裝主要應用程式伺服器執行個體：

![將主要應用程式伺服器執行個體顯示為最後一個步驟的 ABAP 安裝](./media/hana-get-started/image027b.jpg)

安裝主要應用程式伺服器執行個體和 SAP GUI 之後，使用**DBA Cockpit**交易來確認已正確完成 SAP HANA 安裝：

![確認安裝成功的 DBA Cockpit 視窗](./media/hana-get-started/image028b.jpg)

最後一個步驟中，您可能要先在 SAP 應用程式伺服器 VM 中安裝 HANA Studio。 然後連接至在 DB 伺服器 VM 執行 SAP HANA 執行個體。

![在 SAP 應用程式伺服器 VM 中安裝 SAP HANA Studio](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>手動安裝 SAP HANA：HDBLCM
除了使用 SWPM 在分散式安裝過程中安裝 SAP HANA 之外，您還能使用 HDBLCM，先獨立安裝 HANA。 例如，您接著可以安裝 SAP NetWeaver 7.5。 本節中的螢幕擷取畫面會顯示此程序的運作方式。

如需有關 HANA HDBLCM 工具的詳細資訊，請參閱：

* [選擇正確的 SAP HANA HDBLCM，為您的工作](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)。
* [SAP HANA 生命週期管理工具](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)。
* [SAP HANA server 安裝與更新指南](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)。

您想要避免發生問題的預設群組識別碼設定`\<HANA SID\>adm user`，由 HDBLCM 工具建立。 安裝 SAP HANA HDBLCM 透過之前，定義新的群組，稱為`sapsys`藉由使用群組識別碼`1001`:

![使用群組識別碼 1001 定義的新群組 "sapsys"](./media/hana-get-started/image030.jpg)

當您第一次啟動 HDBLCM 時，簡單的開始功能表顯示。 選取的項目 1**安裝新的系統**:

![HDBLCM 開始視窗中的 [安裝新的系統] 選項](./media/hana-get-started/image031.jpg)

下列螢幕擷取畫面顯示您先前選取的所有重要選項。

> [!IMPORTANT]
> 針對 HANA 記錄檔和資料磁碟區，安裝路徑，也就是在此範例中和 /usr/sap 之下的 /hana/shared 命名的目錄不能為根目錄檔案系統的一部分。 這些目錄屬於連接至 VM 的 Azure 資料磁碟。 如需詳細資訊，請參閱 < 磁碟設定 > 一節。 

這個方法有助於避免根檔案系統的空間不足。 請注意，HANA 系統管理員有使用者識別碼`1005`且屬於`sapsys`群組，以及識別碼`1001`，安裝前所定義。

![先前選取的所有重要 SAP HANA 元件清單](./media/hana-get-started/image032.jpg)

檢查`\<HANA SID\>adm user`eg /etc/ passwd 目錄中的詳細資料。 尋找`azdadm`，如下列螢幕擷取畫面所示：

![HANA \<HANA SID\>adm 使用者詳細資料會列於 /etc/passwd 目錄中](./media/hana-get-started/image033.jpg)

使用 HDBLCM 安裝 SAP HANA 之後，您就能在 SAP HANA Studio 中看見檔案結構，如下列螢幕擷取畫面所示。 尚未提供 SAPABAP1 結構描述 (其中包括所有的 SAP NetWeaver 資料表)。

![SAP HANA Studio 中的 SAP HANA 檔案結構](./media/hana-get-started/image034.jpg)

安裝 SAP HANA 之後，就能在其上安裝 SAP NetWeaver。 下列螢幕擷取畫面所示，安裝已使用 SWPM 執行分散式安裝。 此程序是在上一節中所述。 當您使用 SWPM 安裝資料庫執行個體時，您可以使用 HDBLCM 輸入相同的資料。 例如，您輸入的主機名稱、 HANA SID 和執行個體數目。 SWPM 接著會使用現有的 HANA 安裝，並加入多個結構描述。

![使用 SWPM 執行分散式安裝](./media/hana-get-started/image035b.jpg)

下列螢幕擷取畫面會顯示 SWPM 安裝步驟，您可以在其中輸入 DBACOCKPIT 結構描述的相關資料：

![輸入 DBACOCKPIT 結構描述資料的 SWPM 安裝步驟](./media/hana-get-started/image036b.jpg)

輸入 SAPABAP1 結構描述的相關資料：

![輸入 SAPABAP1 結構描述的相關資料](./media/hana-get-started/image037b.jpg)

完成 SWPM 資料庫執行個體安裝後，您可以看到 SAP HANA Studio 中的 SAPABAP1 結構描述：

![SAP HANA Studio 中的 SAPABAP1 結構描述](./media/hana-get-started/image038b.jpg)

最後，SAP 應用程式伺服器和 SAP GUI 安裝完成後，來驗證 HANA DB 執行個體使用**DBA Cockpit**交易：

![使用 DBA Cockpit 來交易驗證 HANA DB 執行個體](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 軟體下載
您可以從 SAP Service Marketplace 下載軟體，如下列螢幕擷取畫面所示。

下載適用於 Linux/HANA 的 NetWeaver 7.5：

 ![SAP 服務安裝和升級視窗用於下載 NetWeaver 7.5](./media/hana-get-started/image001.jpg)

下載 HANA SP12 平台版本：

 ![SAP 服務安裝和升級視窗下載 HANA SP12 平台版本](./media/hana-get-started/image002.jpg)

