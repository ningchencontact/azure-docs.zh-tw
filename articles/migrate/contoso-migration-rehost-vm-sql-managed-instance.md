---
title: 將 Contoso 應用程式移轉至 Azure VM 和 Azure SQL 受控執行個體，以在 Azure 中重新裝載 | Microsoft Docs
description: 了解 Contoso 如何在 Azure VM 和 Azure SQL 受控執行個體上重新裝載內部部署應用程式
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: c7dc9e8406494739aa5d8f21397a606e0b74a617
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301247"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Contoso 移轉：將內部部署應用程式重新裝載至 Azure VM 和 Azure MySQL 受控執行個體

本文章說明 Contoso 如何使用 Azure Site Recovery 服務，將其 SmartHotel 應用程式前端 VM 遷移至 Azure VM，並且將應用程式資料庫遷移至 Azure SQL 受控執行個體。

> [!NOTE]
> Azure SQL 受控執行個體目前處於預覽狀態。

這份文件是一系列文章中的第四篇，描述 Contoso 這家虛構的公司如何將其內部部署資源遷移至 Microsoft Azure 雲端。 整個系列文章中包含背景資訊以及一系列案例，用以說明如何設定移轉基礎結構，以及執行不同類型的移轉。 案例會變得越來越複雜，我們會不定期增加其他文章。


