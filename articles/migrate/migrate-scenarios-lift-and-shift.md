---
title: 使用 Azure DMS 和 Site Recovery 將內部部署工作負載遷移至 Azure | Microsoft Docs
description: 了解如何準備 Azure，以使用 Azure 資料庫移轉服務和 Azure Site Recovery 服務來移轉內部部署機器。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182238"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>案例 2：隨即轉移至 Azure

Contoso 公司考慮要移轉至 Azure。 為了進行試用，他們想要對小型的內部部署旅遊應用程式進行評估，並將其遷移至 Azure。 這個旅遊應用程式分為兩層，其中一個 VM 上有 Web 應用程式在執行，第二個 VM 上則有 SQL Server 資料庫。 應用程式部署在 VMware 中，環境則是由 vCenter Server 來管理。 

在[案例 1：對移轉至 Azure 進行評估](migrate-scenarios-assessment.md)中，他們使用 Data Migration Assistant (DMA) 來評估應用程式的 SQL Server 資料庫。 此外，他們還使用 Azure Migrate 服務來評估應用程式的 VM。 現在，在此案例中，他們想要在成功完成評估後，使用 Azure 資料庫移轉服務 (DMS) 將資料庫遷移至 Azure SQL 受控執行個體，以及使用 Azure Site Recovery 服務將內部部署機器遷移至 Azure VM。

