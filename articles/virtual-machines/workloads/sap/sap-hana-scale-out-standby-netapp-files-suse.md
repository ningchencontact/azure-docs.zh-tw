---
title: 使用 SUSE Linux Enterprise Server 上的 Azure NetApp Files 在 Azure Vm 上以待命節點 SAP Hana 相應放大 |Microsoft Docs
description: 在 SUSE Linux Enterprise Server 上使用 sap NetWeaver 的高可用性指南，適用于 SAP 應用程式的 Azure NetApp Files
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934168"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>使用 SUSE Linux Enterprise Server 上的 Azure NetApp Files 在 Azure Vm 上以待命節點 SAP Hana 相應放大 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文說明如何在 Azure 虛擬機器上部署具有待命功能的高可用性 HANA 系統，以及共用存放磁片區的[Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) 。  
在範例設定中，安裝命令等等，HANA 實例是**03** ，而 HANA 系統識別碼是**HN1**。 這些範例是以 HANA 2.0 SP4 和 SAP 12 SP4 SUSE Linux Enterprise Server 為基礎。 

請先閱讀下列 SAP Note 和文件：

* [Azure NetApp Files 檔][anf-azure-doc] 
* SAP Note [1928533]，其中包含：  
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2205917] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的建議 OS 設定
* SAP Note [1944799]具有 Sap 應用程式 SUSE LINUX ENTERPRISE SERVER 的 sap 方針
* SAP Note [2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1984787] 包含 SUSE LINUX Enterprise Server 12 的一般資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* SAP 附注[1900823]包含 SAP Hana 儲存體需求的相關資訊
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用于 SAP on Linux 的 Azure 虛擬機器規劃和執行][planning-guide]
* [適用于 SAP on Linux 的 Azure 虛擬機器部署][deployment-guide]
* [適用于 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳做法指南][suse-ha-guide]這些指南包含設定 Netweaver HA 和內部部署 SAP Hana 系統複寫的所有必要資訊。 請使用這些指南作為一般基準。 它們提供更詳細的資訊。
* [SUSE 高可用性擴充功能 12 SP3 版本資訊][suse-ha-12sp3-relnotes]
* [使用 Azure NetApp Files 在 Microsoft Azure 的 NetApp SAP 應用程式][anf-sap-applications-azure]
* [使用 NFS 在 NetApp 系統上 SAP Hana](https://www.netapp.com/us/media/tr-4435.pdf)。 設定指南包含如何使用 Azure NetApp Files 所提供的 NFS 來設定 SAP Hana 的資訊。


## <a name="overview"></a>概觀

達到 HANA 高可用性的其中一個方法是主機自動故障切換。 若要設定主機自動故障切換，請將一或多個虛擬機器新增至 HANA 系統，並將其設定為待命節點。 當作用中節點失敗時，待命節點會自動接管。 在 azure 虛擬機器的顯示設定中，由[NFS 在 Azure NetApp Files 上](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)完成。  

待命節點必須具有所有資料庫磁片區的存取權。 HANA 磁片區必須裝載為 NFSv4 磁片區。 NFSv4 通訊協定中改良的檔案租用型鎖定機制是用於 `I/O` 隔離。 

> [!IMPORTANT]
> 您必須將 HANA 資料和記錄磁片區部署為 NFSv 4.1 磁片區，並使用 NFSv 4.1 通訊協定掛接它們，才能擁有支援的設定。 NFSv3 不支援具有待命節點的 HANA 主機自動容錯移轉設定。

![SAP NetWeaver 高可用性概觀](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

遵循 SAP Hana 的網路建議，在一個 Azure 虛擬網路內建立三個子網：用於與儲存系統通訊，適用于內部 HANA 節點間通訊和用戶端通訊。 Azure NetApp 磁片區位於個別的子網中，並[委派給 Azure Netapp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

針對此範例設定，子網為：  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>設定 Azure NetApp Files 基礎結構 

繼續進行 Azure NetApp files 基礎結構的設定之前，請先熟悉[Azure Netapp files][anf-azure-doc]檔。 Azure NetApp files 在數個[azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中都有提供。 檢查您選取的 Azure 區域是否提供 Azure NetApp Files。  

下列連結會依 Azure 區域顯示 Azure NetApp Files 的可用性： azure [Netapp files By Azure 區域的可用性][anf-avail-matrix]。  
在部署 Azure NetApp Files 之前，請在註冊 azure netapp files[指示][anf-register]之後，要求上線至 Azure netapp files。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp Files 資源  

下列步驟假設您已部署[Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp Files 資源和 Vm （將裝載 Azure NetApp Files 資源）必須部署在相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。  

1. 如果您還沒有這麼做，請要求上[架至 Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  

2. 遵循[建立 Netapp 帳戶的指示](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)，在選取的 Azure 區域中建立 netapp 帳戶。  

3. 設定 Azure NetApp Files 容量集區，請遵循[如何設定 Azure Netapp files 容量](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)集區的指示。  
本文中所提供的 HANA 架構使用單一 Azure NetApp Files 容量集區 Ultra 服務層級。 針對 Azure 上的 HANA 工作負載，我們建議 Azure NetApp Files Ultra 或 Premium[服務層級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)。  

4. 將子網委派給 Azure NetApp files，如將[子網委派給 Azure Netapp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中的指示所述。  

5. 部署 Azure NetApp Files 磁片區，[並遵循指示來建立 Azure Netapp files 的磁片](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)區。  部署磁片區時，請務必選取 [ **nfsv 4.1**版本]。 目前 NFSv 4.1 的存取權需要額外的允許清單。 將磁片區部署在指定的 Azure NetApp Files[子網](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中。 請記住，Azure NetApp Files 資源和 Azure Vm 必須位於相同的 Azure 虛擬網路或對等互連 Azure 虛擬網路中。 例如<b>HN1</b>-Mnt00001、 <b>HN1</b>-log-mnt00001 等是磁片區名稱和 nfs://10.23.1.5/<b>HN1</b>-data-mnt00001、nfs://10.23.1.4/<b>HN1</b>-Log-mnt00001 等等，是 Azure NetApp Files 磁片區的檔案路徑。  

   1. 磁片區<b>HN1</b>-資料-mnt00001 （nfs://10.23.1.5/<b>HN1</b>-資料-mnt00001）
   2. 磁片區<b>HN1</b>-資料-mnt00002 （nfs://10.23.1.6/<b>HN1</b>-資料-mnt00002）
   3. 磁片區<b>HN1</b>-記錄-mnt00001 （nfs://10.23.1.4/<b>HN1</b>-log-mnt00001）
   4. 磁片區<b>HN1</b>-記錄-mnt00002 （nfs://10.23.1.6/<b>HN1</b>-log-mnt00002）
   5. 磁片區<b>HN1</b>-共用（nfs://10.23.1.4/<b>HN1</b>-共用）
   
   在此範例中，我們為每個 HANA 資料和記錄磁片區使用不同的 Azure NetApp Files。 如需較小或非高生產力系統上的更高成本優化設定，可以將所有資料掛接和所有記錄掛接在單一磁片區上。  

### <a name="important-considerations"></a>重要考量︰

在針對 SUSE 高可用性架構的 SAP Netweaver 考慮 Azure NetApp Files 時，請注意下列重要考慮：

- 最小容量集區為 4 TiB。  
- 最小磁片區大小為 100 GiB
- Azure NetApp Files 和所有虛擬機器（將裝載 Azure NetApp Files 磁片區）必須位於相同的 Azure 虛擬網路或相同區域的[對等互連虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)中。  
- 選取的虛擬網路必須有委派給 Azure NetApp Files 的子網。
- Azure NetApp volume 的輸送量是磁片區配額和服務等級的功能，如[Azure Netapp Files 的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 調整 HANA Azure NetApp 磁片區的大小時，請確定產生的輸送量符合 HANA 系統需求。  
- Azure NetApp Files 提供[匯出原則](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：您可以控制允許的用戶端、存取類型（讀取 & 寫入、唯讀等等）。 
- Azure NetApp Files 功能尚無法感知區域。 Azure NetApp Files 功能目前不會部署在 Azure 區域中的所有可用性區域。 請留意某些 Azure 區域中可能的延遲含意。  
- 請務必將虛擬機器部署在接近 Azure NetApp storage 的鄰近性，以獲得低延遲。 SAP Hana 工作負載的低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁片區已部署在接近的附近。  
- <b>Sid</b>Adm 的使用者識別碼和虛擬機器上 `sapsys` 的群組識別碼必須符合 Azure NetApp Files 中的設定。 

> [!IMPORTANT]
> SAP Hana 工作負載的低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁片區已部署在接近的附近。  

> [!IMPORTANT]
> 如果<b>sid</b>Adm 的使用者識別碼與虛擬機器與 Azure NetApp 設定之間 `sapsys` 的群組識別碼不相符，則在虛擬機器上裝載的 Azure NetApp 磁片區上的檔案許可權將會顯示為 `nobody`。 [在將新系統](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)上線至 Azure NetApp Files 時，請務必指定<b>sid</b>ADM 的正確使用者識別碼，以及 `sapsys` 的群組識別碼。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>針對 Azure NetApp Files 上的 HANA 資料庫進行大小調整

Azure NetApp volume 的輸送量是磁片區大小和服務層級的功能，如[Azure Netapp Files 的服務等級](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

當您在 Azure 中設計 SAP 的基礎結構時，您應該注意 SAP 所需的一些最低儲存體需求，這會轉譯為最小的輸送量特性：

- 在 250 MB/秒的/hana/log 上啟用讀取/寫入（具有 1 MB i/o 大小）  
- 為/hana/data 啟用至少 400 MB/秒的讀取活動，適用于 16 MB 和 64 MB 的 i/o 大小  
- 針對具有 16 MB 和 64 MB i/o 大小的/hana/data，啟用至少 250 MB/秒的寫入活動  

每1個磁片區配額 TiB 的[Azure NetApp Files 輸送量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)為：
- 進階儲存體層-64 MiB/秒  
- Ultra 儲存層-128 MiB/秒  

為了符合資料和記錄的 SAP 最低輸送量需求，以及根據 `/hana/shared` 的指導方針，建議的大小如下所示：

| 磁碟區 | 大小<br /> 進階儲存體層 | 大小<br /> Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- | --- |
| /hana/log | 4 TiB | 2 TiB | 4\.1 版 |
| /hana/data | 6.3 TiB | 3.2 TiB | 4\.1 版 |
| HANA/shared | 每4個背景工作節點最大值（512 GB、1xRAM） | 每4個背景工作節點最大值（512 GB、1xRAM） | v3 或4.1 版 |

本文中顯示的版面配置 SAP Hana 設定，使用 Azure NetApp Files Ultra 儲存層會如下所示：

| 磁碟區 | 大小<br /> Ultra 儲存層 | 支援的 NFS 通訊協定 |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | 4\.1 版 |
| /hana/log/mnt00002 | 2 TiB | 4\.1 版 |
| /hana/data/mnt00001 | 3.2 TiB | 4\.1 版 |
| /hana/data/mnt00002 | 3.2 TiB | 4\.1 版 |
| HANA/shared | 2 TiB | v3 或4.1 版 |

> [!NOTE]
> 此處所述的 Azure NetApp Files 大小建議以符合 SAP 向其基礎結構提供者表示的最低需求。 在實際的客戶部署和工作負載案例中，這可能不夠。 根據您特定工作負載的需求，使用這些建議作為起點並進行調整。  

> [!TIP]
> 您可以動態重新調整 Azure NetApp Files 磁片區的大小，而不需要 `unmount` 磁片區、停止虛擬機器或停止 SAP Hana。 這可讓您彈性地同時滿足應用程式的預期和未預期的輸送量需求。

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>透過 Azure 入口網站部署 Linux 虛擬機器

首先，您必須建立 Azure NetApp Files 磁片區。 在[azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)中建立[azure 虛擬網路子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 部署虛擬機器。 建立額外的網路介面，並將網路介面連結至對應的 Vm。 每部虛擬機器都有三個網路介面，分別對應至三個 Azure 虛擬網路子網（`storage`、`hana` 和 `client`）。  瞭解[如何在 Azure 中使用多個網路介面卡建立 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。  

> [!IMPORTANT]
> SAP Hana 工作負載的低延遲很重要。 請與您的 Microsoft 代表合作，以確保虛擬機器和 Azure NetApp Files 磁片區會以接近的鄰近性部署，以達到低延遲。 當您使用 SAP Hana Azure NetApp Files 的[新 SAP Hana 系統](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)時，請提交所需的資訊。  
> 
接下來的步驟假設您已建立資源群組、Azure 虛擬網路，以及三個 Azure 虛擬網路子網： `storage`、`hana` 和 `client`。  部署虛擬機器時，請選取 [儲存體] 子網，讓存放裝置網路介面是虛擬機器上的主要介面。 如果無法這麼做，就必須設定透過「儲存體子網閘道」的 Azure NetApp 委派子網的明確路由。 

> [!IMPORTANT]
> 請確定您選取的作業系統在您所使用的特定 VM 類型上已獲得 SAP HANA 認證。 在 [SAP HANA 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 中可查閱 SAP HANA 認證的 VM 類型和作業系統版本清單。 請務必按一下所列 VM 類型的詳細資料，以取得 SAP HANA 針對特定 VM 類型支援的作業系統版本完整清單。  

1. 建立 SAP Hana 的可用性設定組。 請務必設定「更新網域上限」。  

2. 建立三個虛擬機器（**hanadb1**、 **hanadb2**、 **hanadb3**）  
   - 使用 Azure 資源庫中支援 SAP Hana 的使用 SLES4SAP 映射。 在此範例中，我們使用了使用 SLES4SAP 12 SP4 映射。  
   - 選取稍早為 SAP Hana 建立的可用性設定組。  
   - 選取儲存體 Azure 虛擬網路子網。 選取 [[加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)]。  
當您部署虛擬機器時，系統會自動產生網路介面名稱。 我們會參考網路介面，並將其附加至儲存體 Azure 虛擬網路子網，做為**hanadb1 儲存體**、 **hanadb2 儲存體**和**hanadb3 儲存體**。 
3. 建立三個網路介面，分別用於每部虛擬機器，`hana` 的虛擬網路子網。 在此範例中： **hanadb1-hana**、 **hanadb2-** hana 和**hanadb3-hana**。  
4. 建立三個網路介面，分別用於每個虛擬機器，適用于**用戶端**虛擬網路子網。 在此範例中： **hanadb1 客戶**端、 **hanadb2 客戶**端和**hanadb3 用戶端**。  
5. 將新建立的虛擬網路介面附加至對應的虛擬機器  

    1. 流覽至[Azure 入口網站](https://portal.azure.com/#home)中的虛擬機器。  
    2. 選取左側導覽窗格中的 [虛擬機器]。 篩選虛擬機器名稱，例如**hanadb1**。 按一下 [虛擬機器]。  
    3. 在您的總覽中，請選取 [停止] 以解除配置虛擬機器。  
    4. 選取 [網路]、[連接網路介面]。 從 [連結網路介面] 下的下拉式清單中，選取 **`hana`** 和**用戶端**子網已建立的網路介面。  儲存。 
    5. 針對其餘的虛擬機器重複執行。 在我們的範例中為**hanadb2**和**hanadb3**。
    6. 暫時讓虛擬機器處於 [已停止] 狀態。 接下來，我們將為所有新連接的網路介面啟用[加速網路](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。  

6. 針對 **`hana`** 和 **`client`** 子網的額外網路介面啟用加速網路。  

    1. 在[Azure 入口網站](https://portal.azure.com/#home)中開啟[cloud shell](https://azure.microsoft.com/features/cloud-shell/)  
    2. 執行下列命令，為附加至 **`hana`** 和 **`client`** 子網的其他網路介面啟用加速網路。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. 啟動虛擬機器  

    1. 選取左側導覽窗格中的 [虛擬機器]。 篩選虛擬機器名稱，例如**hanadb1**。 按一下 [虛擬機器]。  
    2. 在您的總覽中，請選取 [啟動]。  

## <a name="operating-system-configuration-and-preparation"></a>作業系統設定和準備

下列專案前面會加上 **[A]** -適用于所有節點、 **[1]** -僅適用于節點1、 **[2]** -僅適用于節點2或 **[3]** -僅適用于節點3。

1. **[A]** 維護虛擬機器上的主機檔案。 包含所有子網的專案。 在此範例中，已將下列專案新增至 `/etc/hosts`。  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** 變更 DHCP 和雲端 config 設定，以避免非預期的主機名稱變更。  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** 準備作業系統以使用 Nfs 在 netapp 系統上執行 SAP Hana，如[NetApp AFF systems with NFS 設定指南](https://www.netapp.com/us/media/tr-4435.pdf)中的 SAP Hana 所述。 建立 NetApp configuration 設定的設定檔： `/etc/sysctl.d/netapp-hana.conf`。  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 使用 Microsoft 的 Azure 設定來建立設定檔： `/etc/sysctl.d/ms-az.conf`。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** 根據[NetApp AFF Systems with NFS 設定指南 SAP Hana](https://www.netapp.com/us/media/tr-4435.pdf)中的建議來調整 sunrpc 設定。  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>裝載 Azure NetApp Files 磁片區

1. **[A]** 建立 HANA 資料庫磁片區的掛接點。  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** 在**HN1**上建立 `/usr/sap` 的節點特定目錄-共用。  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 確認 NFS 網域設定。 請確定已將網域設定為 **`localdomain`** ，而且對應已設為 [無**人**]。  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** 停用 NFSv4 識別碼對應。 執行 mount 命令，以建立 `nfs4_disable_idmapping` 所在的目錄結構。  您將無法在/sys/modules 下手動建立目錄，因為已為核心/驅動程式保留存取權。  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** 手動建立 SAP Hana 群組和使用者。 群組 sapsys 和使用者**hn1**Adm 的識別碼必須設定為在上架期間提供的相同識別碼。 此範例中的識別碼會設定為**1001**。 否則，將無法存取磁片區。  群組 sapsys 和使用者帳戶的識別碼**hn1**adm 和 sapadm 在所有虛擬機器上必須相同。  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** 掛接共用的 Azure NetApp Files 磁片區。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** 在**hanadb1**上掛接節點特定的磁片區。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 在**hanadb2**上掛接節點特定的磁片區。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 在**hanadb3**上掛接節點特定的磁片區。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 確認所有 HANA 磁片區都是以 NFS 通訊協定版本**NFSv4**來裝載。  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>安裝  

在此範例中，若要使用 Azure 的待命節點來部署向外延展設定中的 SAP Hana，我們使用 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>準備 HANA 安裝

1. **[A]** 設定 HANA 安裝之前的根密碼（您可以在安裝順利完成後停用根密碼）。 以 `root` 命令 `passwd` 執行。  

2. **[1]** 確認您可以透過 ssh 連線至**hanadb2**和**hanadb3**，而不會收到輸入密碼的提示。  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安裝 HANA 2.0 SP4 所需的其他套件。 如需詳細資訊，請參閱 sap 附注[2593824](https://launchpad.support.sap.com/#/notes/2593824) 。 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2，3]** 將 SAP Hana `data` 和 `log` 目錄的擁有權變更為**hn1**adm。   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA 安裝

1. **[1]** 安裝 SAP Hana，請遵循[SAP Hana 2.0 安裝和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的指示。 在此範例中，我們會使用 master、一個背景工作和一個待命節點來安裝 SAP Hana 相應放大。  
   從 HANA 安裝軟體目錄啟動**hdblcm**程式。 使用 `internal_network` 參數，並傳遞用於內部 HANA 節點間通訊的子網位址空間。  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   在提示字元中輸入下列值。

     * 選擇動作：輸入**1** （用於安裝）
     * 選取要安裝的其他元件：輸入**2、3**
     * 輸入安裝路徑：按 Enter （預設為/hana/shared）
     * 輸入本機主機名稱：按 Enter 以接受預設值
     * 您要將主機新增到系統嗎？ 輸入**y**
     * 輸入要新增的逗號分隔主機名稱： **hanadb2、hanadb3**
     * 輸入根使用者名稱 [根]：按 Enter 以接受預設值
     * 輸入根使用者密碼：輸入根目錄的密碼
     * 選取主機 hanadb2 的角色：輸入**1** （針對背景工作）
     * 輸入主機 hanadb2 的主機容錯移轉群組 [預設]：按 Enter 以接受預設值
     * 輸入主機 hanadb2 的存放磁碟分割編號 [<<assign automatically>>]：按 Enter 以接受預設值
     * 輸入用於主機 hanadb2 的背景工作角色群組 [預設]：按 Enter 以接受預設值
     * 選取主機 hanadb3 的角色：輸入**2** （適用于待命）
     * 輸入主機 hanadb3 的主機容錯移轉群組 [預設]：按 Enter 以接受預設值
     * 輸入用於主機 hanadb3 的背景工作角色群組 [預設]：按 Enter 以接受預設值
     * 輸入 SAP Hana 系統識別碼：輸入**HN1**
     * 輸入實例號碼 [00]：輸入**03**
     * 輸入本機主機背景工作角色群組 [預設]：按 Enter 以接受預設值
     * 選取系統使用量/輸入索引 [4]：輸入**4** （適用于自訂）
     * 輸入資料磁片區的位置 [/hana/data/HN1]：按 Enter 以接受預設值
     * 輸入記錄磁片區的位置 [/hana/log/HN1]：按 Enter 以接受預設值
     * 是否限制記憶體配置上限？ [n]：輸入**n**
     * 輸入主機 hanadb1 的憑證主機名稱 [hanadb1]：按 Enter 以接受預設值
     * 輸入主機 hanadb2 的憑證主機名稱 [hanadb2]：按 Enter 以接受預設值
     * 輸入主機 hanadb3 的憑證主機名稱 [hanadb3]：按 Enter 以接受預設值
     * 輸入系統管理員（hn1adm）密碼：輸入密碼
     * 輸入系統資料庫使用者（系統）密碼：輸入系統的密碼
     * 確認系統資料庫使用者（系統）密碼：輸入系統的密碼
     * 是否在電腦重新開機後重新啟動系統？ [n]：輸入**n** 
     * 您要繼續嗎（y/n）：驗證摘要，如果一切看起來正常，請輸入**y**


2. **[1]** 驗證 global .ini  

   顯示 global.asax，並確定內部 SAP Hana 節點間通訊的設定已就緒。 確認區段**通訊**。 它應該會有 **`hana`** 子網的位址空間，而 `listeninterface` 應設定為 [`.internal`]。 確認區段**internal_hostname_resolution**。 它應該具有屬於 **`hana`** 子網之 HANA 虛擬機器的 IP 位址。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 新增主機對應，以確保用戶端 IP 位址用於用戶端通訊。 新增區段 `public_host_resolution`，並從用戶端子網新增對應的 IP 位址。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** 重新開機 SAP Hana 以啟用變更。  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 確認用戶端介面將使用來自**用戶端**子網的 IP 位址進行通訊。  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   如需如何驗證設定的詳細資訊，請參閱 sap 附注[2183363-SAP Hana 內部網路](https://launchpad.support.sap.com/#/notes/2183363)的設定。  

6. 若要優化基礎 Azure NetApp files 儲存體的 SAP Hana，請設定下列 SAP Hana 參數：

   - `max_parallel_io_requests` **128**
   - `async_read_submit`**于**
   - `async_write_submit_active`**于**
   - **全部**`async_write_submit_blocks`

   如需詳細資訊，請參閱[NETAPP AFF Systems WITH NFS 設定指南中的 SAP Hana](https://www.netapp.com/us/media/tr-4435.pdf)。 

   從 SAP Hana 2.0 系統開始，您可以在 `global.ini` 中設定參數。 請參閱 SAP 附注[1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   針對 SAP Hana 1.0 系統（最多 SPS12 個版本），您可以在安裝期間設定這些參數，如 SAP 附注[2267798](https://launchpad.support.sap.com/#/notes/2267798)中所述。  

7. Azure NetApp Files 使用的儲存體有檔案大小限制或 16 TB。 SAP Hana 不會隱含察覺儲存體限制，而且當達到 16 TB 的檔案大小限制時，不會自動建立新的資料檔案。 當 SAP Hana 嘗試將檔案成長至超過 16 TB 時，將會導致錯誤，最後索引伺服器損毀。 

   > [!IMPORTANT]
   > 若要避免 SAP Hana 嘗試擴大超過儲存體 sybsystem 的[16TB 限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)的資料檔案，請在 `global.ini` 中設定下列參數。  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 如需詳細資訊，請參閱 SAP 附注[2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 注意 SAP 附注[2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>測試 SAP Hana 容錯移轉 

1. 模擬 SAP Hana 背景工作節點上的節點損毀  

   在模擬節點損毀之前，請以**hn1**adm 的身分執行下列命令來捕捉環境的狀態。  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   在背景工作節點上以 root 身分執行下列命令，在此案例中， **hanadb2**以模擬節點當機。  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   監視系統的容錯移轉完成。 當容錯移轉完成時，請捕捉狀態-它看起來應該像下面顯示的狀態。  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 為避免 SAP Hana 容錯移轉時發生延遲，當節點遇到核心異常時，請在**所有**HANA 虛擬機器上將 `kernel.panic` 設定為20秒。 設定是在 `/etc/sysctl` 中完成。 重新開機虛擬機器以啟用變更。 容錯移轉時，如果節點發生核心異常，可能需要10分鐘或更久的時間，否則不會執行此變更。  

2. 終止名稱伺服器  
   在測試之前，請以**hn1**adm 的身分執行下列命令來檢查環境的狀態：  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   在使用中的主要節點上，以**hn1**adm 的形式執行下列命令，在此案例中為**hanadb1**。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   待命節點**hanadb3**會接管作為主要節點。 容錯移轉測試完成之後的資源狀態：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   再次啟動**hanadb1**上的 HANA 實例，也就是在已終止名稱伺服器的相同虛擬機器上。 **Hanadb1**節點會重新加入環境，並會保留其待命角色。  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**hanadb1**上開始 SAP Hana 之後，預期會有下列狀態：  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   現在，在目前作用中的主要節點上，重新終止名稱伺服器，也就是在 hanadb3 上。  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   節點**hanadb1**會繼續主要節點的角色。 狀態，在容錯移轉測試完成後，會如下所示：
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   在**hanadb3**上啟動 SAP Hana-它會準備做為待命節點。  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**hanadb3**上啟動 SAP Hana 之後的狀態。  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>後續步驟

* [適用于 SAP 的 Azure 虛擬機器規劃和執行][planning-guide]
* [適用于 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用于 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 瞭解如何建立高可用性並規劃 SAP 的嚴重損壞修復 
* HANA on Azure （大型實例），請參閱[azure 上的 SAP Hana （大型實例）高可用性和嚴重損壞修復](hana-overview-high-availability-disaster-recovery.md)。
* 若要瞭解如何建立高可用性並規劃 Azure Vm 上 SAP Hana 的嚴重損壞修復，請參閱[azure 虛擬機器（vm）上 SAP Hana 的高可用性][sap-hana-ha]