**文章** | **詳細資料** | **狀態**
--- | --- | ---
[文章 1：概觀](contoso-migration-overview.md) | 簡單介紹 Contoso 的移轉策略、系列文章以及我們使用的範例應用程式。 | 可用
[文章 2：部署 Azure 基礎結構](contoso-migration-infrastructure.md) | 描述 Contoso 如何準備其內部部署和 Azure 基礎結構來進行移轉。 所有 Contoso 移轉案例都使用相同的基礎結構。 | 可用
[文章 3：評定內部部署資源](contoso-migration-assessment.md)  | 說明 Contoso 如何評定他們在 VMware 上執行的內部部署兩層式 SmartHotel 應用程式。 他們利用 [Azure Migrate](migrate-overview.md) 服務來評定應用程式 VM，以及利用 [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) 來評定應用程式 SQL Server 資料庫。 | 可用
文章 4：重新裝載至 Azure VM 和 SQL 受管理的執行個體 (本文) | 示範 Contoso 如何將 SmartHotel 應用程式遷移至 Azure。 他們使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 來遷移應用程式前端 VM，以及使用 [Azure 資料庫移轉](https://docs.microsoft.com/azure/dms/dms-overview)服務，將應用程式資料庫遷移至 SQL 受管理的執行個體。 | 可用
[文章 5：重新裝載至 Azure VM](contoso-migration-rehost-vm.md) | 說明 Contoso 如何僅使用 Site Recovery，便能夠遷移 SmartHotel 應用程式 VM。
[文章 6：重新裝載至 Azure VM 和 SQL Server 可用性群組](contoso-migration-rehost-vm-sql-ag.md) | 說明 Contoso 如何遷移 SmartHotel 應用程式。 他們使用 Site Recovery 來遷移應用程式 VM，以及使用資料庫移轉服務，將應用程式資料庫遷移至 SQL Server 可用性群組。 | 可用
[文章 7：將 Linux 應用程式重新裝載至 Azure VM](contoso-migration-rehost-linux-vm.md) | 示範 Contoso 如何利用 Site Recovery，將 Linux osTicket 應用程式遷移至 Azure VM。 | 可用
[文章 8：將 Linux 應用程式重新裝載至 Azure VM 和 Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | 示範 Contoso 如何使用 Site Recovery，將 Linux osTicket 應用程式遷移至 Azure VM，以及使用 MySQL Workbench，將應用程式資料庫遷移至 Azure MySQL Server 執行個體。 | 可用

如果您想要使用本文中所用的範例 SmartHotel 應用程式，您可以從 [github](https://github.com/Microsoft/SmartHotel360) 下載。

## <a name="on-premises-architecture"></a>內部部署架構

下圖顯示目前的 contoso 內部部署基礎結構。

![Contoso 架構](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso 有一個位於美國東部紐約市的主要資料中心。
- 他們在美國有其他三家分公司。
- 主要資料中心是透過光纖都會乙太網路連線 (500 mbps) 連到網際網路。
- 每家分公司都會使用企業級連線從本機連到網際網路，透過 IPSec VPN 通道連回主要資料中心。 這可讓其整個網路永久連線，並將網際網路連線最佳化。
- 主要資料中心已透過 VMware 完全虛擬化。 他們有兩部 ESXi 6.5 虛擬化主機，均由 vCenter Server 6.5 管理。
- Contoso 會使用 Active Directory 進行身分識別管理，以及內部網路上的 DNS 伺服器。
- 資料中心的網域控制站會在 VMware VM 上執行。 當地分公司的網域控制站會在實體伺服器上執行。



## <a name="business-drivers"></a>商業動機

IT 領導小組與其商務合作夥伴密切合作，了解企業從此次移轉想要實現什麼目標：

- **解決業務成長**：Contoso 的業務量日益增多，對內部部署系統和基礎結構造成了壓力。
- **提高效率**：Contoso 必須移除不必要的程序，並且簡化其開發人員和使用者的程序。  企業亟需快速且不浪費時間或金錢的 IT 服務，進而更快滿足客戶的需求。
- **提高靈活性**：Contoso IT 必須能夠更快回應企業的需求。 它的回應速度必須能夠比市場變化更快，才能更在全球經濟中獲致成功。  它不得礙事，或成為企業絆腳石。
- **級別**：隨著企業順利成長，Contoso IT 必須提供能夠同步成長的系統。

## <a name="migration-goals"></a>移轉目標

Contoso 雲端小組已針對此次移轉擬定好各項目標。 這些目標是用來判斷最合適的移轉方法：

- 移轉之後，應用程式不管是在 Azure 或內部部署 VMWare 環境中，都應具有相同的效能。  移到雲端，並不表示應用程式效能比較不重要。
- Contoso 不想投資這個應用程式。  這對企業很重要，但以其目前的格式而言，他們只想將它移至雲端。
- 在應用程式遷移之後，資料庫管理工作應會減到最少。
- Contoso 不想對此應用程式使用 Azure SQL Database，且正在尋求替代項目。

## <a name="proposed-architecture"></a>提議的架構

在此情節中：

- Contoso 想要遷移其兩層式內部部署旅遊應用程式。
- 這個應用程式橫跨兩層 VM (WEBVM 和 SQLVM)，而且位於 VMware ESXi 主機 **contosohost1.contoso.com** (6.5 版)
- VMware 環境是由 VM 上執行的 VCenter Server 6.5 (**vcenter.contoso.com**) 進行管理。
- 他們會將應用程式資料庫 (SmartHotelDB) 遷移到 Azure SQL 受控執行個體。
- 他們會將內部部署 VMware VM 遷移到 Azure VM。
- Contoso 有內部部署資料中心 (contoso-datacenter) 以及內部部署網域控制站 (**contosodc1**)。
- 移轉完成之後，會解除委任 Contoso 資料中心的內部部署 VM。

![案例架構](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure 服務

**服務** | **說明** | **成本**
--- | --- | ---
[資料庫管理服務](https://docs.microsoft.com/azure/dms/dms-overview) | DMS 能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 | 了解 DMS 的[支援區域](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)，並取得[定價詳細資料](https://azure.microsoft.com/pricing/details/database-migration/)。
[Azure SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | 受控執行個體是一種受控資料庫服務，可代表 Azure 雲端中的完全受控 SQL Server 執行個體。 受控執行個體會與最新版的 SQL Server 資料庫引擎共用相同的程式碼，並擁有最新的功能、效能增強功能和安全性修補程式。 | 使用在 Azure 中執行的 Azure SQL Database 受控執行個體會根據所用容量產生費用。 [深入了解](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 此服務可協調和管理 Azure VM 以及內部部署 VM 和實體伺服器的移轉和災害復原。  | 複寫至 Azure 的期間會產生 Azure 儲存體費用。  發生容錯移轉時，系統會建立 Azure VM 並產生費用。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/)費用和定價。

 

## <a name="migration-process"></a>移轉程序

Contoso 會將 SmartHotel 應用程式的 Web 和資料層遷移至 Azure。

1. Contoso 的 Azure 基礎結構已經備妥，所以只需要為此案例新增幾個特定 Azure 元件即可。
2. 資料層將會使用資料轉換服務 (DMS) 進行遷移。  DMS 會透過 Contoso 資料中心與 Azure 之間的站對站 VPN 連線來連線至內部部署 SQL Server VM，然後再遷移資料庫。
3. Web 層將會透過 Azure Site Recovery 使用隨即轉移進行遷移。 這將會需要準備內部部署 VMware 環境、設定和啟用複寫，以及將 VM 容錯移轉至 Azure 以便移轉。

     ![移轉架構](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>先決條件

以下是 Contoso (和您) 應該準備好的事項。

**需求** | **詳細資料**
--- | ---
**在預覽版中註冊** | 您必須已在 SQL 受控執行個體有限公開預覽版中註冊。 您必須有 Azure 訂用帳戶才可以[註冊](https://portal.azure.com#create/Microsoft.SQLManagedInstance)。 註冊可能需要幾天的時間才能完成，因此請務必在開始部署此案例之前完成註冊。
**Azure 訂用帳戶** | 當您在這系列的第一篇文章中執行評量時，您應該已經建立訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果您建立免費帳戶，您就是訂用帳戶的管理員，並可執行所有動作。<br/><br/> 如果您使用現有訂用帳戶，而且您不是系統管理員，則需要與系統管理員合作，讓其指派擁有者或參與者權限給您。<br/><br/> 如果您需要更細微的權限，請檢閱[此文章](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**站台復原 (內部部署)** | 內部部署 vCenter 伺服器應該執行 5.5、6.0 或 6.5 版<br/><br/> 執行 5.5、6.0 或 6.5 版的 ESXi 主機<br/><br/> 一或多部在 ESXi 主機上執行的 VMware VM。<br/><br/> VM 必須符合 [Azure 需求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。<br/><br/> 支援的[網路](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[儲存體](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)組態。
**DMS** | 針對 DMS，您需要[相容的內部部署 VPN 裝置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。<br/><br/> 您必須能夠設定內部部署 VPN 裝置。 此裝置必須有一個對外開放的公用 IPv4 位址，而且此位址不得位於 NAT 裝置後面。<br/><br/> 請確定您可以存取內部部署 SQL Server 資料庫。<br/><br/> Windows 防火牆應該要能存取來源資料庫引擎。 [深入了解](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> 如果資料庫機器前面有防火牆，請新增規則以允許存取資料庫，以及允許透過 SMB 連接埠 445 存取檔案。<br/><br/> 用來連線至來源 SQL Server 和目標受控執行個體的認證，必須是 sysadmin 伺服器角色的成員。<br/><br/> 內部部署資料庫中必須有可供 DMS 用來備份來源資料庫的網路共用。<br/><br/> 請確定執行來源 SQL Server 執行個體的服務帳戶擁有網路共用的寫入權限。<br/><br/> 請記下擁有網路共用完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬這些使用者認證，以便將備份檔案上傳至 Azure 儲存體容器。<br/><br/> SQL Server Express 安裝程序預設會將 TCP/IP 通訊協定設定為**停用**。 請務必將其啟用。


## <a name="scenario-steps"></a>案例步驟

以下說明 Contoso 要如何設定部署：

> [!div class="checklist"]
> * **步驟 1：設定 SQL Azure 受控執行個體**：他們需要預先建立受控執行個體以作為內部部署 SQL Server 資料庫的遷移目的地。
> * **步驟 2：準備 DMS**：他們需要註冊資料庫移轉提供者、建立執行個體，然後再建立 DMS 專案。 此外，還需要設定 DMS 的 SA URI。 共用存取簽章 (SAS) 統一資源識別項 (URI) 可提供儲存體帳戶資源的委派存取權，讓您對儲存體物件授與有限的權限。 他們會設定 SAS URI，讓 DMS 可存取儲存體帳戶容器 (此為服務在上傳 SQL Server 備份檔案時的上傳目的地)。
> * **步驟 3：針對 Site Recovery 準備 Azure**：針對 Site Recovery，他們必須建立一個 Azure 儲存體帳戶來保存複寫的資料，還要建立一個復原服務保存庫。
> * **步驟 4：針對 Site Recovery 準備內部部署 VMware**：Contoso 會準備帳戶以便探索 VM 和安裝代理程式，並準備在容錯移轉後連線至 Azure VM。
> * **步驟 5：複寫 VM**：為了設定複寫，他們會設定 Site Recovery 來源和目標環境、設定複寫原則，並開始將 VM 複寫至 Azure 儲存體。
> * **步驟 6：使用 DMS 遷移資料庫**：他們現在可以遷移資料庫。
> * **步驟 7：使用 Site Recovery 遷移 VM**：他們會執行測試容錯移轉，確定一切都能正常運作，然後執行完整的容錯移轉，以便將 VM 遷移至 Azure。


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>步驟 1：準備 Azure SQL 受控執行個體

若要設定 Azure SQL 受控執行個體，Contoso 需要一個子網路：

- 此子網路必須是專用的。 它必須空白，未包含任何其他雲端服務，且不得為閘道子網路。
- 建立受控執行個體後，就不得在該子網路中新增資源。
- 子網路不得有相關聯的 NSG。
- 子網路必須有使用者路由表 (UDR)，其中以 0.0.0.0/0 下一個躍點網際網路作為指派給它的唯一路由。 
- 選擇性自訂 DNS：如果在 VNet 上指定自訂 DNS，則必須將 Azure 的遞迴解析程式 IP 位址 (例如 168.63.129.16) 新增至清單。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- 子網路不得有相關聯的服務端點 (儲存體或 SQL)。 虛擬網路上應該停用服務端點。
- 子網路必須至少具有 16 個 IP 位址。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances)如何調整受控執行個體子網路的大小。
- 在其混合式環境中，需要有自訂 DNS 設定。 Contoso 會將 DNS 設定配置為使用他們其中一或多部 Azure DNS 伺服器。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) DNS 自訂。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>設定受控執行個體的虛擬網路

Contoso 會按照下列方式設定 VNet： 

1. Contoso 會在主要美國東部 2 區域的 ContosoNetworkingRG 資源群組中建立新的 VNet (VNET-SQLMI-EU2)。
2. Contoso 會指派 10.235.0.0/24 位址空間，確保範圍不會與 Contoso 企業中的任何其他網路重疊。
2. 他們會將兩個子網路新增到網路：
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29)。 此子網路會用來將目錄連結到受控執行個體 (SQLMI)。

    ![受控執行個體網路](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. 部署 VNet 和子網路之後，Contoso 會將網路對等互連，如下所示：

    - 對等互連 VNET-SQLMI-EUS2 與 VNET-HUB-EUS2 (美國東部 2 的中樞 VNet)。
    - 對等互連 VNET-SQLMI-EUS2 與 VNET-PROD-EUS2 (生產網路)。

    ![網路對等互連](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso 可設定自訂 DNS 設定。 DNS 會先指向其 Azure DC， 而後指向 Azure DNS。 Contoso Azure DC 的位置如下所示：

    - 位於在美國東部 2 生產網路 (VNET-PROD-EUS2) 中的 PROD-DC-EUS2 子網路。
    - CONTOSODC3 位址：10.245.42.4
    - CONTOSODC4 位址：10.245.42.5
    - Azure DNS 解析程式：168.63.129.16

     ![網路 DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**需要其他協助？**

- 針對 Azure SQL 受控執行個體[取得概觀](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances)如何為 SQL 受控執行個體建立 VNet。
- [了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)如何設定對等互連。
- [了解](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)如何更新 Azure AD DNS 設定。



### <a name="set-up-routing"></a>設定路由

受控執行個體會置於私人 VNET 中，因此 Contoso 需要路由表，它才能與 Azure 管理服務通訊。 如果它無法與管理它的服務通訊，則會變成無法存取。

- 路由表包含一組規則 (路由)，指定從受控執行個體傳送的封包應如何在 VNet 中路由傳送。
- 路由表會與受控執行個體部署所在的子網路相關聯。 每個離開子網路的封包會依據相關聯的路由表進行處理。
- 一個子網路只能與單一路由資料表相關聯。
- 在 Microsoft Azure 中建立路由表，沒有任何額外的費用。

1. Contoso 會建立使用者定義的路由表。 此路由表會建立於 ContosoNetworkingRG 資源群組中。

    ![路由表](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. 若要符合受控執行個體需求，在部署路由表 (MIRouteTable) 之後，Contoso 會新增位址前置詞為 0.0.0.0/0 的路由，並且將 [下一個躍點類型] 設定為 [網際網路]。

    ![路由表前置詞](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso 會讓路由表與 SQLMI-DB-EUS2 子網路 (在 VNET-SQLMI-EUS2 網路中) 建立關聯。 

    ![路由表子網路](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**需要其他協助？**

[了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route)如何設定受控執行個體的路由。

### <a name="create-a-managed-instance"></a>建立受控執行個體

Contoso 現在可以佈建 SQL Database 受控執行個體。

1. 受控執行個體會提供一個商務應用程式，所以 Contoso 將它部署在其主要美國東部 2 區域的 ContosoRG 資源群組中。 
2. 他們會選取執行個體的定價層、大小計算和儲存體。 [深入了解](https://azure.microsoft.com/pricing/details/sql-database/managed/)定價。

    ![受控執行個體](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. 部署受控執行個體之後，ContosoRG 資源群組中會出現兩個新的資源：

    - 虛擬叢集 (假使您有多個受控執行個體)。
    - SQL Server 受控執行個體。 

    ![受控執行個體](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**需要其他協助？**

[了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)如何佈建受控執行個體。

## <a name="step-2-prepare-dms"></a>步驟 2：準備 DMS

若要準備 DMS，Contoso 必須執行下列動作：

- 在 Azure 中註冊 DMS 提供者
- 將 Azure 儲存體存取權給提供 DMS，以便上傳用來遷移資料庫的備份檔案。 他們可藉由建立 blob 儲存體容器來達成，並且為其產生共用存取簽章 (SAS) URI。 
- 建立 DMS 專案。


完成如下所示的步驟：

1. Contoso 會在其訂用帳戶之下註冊資料庫移轉提供者。
    ![DMS 註冊](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. 他們會建立儲存體 blob 容器，並產生 SAS URI，以便 DMS 存取它。

    ![SAS URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. 最後，他們會建立 DMS 執行個體。 

    ![DMS 執行個體](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso 會將 DMS 執行個體置於 VNET-PROD-DC-EUS2 VNet 的 PROD-DC-EUS2 子網路中。
    - 他們將它放在該處，是因為它必須在可透過 VPN 閘道存取內部部署 SQL Server VM 的 VNet 中。
    - VNET-PROD-EUS2 會與 VNET-HUB-EUS2 對等互連，並且能夠使用遠端閘道。  這會確保 DMS 可以視需要進行通訊。

        ![DMS 網路](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**需要其他協助？**
- [了解](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)如何設定 DMS。
- [深入了解](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2)如何建立和使用 SAS。


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>步驟 3：針對 Site Recovery 服務準備 Azure

Contoso 需要一些 Azure 元素，才能夠設定 Site Recovery 以便移轉其 Web 層 VM (WEBMV)。

- 一個 VNet，容錯移轉的資源位於其中。
- 一個 Azure 儲存體帳戶，用來保存複寫的資料。 
- Azure 中的一個復原服務保存庫。

他們會按照下列方式設定 Site Recovery：

1. VM 是 SmartHotel 應用程式的 Web 前端，所以他們會將 VM 容錯移轉至主要美國東部 2 區域中現有的生產網路 (VNET-PROD-EUS2) 和子網路 (PROD-FE-EUS2)。 他們會在[部署 Azure 基礎結構](contoso-migration-infrastructure.md)時設定此網路。
2. 他們會建立 Azure 儲存體帳戶 (contosovmsacc20180528)。 他們會使用一般用途的帳戶，配備標準儲存體和 LRS 複寫。

    ![Site Recovery 儲存體](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. 網路和儲存體帳戶準備就緒之後，Contoso 現在即可建立保存庫 (ContosoMigrationVault)，然後將它放在美國東部 2 號主要區域的 ContosoFailoverRG 資源群組中。

    ![復原服務保存庫](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**需要其他協助？**

[了解](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)如何針對 Site Recovery 設定 Azure。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>步驟 4：針對 Site Recovery 準備內部部署 VMware

若要針對 Site Recovery 準備 VMware，Contoso 必須執行下列作業：

- 在 vCenter Server 或 vSphere ESXi 主機上，準備一個用來將 VM 探索自動化的帳戶。
- 準備一個帳戶，其允許在您想要複寫的 VMware VM 上自動安裝行動服務。
- 準備內部部署 VM，以便在容錯移轉之後於建立時連線到 Azure VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>準備帳戶以進行自動探索

Site Recovery 需要存取 VMware 伺服器才能：

- 自動探索 VM。 需要至少一個唯讀帳戶。
- 協調複寫、容錯移轉和容錯回復。 您需要可執行建立和移除磁碟以及開啟 VM 等作業的帳戶。

Contoso 會按照下列方式設定帳戶：

1. 在 vCenter 層級建立一個角色。
2. 將必要權限指派給該角色。

**需要其他協助？**

[深入了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)如何建立和指派自動探索所需的角色。

### <a name="prepare-an-account-for-mobility-service-installation"></a>準備一個用來安裝行動服務的帳戶

行動服務必須安裝在您要複寫的 VM 上。

- 當您啟用 VM 的複寫功能時，Site Recovery 可以自動推送安裝此元件。
- 若要自動推送安裝，您必須準備一個可供 Site Recovery 用來存取 VM 的帳戶。
- 當您在 Azure 主控台中設定複寫時，要指定此帳戶。
- 您需要有權限可以在 VM 上安裝的網域或本機帳戶。

**需要其他協助？**

[深入了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)建立行動服務推送安裝的帳戶。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>準備在容錯移轉後連接到 Azure VM

容錯移轉至 Azure 之後，Contoso 希望能夠連線到 Azure 中複寫的 VM。 若要這樣做，他們必須在執行移轉之前，在內部部署 VM 上執行下列步驟： 

1. 為了透過網際網路存取，他們會在容錯移轉前在內部部署 VM 上啟用 RDP，並確定已針對 [公用] 設定新增 TCP 和 UDP 規則，而且已在 [Windows 防火牆] > [允許的應用程式] 中針對所有設定檔允許 RDP。
2. 為了透過站對站 VPM 存取，他們會在內部部署機器上啟用 RDP，並且在 [Windows 防火牆]  ->  [允許的應用程式和功能] 中針對 [網域] 和 [私人] 網路允許 RDP。
3. 他們會將內部部署 VM 的作業系統 SAN 原則設定成 **OnlineAll**。

此外，當他們執行容錯移轉時，需要檢查以下各項：

- 觸發容錯移轉時，VM 上不應該有任何擱置的 Windows 更新。 如果有，在更新完成之前，他們將無法登入虛擬機器。
- 容錯移轉之後，他們應勾選 [開機診斷] 以檢視 VM 的螢幕擷取畫面。 如果這個不起任何作用，則應檢查 VM 是否正常執行，並且檢閱這些[疑難排解祕訣](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>步驟 5：使用 Site Recovery 將內部部署 VM 複寫至 Azure

在執行移轉至 Azure 之前，Contoso 必須設定複寫，然後為其內部部署 VM 啟用複寫。

### <a name="set-a-replication-goal"></a>設定複寫目標

1. 在保存庫的保存庫名稱 (ContosoVMVault) 底下，他們會設定一個複寫目標 ([開始使用] > [Site Recovery] > [準備基礎結構]。
2. 他們會指定自己的機器都位於內部部署環境、都是 VMware VM，以及想要複寫至 Azure。

    ![複寫目標](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>確認部署規劃

若要繼續，他們必須選取 [是，我已經完成]，確認自己已經完成部署規劃。 在此部署中，Contoso 只會移轉單一 VM，所以不需要部署規劃。

### <a name="set-up-the-source-environment"></a>設定來源環境

他們現在必須設定其來源環境。 若要這樣做，他們需要下載 OVF 範本，然後用它來將組態伺服器與其相關聯的元件部署為高可用性、內部部署 VMware VM。 伺服器上的元件包括︰

- 設定伺服器，會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器，可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。
- 處理序伺服器也會在您要複寫的 VM 上安裝行動服務，並且在內部部署 VMware VM 上執行自動探索。
- 建立並啟動組態伺服器 VM 之後，Contoso 可以在保存庫中註冊它。


Contoso 會按照下列方式，執行這些步驟：

1. 他們會從 Azure 入口網站 ([準備基礎結構] > [來源] > [組態伺服器]) 下載 OVF 範本。
    
    ![下載 OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. 他們將範本匯入 VMware，以便建立和部署 VM。

    ![OVF 範本](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  當他們第一次啟動 VM 時，就像是在安裝 Windows Server 2016 一樣。 他們接受授權合約，並輸入系統管理員密碼。
4. 安裝完成之後，他們會以系統管理員身分登入 VM。 第一次登入時，根據預設，Azure Site Recovery 設定工具會啟動。
5. 在這個工具中，他們會指定一個名稱，用於在保存庫中註冊組態伺服器。
6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，他們會選取 [登入] 以登入 Azure 訂用帳戶。 認證必須能夠存取將要在其中註冊組態伺服器的保存庫。 

    [註冊組態伺服器](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. 此工具會執行一些設定工作，而後重新開機。 他們再次登入機器，組態伺服器管理精靈會自動啟動。
8. 在精靈中，他們會選取接收複寫流量的 NIC。 這項設定調整好之後，便無法變更。
9. 他們選取訂用帳戶、資源群組，以及在其中註冊組態伺服器的保存庫。
        ![保存庫](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. 他們會接著下載並安裝 MySQL Server 和 VMWare PowerCLI。 然後，他們會驗證伺服器設定。
11. 驗證之後，他們會指定 vCenter 伺服器或 vSphere 主機的 FQDN 或 IP 位址。 他們會保留預設的連接埠，並且在 Azure 中為 vCenter Server 指定易記名稱。
12. 他們必須指定先前所建立的帳戶，以便 Site Recovery 自動探索可用於複寫的 VMware VM。 
13. 他們會在啟用複寫時，指定用於自動安裝行動服務的認證。 至於 Windows 機器，此帳戶需具備 VM 的本機系統管理員權限。 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. 註冊完成後，Contoso 會在 Azure 入口網站中再次確認組態伺服器和 VMware 伺服器都已列在保存庫中的 [來源] 頁面上。 探索可能需要 15 分鐘以上的時間。 
8. Site Recovery 接下來會使用指定的設定來連線至 VMware 伺服器並探索 VM。

### <a name="set-up-the-target"></a>設定目標

Contoso 現在必須設定目標複寫環境。

1. 在 [準備基礎結構] > [目標] 中，他們會選取目標設定。
2. Site Recovery 會確認在指定的目標中有 Azure 儲存體帳戶和網路。

### <a name="create-a-replication-policy"></a>建立複寫原則

設定好來源和目標之後，Contoso 即可建立複寫原則，並將讓它與組態伺服器建立關聯。

1. 在 [準備基礎結構] > [複寫設定] > [複寫原則] >  [建立和關聯] 中，他們會建立一個 **ContosoMigrationPolicy** 原則。
2. 他們會使用預設值：
    - **RPO 閾值**：預設值是 60 分鐘。 這個值可定義復原點的建立頻率。 連續複寫超過此限制時會產生警示。
    - **復原點保留**。 預設值為 24 小時。 這個值會指定每個復原點的保留週期有多長。 複寫的 VM 可以還原至一個週期內的任何時間點。
    - **應用程式一致快照頻率**。 預設值是一小時。 這個值會指定應用程式一致快照的建立頻率。
 
        ![建立複寫原則](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. 此原則會自動與設定伺服器產生關聯。 

    ![關聯複寫原則](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**需要其他協助？**

- 您可以到[設定內部部署 VMware VM 的災害復原](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)，閱讀所有步驟的詳細逐步解說。
- 您會得到詳細的操作說明，協助您[設定來源環境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署組態伺服器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[指定複寫設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。

### <a name="enable-replication"></a>啟用複寫

Contoso 現在可以開始複寫 WebVM。

1. 在 [複寫應用程式] > [來源] > [+複寫] 中，他們會選取來源設定。
2. 他們表示想要啟用虛擬機器、選取 vCenter 伺服器和組態伺服器。

 ![啟用複寫](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. 他們現在會指定目標設定，包括 Azure VM 在容錯移轉之後所在的資源群組和網路，以及要儲存所複寫資料的儲存體帳戶。

     ![啟用複寫](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso 會選取 WebVM 進行複寫。 Site Recovery 會在已啟用複寫的每個 VM 上安裝行動服務。 

    ![啟用複寫](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso 會確認已選取正確的複寫原則，並針對 WEBVM 啟用複寫。 他們會在 [作業] 中追踨複寫進度。 執行 [完成保護] 作業之後，機器即準備好進行容錯移轉。
6. 在 Azure 入口網站的 [程式集] 中，Contoso 可以看到 VM 複寫至 Azure 的結構。

    ![基礎結構檢視](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**需要其他協助？**

您可以在[啟用複寫](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)中閱讀所有步驟的完整逐步解說。

## <a name="step-6-migrate-the-database-with-dms"></a>步驟 6：使用 DMS 遷移資料庫

Contoso 必須建立 DMS 專案，然後遷移資料庫。

### <a name="create-a-dms-project"></a>建立 DMS 專案
1. 他們會建立 DMS 專案。 他們會將來源伺服器類型指定為 SQL Server，並以 Azure SQL Database 受控執行個體作為目標。

     ![DMS 專案](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 建立專案後，移轉精靈隨即開啟。

### <a name="migrate-the-database"></a>遷移資料庫 

1. 在移轉精靈中，Contoso 會指定內部部署資料庫所在的來源 VM，以及用來存取它的認證。

    ![DMS 來源](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 他們會選取要遷移的資料庫 (SmartHotel.Registration)。

    ![DMS 來源資料庫](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. 他們會指定 Azure 中的受控執行個體名稱作為目標，以及存取認證。

    ![DMS 目標](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. 然後，在 [+ 新增活動] > [執行移轉] 中，他們會指定下列設定來執行移轉：
    - 來源和目標認證。
    - 要遷移的資料庫。
    - 他們在內部部署 VM 上建立的網路共用。 DMS 會將來源備份擷取到此共用。
        - 執行來源 SQL Server 執行個體的服務帳戶必須擁有此共用的寫入權限。
        - 請指定共用的 FQDN 路徑。
    - SAS URI，此 URI 可供 DMS 存取儲存體帳戶容器，而服務會將備份檔案上傳至該容器以供移轉。

        ![DMS 設定](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 他們會儲存移轉，然後加以執行。
6. 在 [概觀] 中，他們會監視移轉狀態。

    ![DMS 監視器](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 移轉完成後，他們會確認受控執行個體上有目標資料庫。

    ![DMS 監視器](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>步驟 7：使用 Site Recovery 遷移 VM

Contoso 會執行一次快速的測試容錯移轉，然後遷移 VM。

### <a name="run-a-test-failover"></a>執行測試容錯移轉

在移轉 WEBVM 之前，測試容錯移轉有助於確保一切運作正常。 

1. 他們會針對最新的可用時間點 (**最近處理的**) 執行一次測試容錯移轉。
2. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]。 即使關機失敗，仍會繼續容錯移轉。 
3. 執行測試容錯移轉： 

    - 系統會執行先決條件檢查，以確保所有移轉所需的條件都已準備就緒。
    - 容錯移轉會處理資料，以便建立 Azure VM。 若選取最新的復原點，則會根據資料建立復原點。
    - 將會使用先前步驟中處理的資料來建立 Azure VM。
3. 容錯移轉完成後，Azure VM 複本會出現在 Azure 入口網站中。 他們會確認一切運作正常。 VM 為適當的大小、其已連線到正確的網路，而且正在執行中。 
4. 確認測試容錯移轉之後，Contoso 會清除容錯移轉，並記錄與儲存任何觀察到的結果。 

### <a name="migrate-the-vm"></a>移轉 VM

1. 驗證測試容錯移轉如預期般正常運作之後，Contoso 會針對移轉建立一個復原方案。 他們會將 WEBVM 新增至此方案。

     ![復原計畫](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. 然後，他們會對此方案執行容錯移轉。 他們會選取最新的復原點，然後指定 Site Recovery 應該在嘗試觸發容錯移轉之前，先關閉內部部署 VM。

    ![容錯移轉](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. 容錯移轉之後，Contoso 會確認 Azure VM 會如預期般出現在 Azure 入口網站中。

   ![復原計畫](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. 在 Azure 中確認 VM 之後，他們會完成移轉程序、停止 VM 複寫，以及停止 VM 的 Site Recovery 計費。

    ![容錯移轉](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>更新連接字串

在移轉程序的最後一個步驟中，Contoso 會將應用程式的連接字串更新為指向在其 SQL MI 上執行的遷移後資料庫。

1. 在 Azure 入口網站中，他們會按一下 [設定] > [連接字串] 來尋找連接字串。

    ![容錯移轉](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. 他們會使用 SQL 受控執行個體的使用者名稱與密碼來更新此字串。
3. 設定此字串之後，他們會取代其應用程式的 web.config 檔案中目前的連接字串。
4. 更新該檔案並加以儲存後，他們會在 WEBVM 上重新啟動 IIS。 他們會從命令提示字元使用 **IISRESET /RESTART** 執行此動作。
5. 重新啟動 IIS 後，應用程式會使用在 SQL 受控執行個體上執行的資料庫。
6. 此時，Contoso 可以關閉 SQLVM 內部部署機器，移轉便隨之完成。

**需要其他協助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)如何執行測試容錯移轉。 
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)如何建立復原方案。
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)如何容錯移轉至 Azure。

## <a name="clean-up-after-migration"></a>移轉之後進行清除

移轉完成時，SmartHotel 應用程式正在 Azure VM 上執行，而 SmartHotel 資料庫可適用於 Azure SQL 受控執行個體。  

現在，Contoso 必須執行一些清除，如下所示：  

- 從 vCenter 清查中移除 WEBVM 機器。
- 從 vCenter 清查中移除 SQLVM 機器。
- 從本機備份作業移除 WEBVM 和 SQLVM。
- 更新內部文件以顯示 WEBVM 的新位置和 IP 位址。
- 從文件中移除 SQLVM。 或者，他們可加以標記，以顯示為已刪除且已不在 VM 清查中。
- 檢閱與已解除委任 VM 互動的任何資源，並更新任何相關的設定或文件，以反映新的組態。

## <a name="review-the-deployment"></a>檢閱部署

對於 Azure 中的遷移後資源，Contoso 必須能發揮一切功能並保護其新的基礎結構。

### <a name="security"></a>安全性

Contoso 安全性小組會檢閱 Azure VM 和 SQL 受控執行個體，判斷其實作是否有任何的安全疑慮。

- 他們會檢閱 VM 的網路安全性群組 (NSG) 來控制存取權。 NSG 可用來確保只可以傳遞該應用程式允許的流量。
- 他們也會考慮使用 Azure 磁碟加密和 Azure KeyVault 來保護磁碟上的資料。
- 他們會在 SQL 受控執行個體 (SQLMI) 上啟用威脅偵測。 如果偵測到威脅，他們會將警示傳送至其安全性小組/服務台系統。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![受控執行個體安全性](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[深入了解](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) VM 的安全措施。

### <a name="backups"></a>備份
Contoso 即將使用 Azure 備份服務來備份 WEBVM 上的資料。 [深入了解](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>授權和成本最佳化

1. Contoso 具有 WEBVM 的現有授權，而且會利用 Azure Hybrid Benefit。  他們會轉換現有的 Azure VM，以便充分利用這個定價。
2. Contoso 會啟用 Microsoft 子公司 Cloudyn 授權的 Azure 成本管理。 它是一種多雲端成本管理解決方案，可協助您利用和管理 Azure 和其他雲端資源。  [深入了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 


## <a name="conclusion"></a>結論

在本文中，Contoso 會使用 Site Recovery 服務，將應用程式前端 VM 移轉至 Azure，以便重新裝載 SmartHotel。 他們使用了 DMS 將內部部署資料庫遷移至 Azure SQL 受控執行個體。

## <a name="next-steps"></a>後續步驟

在這系列的下一篇文章中，我們會說明 Contoso 如何只使用 Azure Site Recovery 服務，將 SmartHotel 應用程式重新裝載至 Azure VM。