如果您想要試用[案例 1](migrate-scenarios-assessment.md)，然後再使用這裡所說明的旅遊應用程式來試用此案例，您可以從 [github](https://github.com/Microsoft/SmartHotel360) 下載此應用程式。



**服務** | **說明** | **成本**
--- | --- | ---
[資料庫管理服務](https://docs.microsoft.com/azure/dms/dms-overview) | DMS 能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 | 此服務目前為公開預覽版 (2018 年 4 月)，在預覽期間內可免費使用。<br/><br/> 請注意，在公開預覽版中，DMS 僅在[一些區域](https://docs.microsoft.com/azure/dms/dms-overview)提供使用。
[Azure SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | 受控執行個體是一種受控資料庫服務，可代表 Azure 雲端中的完全受控 SQL Server 執行個體。 受控執行個體會與最新版的 SQL Server 資料庫引擎共用相同的程式碼，並擁有最新的功能、效能增強功能和安全性修補程式。 | 使用在 Azure 中執行的 Azure SQL Database 受控執行個體會根據所用容量產生費用。 [深入了解](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 此服務可協調和管理 Azure VM 以及內部部署 VM 和實體伺服器的移轉和災害復原。  | 複寫至 Azure 的期間會產生 Azure 儲存體費用。  發生容錯移轉時，系統會建立 Azure VM 並產生費用。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/)費用和定價。

在此案例中，我們會建立站對站 VPN，讓 DMS 可以連線至內部部署資料庫、在 Azure 中建立 Azure SQL 受控執行個體，並遷移資料庫。 在 Site Recovery 中，我們會準備內部部署 VMware 環境、設定複寫，以及將 VM 遷移至 Azure。  


> [!IMPORTANT]
> 您必須已在 SQL 受控執行個體有限公開預覽版中註冊。 您必須有 Azure 訂用帳戶才可以[註冊](https://portal.azure.com#create/Microsoft.SQLManagedInstance)。 註冊可能需要幾天的時間才能完成，因此請務必在開始部署此案例之前完成註冊。

## <a name="architecture"></a>架構

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

在此情節中：

- Contoso 是代表一般企業組織的虛構名稱。 Contoso 想要評估並遷移其兩層式內部部署旅遊應用程式。
- Contoso 有內部部署資料中心 (contoso-datacenter) 以及內部部署網域控制站 (**contosodc1**)。
- 內部旅遊應用程式分為兩個 VM 層級 (分別是 WEBVM 和 SQLVM)，而且位於 VMware ESXi 主機 (**contosohost1.contoso.com**)。
- VMware 環境是由 VM 上所執行的 VCenter Server (**vcenter.contoso.com**) 進行管理。

## <a name="prerequisites"></a>先決條件

如果您想要執行此案例，您應該具備以下項目。

需求 | 詳細資料
--- | ---
**Azure 訂用帳戶** | 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果您建立免費帳戶，您就是訂用帳戶的管理員，並可執行所有動作。<br/><br/> 如果您使用現有訂用帳戶，而且您不是系統管理員，則需要與系統管理員合作，讓其指派擁有者或參與者權限給您。<br/><br/> 如果您需要更細微的權限，請檢閱[此文章](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**站台復原 (內部部署)** | 執行 5.5、6.0 或 6.5 版的內部部署 vCenter 伺服器<br/><br/> 執行 5.5、6.0 或 6.5 版的 ESXi 主機<br/><br/> 一或多部在 ESXi 主機上執行的 VMware VM。<br/><br/> VM 必須符合 [Azure 需求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。<br/><br/> 支援的[網路](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[儲存體](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)組態。
**DMS** | 針對 DMS，您需要[相容的內部部署 VPN 裝置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。<br/><br/> 您必須能夠設定內部部署 VPN 裝置。 此裝置必須有一個對外開放的公用 IPv4 位址，而且此位址不得位於 NAT 裝置後面。<br/><br/> 請確定您可以存取內部部署 SQL Server 資料庫。<br/><br/> Windows 防火牆應該要能存取來源資料庫引擎。 [深入了解](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> 如果資料庫機器前面有防火牆，請新增規則以允許存取資料庫，以及允許透過 SMB 連接埠 445 存取檔案。<br/><br/> 用來連線至來源 SQL Server 和目標受控執行個體的認證，必須是 sysadmin 伺服器角色的成員。<br/><br/> 內部部署資料庫中必須有可供 DMS 用來備份來源資料庫的網路共用。<br/><br/> 請確定執行來源 SQL Server 執行個體的服務帳戶擁有網路共用的寫入權限。<br/><br/> 請記下擁有網路共用完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬這些使用者認證，以便將備份檔案上傳至 Azure 儲存體容器。<br/><br/> SQL Server Express 安裝程序預設會將 TCP/IP 通訊協定設定為**停用**。 請務必將其啟用。


# <a name="scenario-overview"></a>案例概觀

以下是所要執行的步驟：

> [!div class="checklist"]
> * **步驟 1：設定站對站 VPN 連線**：DMS 會透過 VPN 站對站連線來連線至內部部署資料庫。 我們會建立供 VPN 連線使用的虛擬網路，並於稍候將此網路用於 Site Recovery。 您所建立的網路必須位於您在建立復原服務保存庫時所在的區域。
> * **步驟 2：設定 SQL Azure 受控執行個體**：您需要預先建立受控執行個體以作為內部部署 SQL Server 資料庫的遷移目的地。
> * **步驟 3：設定 DMS 的 SAS URI**：共用存取簽章 (SAS) 統一資源識別項 (URI) 可提供儲存體帳戶資源的委派存取權，讓您對儲存體物件授與有限的權限。 請設定 SAS URI，讓 DMS 可存取儲存體帳戶容器 (此為服務在上傳 SQL Server 備份檔案時的上傳目的地)。
> * **步驟 4：準備 DMS**：您要註冊資料庫移轉提供者、建立執行個體，然後再建立 DMS 專案。
> * **步驟 5：針對 Site Recovery 準備 Azure**：您要建立 Azure 儲存體帳戶來保存複寫的資料。
> * **步驟 6：針對 Site Recovery 準備內部部署 VMware**：您要準備帳戶以便探索 VM 和安裝代理程式，並準備在容錯移轉後連線至 Azure VM。 
> * **步驟 7：複寫 VM**：您要設定 Site Recovery 的來源和目標環境、設定複寫原則，並開始將 VM 複寫至 Azure 儲存體。
> * **步驟 8：使用 DMS 遷移資料庫**：執行資料庫移轉。
> * **步驟 9：使用 Site Recovery 遷移 VM**：執行容錯移轉，以將 VM 遷移至 Azure。


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>步驟 1：設定站對站 VPN 連線

若要準備進行 VPN 站對站連線，您需要設定虛擬網路和子網路、建立虛擬網路的 VPN 閘道，並設定區域網路閘道，讓 Azure 知道如何連線至內部部署 VPN。 接著，建立並確認站對站連線。

### <a name="set-up-a-virtual-network"></a>設定虛擬網路

建立 Azure 虛擬網路，為 DMS 提供連往內部部署 SQL Server 的站對站連線能力。


1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [建立資源]。 選取 [網路] > [虛擬網路]。
2. 在 [虛擬網路] > [選取部署模型] 中，選取 [Resource Manager] > [建立]
3. 在 [建立虛擬網路] > [名稱] 中，輸入唯一的網路名稱。 在本文案例中為 **ContosoVNET**。
4. 在 [位址空間] 中，新增 IP 位址範圍。 此範圍不得與內部部署位址空間重疊。
5. 在 [資源群組] 中，建立新的群組或選取現有群組。 在此案例中，我們會使用 **ContosoRG**。
6. 在 [位置] 中，選取要在其中建立虛擬網路的區域。 我們會使用 [美國東部 2]。
7. 在 [子網路] 中，新增第一個子網路名稱和位址範圍。 我們建立了 **Apps** 子網路。
8. 停用服務端點。

    ![建立虛擬網路](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. 選取 [釘選到儀表板] 以便日後可以輕鬆地找到網路，然後按一下 [建立]。
10. 虛擬網路的建立會耗用幾秒鐘時間。 建立好之後，在 Azure 入口網站儀表板中加以確認。
11. 確定虛擬網路中允許這些通訊連接埠：443、53、9354、445、12000。


### <a name="set-up-subnets"></a>設定子網路

Azure 網路中會有 4 個子網路：

![子網路清單](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

您已在建立網路時設定了第一個子網路 (Apps)。 現在，請建立其餘子網路。 VPN 閘道連線會使用閘道子網路，讓 Azure 透過 VPN 連線將流量傳送至內部部署網站。

1. 在虛擬網路的 [設定] 底下，按一下 [子網路] > [+子網路]。
2. 使用此位址範圍設定子網路 **Datamigration**，然後按一下 [確定]。

    ![資料子網路](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. 在 [+子網路] 中，使用此位址範圍設定子網路 **Identity**，然後按一下 [確定]。
    ![Identity 子網路](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. 在 [子網路] 中，按一下 [閘道子網路]，然後按一下 [確定]。
    - 此子網路包含 VPN 閘道會用於 VPN 連線的 IP 位址。
    - 系統會自動設定此子網路的名稱，以供 Azure 辨識。
    - 調整自動填入的位址範圍，使其符合內部部署子網路。 

    ![閘道子網路](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>設定虛擬網路閘道

1. 在入口網站頁面的左側，按一下 **+** 並且在搜尋中輸入「虛擬網路閘道」。 在 [結果] 中，按一下 [虛擬網路閘道]。
2. 在 [虛擬網路閘道] 頁面的底部，按一下 [建立]。
3. 在 [建立虛擬網路閘道] >  [名稱] 中，指定閘道物件的名稱。
4. 在 [閘道類型] 中，選取 [VPN]。
5. 在 [VPN 類型] 中，選取 [路由式]。
6. 在 [SKU] 中，從下拉式清單中選取閘道 SKU。 下拉式清單中所列的 SKU 取決於您選取的 VPN 類型。 請勿啟用主動-主動模式。 [深入了解](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) SKU。
7. 在 [虛擬網路] > [選擇虛擬網路] 中，新增您所建立的網路。 閘道會新增至此網路。
8. 在 [第一個 IP 組態] 中，指定公用 IP 位址。  VPN 閘道必須具有公用 IP 位址。 建立 VPN 閘道時，系統會將此位址動態指派給此資源。 目前只支援動態位址，但 IP 位址在指派後就不會改變。
    - 按一下 [建立閘道 IP 組態]。 在 [選擇公用 IP 位址] 中﹐按一下 [+新建]。
    - 在 [建立公用 IP 位址] 中，指定公用 IP 位址的名稱 (Contoso-Gateway)。 讓 SKU 保持為 [基本]，然後按一下 [確定] 以儲存變更。
        
    ![虛擬網路閘道](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. 按一下 [建立]  即可開始建立 VPN 閘道。 系統會驗證設定，且儀表板上會出現 [正在部署虛擬網路閘道]。

    ![驗證虛擬網路閘道](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
建立閘道可能需要長達 45 分鐘。 您可能需要重新整理入口網站頁面，才會看到已完成的狀態。

### <a name="set-up-a-local-network-gateway"></a>設定區域網路閘道

在 Azure 中設定區域網路閘道，以代表內部部署網站。

1. 在入口網站中，按一下 [+建立資源]。
2. 在搜尋方塊中輸入**區域網路閘道**，然後按 **Enter** 鍵進行搜尋。 在結果中，按一下 [區域網路閘道] > [建立]。
3. 在 [建立區域網路閘道] > [名稱] 中，指定供 Azure 用於區域網路閘道物件的名稱。
4. [IP 位址] 會指定 Azure 所要連線的內部部署 VPN 裝置公用 IP 位址。 此 IP 位址不應經過 NAT 處理，且 Azure 必須能夠連線到該位址。
5. 在 [位址空間] 中，輸入會透過 VPN 閘道路由至內部部署裝置的區域網路位址範圍。 請確定這些範圍不會與 Azure 虛擬網路上的範圍重疊。
6. [設定 BGP 設定] 不適用於此案例。
7. 在 [訂用帳戶] 中，確認訂用帳戶正確無誤。
8. 在 [資源群組] 中：選取之前用來建立網路的資源群組 (ContosoRG)。
9. 在 [位置] 中，選取建立虛擬網路時所在的區域。

    ![區域閘道](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. 按一下 [建立] 以建立區域閘道。

### <a name="configure-your-on-premises-vpn-device"></a>設定內部部署 VPN 裝置

需要在內部部署 VPN 裝置上設定的項目如下：

- 您剛才建立的 Azure 虛擬網路閘道公用 IPv4 位址。
- 預先共用的金鑰 (之前在建立 VPN 站對站連線時所使用的金鑰)。  

為了協助您設定內部部署 VPN 裝置：

- 視內部部署 VPN 裝置而定，您或許可以下載 VPN 裝置組態指令碼。 [深入了解](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript)。
- 檢閱[更實用的資源](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice)。

### <a name="create-the-vpn-connection"></a>建立 VPN 連線

現在，在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。

1. 在 [虛擬網路] > [概觀] > [已連線的裝置] 中開啟虛擬網路頁面。 
2. 按一下 [連線] > [+新增]。
3. 在 [新增連線] > [名稱] 中，指定連線的名稱。
4. 在 [連線類型] 中，選取 [站對站 (IPSec)]。
5. 因為您要從這個虛擬網路閘道連線，所以 [虛擬網路閘道] 值是固定的。
6. 在 [區域網路閘道] 中，指定您所建立的區域網路閘道。
7. 在 [共用金鑰] 中，指定與您要用於本機內部部署 VPN 裝置的值相符的金鑰。 範例中使用的是 'abc123'，但是您可以(且應該) 使用更為複雜的值。 重要的是，您在此指定的值必須與您在設定 VPN 裝置時指定的值相同。
8. [訂用帳戶]、[資源群組] 和 [位置] 的值是固定的。

    ![VPN 連線](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. 按一下 [確定] 來建立連線。 您會看到畫面上閃爍「正在建立連線」。
5. 在建立連線後，請於虛擬網路閘道的 [連線] 頁面上檢視連線。 狀態會從 [未知] > [正在連線] > [已成功]。

### <a name="verify-the-vpn-connection"></a>驗證 VPN 連線

在 Azure 入口網站中，您可以瀏覽至連線，以檢視傳統 VPN 閘道的連線狀態。 

1. 按一下 [所有資源]，然後瀏覽至虛擬網路閘道。
2. 在虛擬網路閘道頁面上，按一下 [連線]。 您可以看到每個連線的狀態。
3. 按一下連線名稱，然後檢視 [基本資訊] 中的詳細資訊。 當您成功建立連線時，狀態會是 [已成功] 和 [已連線]。

現在，請確認您可以透過 VPN 連線至 SQL Server VM。 使用遠端桌面連線，並連線至 VM 的 IP 位址。



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>步驟 2：準備 Azure SQL 受控執行個體

### <a name="set-up-a-virtual-network"></a>設定虛擬網路

在此案例中，您必須建立另一個專供 Azure SQL 受控執行個體使用的虛擬網路。  請注意下列需求：

- 若要建立受控執行個體，您需要有專用的子網路。
- 此子網路必須空白，未包含任何其他雲端服務，且不得為閘道子網路。 建立受控執行個體後，就不得在該子網路中新增資源。
- 子網路不得有相關聯的 NSG。
- 子網路必須有使用者路由表 (UDR)，其中以 0.0.0.0/0 下一個躍點網際網路作為指派給它的唯一路由。 
- 選擇性自訂 DNS：如果在 VNet 上指定自訂 DNS，則必須將 Azure 的遞迴解析程式 IP 位址 (例如 168.63.129.16) 新增至清單。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- 子網路不得有相關聯的服務端點 (儲存體或 SQL)。 虛擬網路上應該停用服務端點。
- 子網路必須至少具有 16 個 IP 位址。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances)如何調整受控執行個體子網路的大小。

設定虛擬網路，如下所示：

1.  在 Azure 入口網站中，按一下 [建立資源]。 
2. 選取 [網路] > [虛擬網路]。
3. 在 [虛擬網路] > [選取部署模型] 中，選取 [Resource Manager] > [建立]。
4. 在 [建立虛擬網路] > [名稱] 中，輸入唯一的網路名稱。 在本文案例中為 **ContosoVNETSQLMI**。
5. 在 [位址空間] 中新增 IP 位址範圍，如下所示。 此範圍不得與內部部署和 ContosoVNET 位址空間重疊。
6. 在 [資源群組] 中，建立新的群組或選取現有群組。 在此案例中，我們會使用 **ContosoRG**。
7. 在 [位置] 中，選取要在其中建立虛擬網路的區域。 我們會使用 [美國東部 2]。
8. 在 [子網路] 中，新增第一個子網路名稱和位址範圍。 我們建立了 **ManagedInstances** 子網路。
9. 停用服務端點。

    ![受控執行個體網路](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. 部署網路之後，您需要變更 DNS 設定。 在此案例中，DNS 會先指向 Identity 子網路中使用靜態私人 IP 位址 (172.16.3.4) 的網域控制站，然後指向 Azure DNS 解析程式 168.63.129.16。

    ![受控執行個體網路](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>設定路由

1. 按一下 Azure 入口網站左上角的 [建立資源]。 按一下 [路由表] > [建立在路由表上] 頁面。
2. 在 [建立路由表] > [名稱] 中，指定資料表的名稱。
3. 選取訂用帳戶、資源群組和位置。 讓 BGP 保持停用，然後按一下 [建立]。
    ![路由表](media/migrate-scenarios-lift-and-shift/route-table.png)

4. 將建立好的路由表開啟，然後按一下 [路由] > [+新增]。
5. 在 [新增路由] > [名稱] 中，指定路由名稱。
6. 在 [位址首碼] 中，指定 0.0.0.0/0。
7. 在 [下一個躍點類型] 中，選取 [網際網路]。 然後按一下 [確定] 。

     ![路由表](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>將路由套用至受控執行個體子網路

1. 開啟您所建立的虛擬網路。 按一下 [子網路] > your-subnet-name。
2. 按一下 [路由表] > your-table-name。 然後按一下 [儲存] 。

     ![路由表](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>建立受控執行個體

1. 按一下 [建立資源]，找出 [受控執行個體]，然後選取 [Azure SQL Database 受控執行個體 (預覽)]。
2. 按一下頁面底部的 [新增] 。
3. 在 [SQL 受控執行個體] 中選取訂用帳戶，並確認 [預覽條款] 顯示 [已接受]。
4. 在 [受控執行個體名稱] 中指定名稱。 
5. 指定使用者名稱和密碼以作為執行個體的系統管理員。
6. 選取執行個體的資源群組、位置和虛擬網路。
7. 按一下 [定價層] 來調整計算和儲存體的大小。 根據預設，執行個體會免費獲得 32 GB 的儲存體空間 (2018 年 4 月)。
8. 指定儲存體，以及虛擬核心的數目。
9. 按一下 [套用] 以儲存設定，然後按一下 [建立] 以部署受控執行個體。 若要檢視部署狀態，請按一下 [通知] 和 [部署進行中]。

    ![受控執行個體](media/migrate-scenarios-lift-and-shift/mi-1.png)

受控執行個體部署完成後，ContosoRG 資源群組中會出現兩個新的資源：受控執行個體的虛擬叢集，以及 SQL 受控執行個體。 兩者都位於「美國東部 2」位置。

![受控執行個體](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>步驟 3：取得 DMS 的 SAS URI

取得 SAS URI，讓 DMS 可以存取儲存體帳戶容器，把可用來將資料庫遷移至 Azure SQL Database 受控執行個體的備份檔案上傳。 

1. 開啟儲存體 Explorer (預覽)。
2. 在左窗格中，展開您要為其取得 SAS 的 Blob 容器所在的儲存體帳戶。 展開儲存體帳戶的 [Blob 容器] 。
3. 以滑鼠右鍵按一下容器，然後選取 [取得共用存取簽章]。

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. 在 [共用存取簽章] 中，指定您要用於資源的原則、開始和到期日期、時區和存取層級。 接著，按一下 [建立]。

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. 第二個對話方塊會顯示 Blob 容器，以及可用來存取儲存體資源的 URL 和 QueryStrings。 選取 [複製] 來複製值。 將值保存在安全的地方。 設定 DMS 時會用到這些值。

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>步驟 4：準備 DMS

您需要註冊資料庫移轉提供者，並建立執行個體。

### <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 在訂用帳戶中，選取 [資源提供者]。 
2. 搜尋「移轉」，然後在 Microsoft.DataMigration 的右邊，選取 [註冊]。 


### <a name="create-an-instance"></a>建立執行個體

1. 選取 [+ 建立資源]，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]。
2. 在 [Azure 資料庫移轉服務 (預覽)] 畫面上，選取 [建立]。
3. 指定服務的名稱、訂用帳戶、資源群組、虛擬網路和定價層。
4. 選取 [建立] 以建立服務。

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>步驟 5：針對 Site Recovery 服務準備 Azure

### <a name="set-up-a-virtual-network"></a>設定虛擬網路

您需要有 Azure 網路以供作為容錯移轉後所建立的 Azure VM 的所在位置，也需要 Azure 儲存體帳戶以供儲存複寫的資料。

- 為了進行此案例，我們會使用稍早為 DMS 所建立的 Azure 網路。
- 請注意，所使用的網路必須位於與 Site Recovery 保存庫相同的區域中。


### <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

Site Recovery 會將 VM 資料複寫至 Azure 儲存體。 當您從內部部署容錯移轉至 Azure 時，會從儲存體建立 Azure VM。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表上，選取 [新增] > [儲存體] > [儲存體帳戶]。
2. 在 [建立儲存體帳戶] 上，輸入帳戶的名稱。 在這些教學課程中，使用名稱 **contosovmsacct1910171607**。 此名稱必須是 Azure 中的唯一名稱，並介於 3 到 24 個字元，而且只能包含數字和小寫字母。
3. 在 [部署模型] 中選取 [Resource Manager]。
4. 在 [帳戶種類] 中選取 [一般用途]。 在 [效能] 中選取 [標準]。 請勿選取 Blob 儲存體。
5. 在 [複寫] 中，針對儲存體備援選取預設的 [讀取權限異地備援儲存體]。
6. 在 [訂用帳戶] 中，選取您要在其中建立新儲存體帳戶的訂用帳戶。
7. 在 [資源群組] 中，輸入新的資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在這些教學課程中，使用名稱 **ContosoRG**。
8. 在 [位置] 中，選取儲存體帳戶的地理位置。 儲存體帳戶與復原服務保存庫必須位於相同的區域。 在此案例中，我們會使用**美國東部 2** 區域。

   ![建立儲存體帳戶](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. 選取 [建立]  以建立儲存體帳戶。

### <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

1. 在 Azure 入口網站中，選取 [建立群組] > [監視 + 管理] > [備份和 Site Recovery]。
2. 在 [ **名稱**] 中，輸入保存庫的易記識別名稱。 在此教學課程中，使用 **ContosoVMVault**。
3. 在 [資源群組] 中，選取名為 **contosoRG** 的現有資源群組。
4. 在 [位置] 中，輸入 Azure 區域**美國東部 2**。
5. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板] > [建立]。
新的保存庫會出現在 [儀表板] > [所有資源] 上，以及 [復原服務保存庫] 主頁面上。

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>步驟 6：針對 Site Recovery 準備內部部署 VMware

若要針對 Site Recovery 準備 VMware，您必須執行下列作業：

- 在 vCenter Server 或 vSphere ESXi 主機上，準備一個用來將 VM 探索自動化的帳戶
- 準備一個用來在 VMware VM 上自動安裝行動服務的帳戶
- 如果您想要在容錯移轉之後連線至 Azure VM，請準備內部部署 VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>準備帳戶以進行自動探索

Site Recovery 需要存取 VMware 伺服器才能：

- 自動探索 VM。 需要至少一個唯讀帳戶。
- 協調複寫、容錯移轉和容錯回復。 您需要可執行建立和移除磁碟以及開啟 VM 等作業的帳戶。

遵循下列方式建立此帳戶：

1. 若要使用專用帳戶，請在 vCenter 層級建立一個角色。 指定角色的名稱，例如 **Azure_Site_Recovery**。
2. 將下表摘要說明的權限指派給角色。
3. 在 vCenter 伺服器或 vSphere 主機上建立使用者。 將角色指派給使用者。

**Task** | **角色/權限** | **詳細資料**
--- | --- | ---
**VM 探索** | 至少是唯讀使用者<br/><br/> 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。
**完整複寫、容錯移轉、容錯回復** |  建立具有必要權限的角色 (Azure_Site_Recovery)，然後將角色指派給 VMware 使用者或群組<br/><br/> 資料中心物件 –> 傳播至子物件、role=Azure_Site_Recovery<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。

### <a name="prepare-an-account-for-mobility-service-installation"></a>準備一個用來安裝行動服務的帳戶

行動服務必須安裝在您要複寫的 VM 上。

- 當您啟用 VM 的複寫功能時，Site Recovery 可以自動推送安裝此元件。
- 若要自動推送安裝，您必須準備一個可供 Site Recovery 用來存取 VM 的帳戶。
- 當您在 Azure 主控台中設定複寫時，要指定此帳戶。
- 您需要有權限可以在 VM 上安裝的網域或本機帳戶。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>準備在容錯移轉後連接到 Azure VM

在容錯移轉至 Azure 後，請從內部部署網路連線至 Azure 中已複寫的 VM。

若要在容錯移轉後使用 RDP 連線到 Windows VM，請執行下列作業：

1. 若要透過網際網路存取，請在內部部署 VM 上啟用 RDP，再進行容錯移轉。 確定已針對 [公用] 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。
2. 若要透過站對站 VPN 存取，請在內部部署機器上啟用 RDP。 您應該在 [Windows 防火牆] -> [允許的應用程式與功能] 中，針對 [網域] 和 [私人] 網路允許 RDP。
3. 確認作業系統的 SAN 原則已設為 [OnlineAll]。 [深入了解](https://support.microsoft.com/kb/3031135)。
4. 觸發容錯移轉時，VM 上不應該有任何擱置的 Windows 更新。 如果有，在更新完成之前，您將無法登入虛擬機器。
5. 在容錯移轉之後，於 Windows Azure VM 上，勾選 [開機診斷] 以檢視 VM 的螢幕擷取畫面。 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。



## <a name="step-7-replicate-vms-with-site-recovery"></a>步驟 7：使用 Site Recovery 複寫 VM

### <a name="select-a-replication-goal"></a>選取複寫目標

1. 在 [復原服務保存庫] 中，選取保存庫名稱 **ContosoVMVault**。
2. 在 [使用者入門] 中，選取 Site Recovery。 然後選取 [準備基礎結構]。
3. 在 [保護目標] > [您的電腦位於何處] 中，選取 [內部部署]。
4. 在 [您要將電腦複寫到何處] 中，選取 [複製到 Azure]。
5. 在 [您的電腦虛擬化了嗎] 中，選取 [是，使用 VMware vSphere Hyperviso]。 然後選取 [確定]。

    ![OVF 範本](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>確認部署規劃

在 [部署規劃] 的下拉式清單中按一下 [是，已完成]。

### <a name="set-up-the-source-environment"></a>設定來源環境


若要設定來源環境，您需要單一、高可用性、內部部署的電腦來裝載 Site Recovery 元件。 這些元件包含組態伺服器和處理序伺服器。

- 設定伺服器會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。
- 處理序伺服器也會在您要複寫的 VM 上安裝行動服務，並且在內部部署 VMware VM 上執行自動探索。


若要將組態伺服器設定為高可用性的 VMware VM：

- 下載已準備好的開放式虛擬化格式 (OVF) 範本。
- 將範本匯入至 VMware 以建立 VM。
- 設定組態伺服器，然後在保存庫中加以註冊。 

註冊之後，Site Recovery 會探索內部部署 VMware VM。



#### <a name="download-the-vm-template"></a>下載 VM 範本

1. 在保存庫中，移至 [準備基礎結構] > [來源]。
2. 在 [準備來源] 中，選取 [+設定伺服器]。

    ![下載範本](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. 在 [新增伺服器] 中，檢查 [VMware 的組態伺服器] 是否出現在 [伺服器類型] 中。
2. 下載設定伺服器的 OVF 範本。

    ![下載 OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  您可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)，下載最新版的設定伺服器範本。

#### <a name="import-the-template-in-vmware"></a>在 VMware 中匯入範本

1. 使用 VMware vSphere 用戶端登入 VMware vCenter 伺服器。
2. 在 [檔案] 功能表上，選取 [部署 OVF 範本] 以啟動 [部署 OVF 範本] 精靈。 

     ![OVF 範本](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. 在 [選取來源] 上，輸入所下載 OVF 的位置。
4. 在 [檢閱詳細資料] 上，選取 [下一步]。
5. 在 [選取名稱和資料夾] 和 [選取組態] 上，接受預設設定。
6. 在 [選取儲存體] 上，於 [選取虛擬磁碟格式] 中選取 [Thick Provision Eager Zeroed]，以達到最佳效能。
7. 在精靈的其餘頁面上，接受所有的預設設定。
8. 在 [準備要完成] 上：

    * 若要使用預設設定來設定 VM，請選取 [在部署後開啟電源] > [完成]。

    * 如果您想要新增額外的網路介面，請清除 [在部署後開啟電源]。 然後選取 [完成]。 預設會使用單一 NIC 部署設定伺服器範本。 您可以在部署後新增其他 NIC。



#### <a name="register-the-configuration-server"></a>註冊組態伺服器 

1. 從 VMWare vSphere 用戶端主控台開啟 VM。
2. VM 會開機進入 Windows Server 2016 安裝體驗。 接受授權合約，並輸入系統管理員密碼。
3. 在安裝完成之後，以系統管理員身分登入 VM。
4. 您第一次登入時，Azure Site Recovery 設定工具會啟動。
5. 輸入用來向 Site Recovery 註冊設定伺服器的名稱。 然後，選取 [下一步]。

    ![OVF 範本](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. 此工具會檢查 VM 是否可連線到 Azure。 建立連線之後，選取 [登入] 以登入您的 Azure 訂用帳戶。 認證必須能夠存取您要在其中註冊組態伺服器的保存庫。

    ![OVF 範本](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. 此工具會執行一些設定工作，而後重新開機。
8. 再次登入電腦。 設定伺服器管理精靈會自動啟動。

#### <a name="configure-settings-and-add-the-vmware-server"></a>進行設定與新增 VMware 伺服器

1. 在設定伺服器管理精靈中，選取 [設定連線]，然後選取要接收複寫流量的 NIC。 然後選取 [儲存]。 您在設定後便無法變更此設定。
2. 在 [選取復原服務保存庫] 中，選取您的 Azure 訂用帳戶及相關的資源群組和保存庫。

    ![保存庫](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. 在 [安裝第三方軟體] 中，接受授權合約。 選取 [下載並安裝] 以安裝 MySQL Server。
4. 選取 [安裝 VMware PowerCLI]。 在您執行這項作業之前，確定所有瀏覽器視窗都已關閉。 然後選取 [繼續]。
5. 在 [驗證設備設定] 中，必要條件會在您繼續之前進行驗證。
6. 在 [設定 vCenter 伺服器/vSphere ESXi 伺服器] 中，輸入 vCenter 伺服器或 vSphere 主機 (您要複寫的 VM 位於其上) 的 FQDN 或 IP 位址。 輸入伺服器所接聽的連接埠。 輸入要用於保存庫中 VMware 伺服器的易記名稱。
7. 輸入供設定伺服器用來連線至 VMware 伺服器的認證。 Site Recovery 會使用這些認證來自動探索可用於複寫的 VMware VM。 選取 [新增]，然後選取 [繼續]。

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. 在 [設定虛擬機器認證] 中，輸入要用於在電腦上自動安裝行動服務的使用者名稱和密碼 (已啟用複寫時)。 若為 Windows 電腦，此帳戶需具備您要複寫之機器的本機系統管理員權限。 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. 選取 [完成設定] 以完成註冊。 
8. 註冊完成後，在 Azure 入口網站中確認組態伺服器和 VMware 伺服器都已列在保存庫中的 [來源] 頁面上。 然後選取 [確定] 以設定目標設定。
9. Site Recovery 會使用指定的設定連線至 VMware 伺服器並探索 VM。

> [!NOTE]
> 可能需要 15 分鐘以上，帳戶名稱才會出現在入口網站。 若要立即更新，請選取 [設定伺服器] > 伺服器名稱 > [重新整理伺服器]。

### <a name="set-up-the-target-environment"></a>設定目標環境

選取並確認目標資源。

1. 在 [準備基礎結構] > [目標] 中，選取您要使用的 Azure 訂用帳戶。
2. 選取 [Azure Resource Manager] 作為目標部署模型。

3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。


### <a name="create-a-replication-policy"></a>建立複寫原則

1. 在 [準備基礎結構] > [複寫設定] > [複寫原則] 中，按一下 [建立並產生關聯]。
1. 在 [建立複寫原則] 中，選取原則名稱 **VMwareRepPolicy**。
2. 在 [RPO 閾值] 中，使用預設值 60 分鐘。 這個值可定義復原點的建立頻率。 連續複寫超過此限制時會產生警示。
3. 在 [復原點保留] 中，使用預設值 24 小時作為每個復原點的保留期長度。 在本教學課程中，使用 72 小時。 複寫的 VM 可以還原至一個週期內的任何時間點。
4. 在 [應用程式一致快照頻率] 中，使用 60 分鐘預設值作為應用程式一致快照集的建立頻率。 選取 [確定] 以建立原則。

    ![建立複寫原則](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. 此原則會自動與設定伺服器產生關聯。 

    ![關聯複寫原則](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>啟用複寫

現在開始複寫 VM。 Site Recovery 會在已啟用複寫的每個 VM 上安裝行動服務。 


1. 選取 [複寫應用程式] > [來源]。
2. 在 [來源] 中，選取設定伺服器。
3. 在 [機器類型] 中，選取 [虛擬機器]
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。
5. 選取處理序伺服器 (組態伺服器)。 然後選取 [確定]。

    ![啟用複寫](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. 在 [目標] 中，選取您想要在其中建立容錯移轉 VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或 Resource Manager) 中，針對容錯移轉 VM 使用的部署模型。
2. 選取您要用來複寫資料的 Azure 儲存體帳戶。
3. 選取 Azure VM 在容錯移轉後所要連線的 Azure 網路和子網路。
4. 選取 [立即設定選取的機器]，將網路設定套用至您選取要進行保護的所有機器。 選取 [稍後設定] 以選取每部機器的 Azure 網路。
5. 選取虛擬網路中的子網路。 然後選取 [確定]。

    ![啟用複寫](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. 在 [虛擬機器] > [選取虛擬機器] 中，選取您想要的 VM (WEBVM)。 您只能選取可以啟用複寫的機器。 

    ![啟用複寫](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. 若要監視您新增的 VM，請在 [設定伺服器] >  [上次連絡時間] 中查看上次探索 VM 的時間。 若要新增 VM 而不等候已排定的探索，請醒目提示設定伺服器 (但不要選取)，然後選取 [重新整理]。 可能需要 15 分鐘或更久的時間，變更才會生效並顯示在入口網站中。
8. 在 [屬性] > [設定屬性] 中，選取處理序伺服器要用來在電腦上自動安裝行動服務的帳戶。 

    ![啟用複寫](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. 在 [複寫設定] > [設定複寫設定] 中，確認已選取正確的複寫原則。
10. 選取 [啟用複寫]。

    ![啟用複寫](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. 在 [設定]  > [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。 
12. 執行 [完成保護] 作業後，機器即準備好進行容錯移轉，並且會出現在 [概觀] > [基本資訊] 中。

    ![基本資訊](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>步驟 8：使用 DMS 遷移資料庫

建立 DMS 專案，並執行移轉。


### <a name="create-a-database-migration-project"></a>建立資料庫移轉專案

1. 在 Azure 入口網站中找到 DMS 資源，並將它開啟。
2. 選取 [+ New Migration Project] \(+ 新增移轉專案\)。
3. 在 [新增移轉專案] 中，指定專案的名稱。
4. 在 [來源伺服器類型] 中選取 [SQL Server]。 在 [目標伺服器類型] 中，選取 [Azure SQL Database 受控執行個體]。
5. 按一下 [建立] 以建立專案。
6. 在 [來源詳細資料] 中，指定內部部署來源 SQL Server 的連線詳細資料。 按一下 [檔案] 。
7. 在 [目標詳細資料] 中，指定目標的連線詳細資料，這個目標是內部部署資料庫所要遷移前往的預先佈建 Azure SQL Database 受控執行個體。 然後按一下 [儲存] 。
8. 在 [專案摘要] 中，檢閱並確認與移轉專案相關聯的詳細資料。

    ![DMS 專案](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>遷移資料庫

1. 建立專案後會出現「移轉精靈」。
2. 指定來源和目標伺服器的認證，然後按一下 [儲存]。 此精靈會嘗試登入內部部署 SQL Server。

    ![DMS 精靈](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. 在 [對應到目標資料庫] 中，選取您要遷移的來源資料庫。 然後按一下 [儲存] 。

    ![DMS 精靈](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. 在 [目標詳細資料] 中，選取 [我知道目標的詳細資料]。 提供 SQL 受控執行個體的 DNS 名稱，以及認證。 然後按一下 [儲存] 。

    ![DMS 精靈](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. 在已儲存的專案中，選取 [+新增活動]。 然後選取 [執行移轉]。
6. 出現提示時，輸入來源和目標伺服器的認證。 然後按一下 [儲存] 。
7. 在 [對應到目標資料庫] 中，選取您要遷移的來源資料庫。 然後按一下 [儲存] 
8. 在 [設定移轉設定] > [伺服器備份位置] 中，指定您之前在內部部署機器上所建立的網路共用。 DMS 會將來源備份擷取到此共用。  請記住，執行來源 SQL Server 執行個體的服務帳戶必須擁有此共用的寫入權限。
9. 指定可供 DMS 模擬的使用者名稱和密碼，以將可用於還原的備份檔案上傳至 Azure 儲存體容器。
10. 指定 SAS URI，此 URI 會向 DMS 提供服務上傳備份檔案時作為目的地的儲存體帳戶容器的存取權，並可用於目的地為 Azure SQL Database 受控執行個體的移轉作業。  然後按一下 [儲存] 。
11. 在 [移轉摘要] 中，指定移轉活動的名稱 > [執行移轉]。
12. 在專案 > [概觀] 中監視移轉狀態。 移轉完成後，確認受控執行個體上有目標資料庫。


## <a name="step-9-migrate-vms-with-site-recovery"></a>步驟 9：使用 Site Recovery 遷移 VM

建議您先執行測試容錯移轉以確定一切正常運作，然後才執行完整移轉。 當您執行測試容錯移轉時，會發生下列情況：

1. 系統會執行先決條件檢查，以確保所有移轉所需的條件都已準備就緒。
2. 容錯移轉會處理資料，以便建立 Azure VM。 若選取最新的復原點，則會根據資料建立復原點。
3. 將會使用先前步驟中處理的資料來建立 Azure VM。

### <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 [受保護的項目] 中，按一下 [複寫的項目] > VM。
2. 在 VM 屬性中，按一下 [+測試容錯移轉]。

    ![測試容錯移轉](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. 選取 [最新處理]，以將 VM 容錯移轉至最新的可用時間點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低 RTO (復原時間目標)。
2. 在 [測試容錯移轉] 中，選取 Azure VM 在容錯移轉之後要連線的目標 Azure 網路。
3. 按一下 [確定]  即可開始容錯移轉。 您可以在保存庫 > [設定] > [作業] > [測試容錯移轉] 中追蹤進度。
4. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器] 中。 確認 VM 為適當的大小、其已連線到正確的網路，而且正在執行中。 您現在應該能夠連線到 Azure 中複寫的 VM。
5. 若要刪除測試容錯移轉期間建立的 Azure VM，請按一下 VM 上的 [清除測試容錯移轉]。 在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。

### <a name="migrate-the-machines"></a>遷移機器

確認過測試容錯移轉可正常運作後，請建立可遷移至 Azure 的復原方案。

### <a name="create-a-recovery-plan"></a>建立復原計畫

1. 在保存庫中，選取 [復原方案 (Site Recovery)] > [+復原方案]。
2. 在 [建立復原方案] 中，指定方案的名稱。
3. 選擇來源組態伺服器及 [Azure] 作為目標。 選取 [Resource Manager] 作為部署模型。 來源位置必須有已啟用容錯移轉和復原功能的機器。 
4. 在 [選取項目] 中，將機器 (WEBVM) 新增至方案。 然後按一下 [確定] 。
5. 按一下 [確定] 以建立方案。

    ![復原計畫](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>執行容錯移轉

1. 在 [復原方案] 中，按一下方案 > [容錯移轉 **]**。
2. 在 [容錯移轉] > [復原點] 中，選取最新的復原點。
3. 加密金鑰設定不適用於此案例。
4. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)。 Site Recovery 在觸發容錯移轉之前，會嘗試將來源虛擬機器關機。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業] 頁面上追蹤容錯移轉進度。

    ![容錯移轉](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. 確認 Azure VM 如預期般出現在 Azure 中。

    ![容錯移轉](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. 在 [複寫的項目] 中，以滑鼠右鍵按一下 VM > [完成移轉]。 這會完成移轉程序、停止 VM 的複寫功能，並停止 VM 的 Site Recovery 計費。

    ![容錯移轉](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>更新連接字串

移轉程序的最後一個步驟是，將應用程式的連接字串更新為指向在 SQL MI 上執行的遷移後資料庫。

1. 在 Azure 入口網站中按一下 [設定] > [連接字串]，以尋找新的連接字串。

    ![容錯移轉](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. 您需要使用 SQL MI 的使用者名稱與密碼來更新此字串。
3. 此字串設定好之後，您必須取代應用程式的 web.config 檔案中目前的連接字串。
4. 更新該檔案並加以儲存後，在 WEBVM 上重新啟動 IIS。 從命令提示字元使用 IISRESET /RESTART，即可執行此動作。
5. IIS 重新啟動後，應用程式現在會使用在 SQL MI 上執行的資料庫。
6. 此時，您可以將 SQLVM 內部部署機器關閉，移轉便隨之完成。



## <a name="conclusion"></a>結論

在此案例中，我們已：

> [!div class="checklist"]
> * 使用 DMS 將內部部署資料庫遷移至 Azure SQL 受控執行個體。
> * 使用 Azure Site Recovery 服務將內部部署 VM 遷移至 Azure VM。
