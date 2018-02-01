---
title: "使用入口網站佈建 Azure SSIS 整合執行階段 | Microsoft Docs"
description: "本文說明如何使用 Azure 入口網站建立 Azure-SSIS 整合執行階段。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>使用 Data Factory UI 佈建 Azure SSIS 整合執行階段
本教學課程提供使用 Azure 入口網站在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段 (IR) 的步驟。 接著，您可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS)，將 SQL Server Integration Services (SSIS) 套件部署到 Azure 上的此執行階段。 如需 Azure-SSIS IR 的概念資訊，請參閱 [Azure-SSIS Integration Runtime 概觀](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立及啟動 Azure-SSIS 整合執行階段

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="prerequisites"></a>先決條件
- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。 
- **Azure SQL Database 伺服器**。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 Azure Data Factory 會在此資料庫伺服器上建立 SSIS 目錄資料庫 (SSISDB)。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 
    - 確認資料庫伺服器的 [允許存取 Azure 服務] 設定已啟用。 如需詳細資訊，請參閱[保護 Azure SQL Database 資料庫](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。
    - 新增用戶端電腦的 IP 位址或 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
    - 確認您的 Azure SQL Database 伺服器沒有 SSIS 目錄 (SSIDB 資料庫)。 Azure-SSIS IR 的佈建不支援使用現有的 SSIS 目錄。
 
## <a name="create-a-data-factory"></a>建立 Data Factory

1. 登入 [Azure 入口網站](https://portal.azure.com/)。    
2. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. 在 [新增資料處理站] 頁面中，輸入 **MyAzureSsisDataFactory** 作為 [名稱]。 
      
     ![新增資料處理站頁面](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您收到下列錯誤，請變更資料處理站的名稱 (例如 yournameMyAzureSsisDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，請執行下列其中一個步驟︰
     
      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。 
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
      若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 對 [版本] 選取 [V2 (預覽)]。
5. 選取 Data Factory 的 [位置]  。 清單中只會顯示資料處理站建立所支援的位置。
6. 選取 [釘選到儀表板]。     
7. 按一下頁面底部的 [新增] 。
8. 在儀表板上，您會看到狀態如下的下列圖格︰**正在部署資料處理站**。 

    ![部署資料處理站圖格](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。
   
   ![Data Factory 首頁](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. 按一下 [編寫與監視]，以在另一個索引標籤中啟動 Data Factory 使用者介面 (UI)。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure SSIS 整合執行階段

1. 在開始使用頁面中，按一下 [設定 SSIS 整合執行階段] 圖格。 

   ![設定 SSIS 整合執行階段圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. 在 [整合執行階段設定] 的 [一般設定] 頁面中，執行下列步驟： 

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. 指定整合執行階段的 [名稱]。
    2. 選取整合執行階段的 [位置]。 系統只會顯示支援的位置。
    3. 選取**節點的大小**，這必須透過 SSIS 執行階段設定。
    4. 指定叢集中的**節點數目**。
    5. 按 [下一步] 。 
1. 在 [SQL 設定] 中，執行下列步驟： 

    ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. 指定具有此 Azure SQL 伺服器的 Azure [訂用帳戶]。 
    2. 在 [目錄資料庫伺服器端點] 選取您的 Azure SQL 伺服器。
    3. 輸入系統管理員的 [使用者名稱]。
    4. 輸入系統管理員的 [密碼]。  
    5. 選取 SSISDB 資料庫的 [服務層]。 預設值為「基本」。
    6. 按 [下一步] 。 
1.  在 [進階設定] 頁面上，選取 [每個節點的平行執行上限] 值。   

    ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. 此為**選用**步驟。 如果您想要讓整合執行階段加入您的傳統虛擬網路 (VNet)，請選取 [選取您 Azure-SSIS 整合執行階段要加入的 VNet，並允許 Azure 服務設定 VNet 的權限/設定] 選項，然後執行下列步驟： 

    ![VNet 進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. 指定具有此傳統 VNet 的 [訂用帳戶]。 
    2. 選取 **VNet**。 <br/>
    4. 選取**子網路**。<br/> 
1. 按一下 [完成] 以啟動 Azure-SSIS 整合執行階段的建立。 

    > [!IMPORTANT]
    > - 此程序需要大約 20 分鐘才能完成。
    > - 此 Data Factory 服務會連線到您的 Azure SQL Database，以準備 SSIS 目錄資料庫 (SSISDB)。 若已指定，則指令碼也會設定您 VNet 的權限和設定，並將 Azure-SSIS 整合執行階段的新執行個體加入到 VNet。
    > - 當您佈建 SQL Database 的執行個體以裝載 SSISDB 時，也會安裝適用於 SSIS 的 Azure Feature Pack 和 Access 可轉散發套件。 除了內建元件所支援的資料來源以外，這些元件還提供對 Excel 和 Access 檔案以及各種 Azure 資料來源的連線能力。 您此時無法安裝適用於 SSIS 的第三分元件 (包括來自 Microsoft 的第三方元件，例如 Attunity 所提供的 Oracle 和 Teradata 元件及 SAP BI 元件)。

7. 如有必要，切換到 [連線] 視窗中的 [整合執行階段]。 按一下 [重新整理]  即可重新整理狀態。 

    ![建立狀態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. 使用 [動作] 資料行下方的連結來監視、停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。 

    ![Azure SSIS IR - 動作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. 按一下 [動作] 底下的 [監視] 連結。  

    ![Azure SSIS IR - 詳細資料](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. 如果發生與 Azure SSIS IR 相關聯的**錯誤**，您會在此頁面上看到錯誤數目，以及可檢視錯誤詳細資訊的連結。 例如，如果資料庫伺服器上已有 SSIS 目錄，您會看到錯誤指出 SSISDB 資料庫已存在。  
11. 按一下頂端的 [整合執行階段] 瀏覽回先前的頁面，以查看與資料處理站相關聯的所有整合執行階段。  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 Azure Data Factory 使用者介面中，切換至 [編輯] 索引標籤，按一下 [連線]，然後切換到 [整合執行階段] 索引標籤，以檢視資料處理站中的現有整合執行階段。 
    ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. 按一下 [新增] 建立新的 Azure-SSIS IR。 

    ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. 若要建立 Azure-SSIS 整合執行階段，按一下 [新增]，如圖中所示。 
3. 在 [整合執行階段設定] 視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]，然後按一下 [下一步]。

    ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. 請參閱[佈建 Azure SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節，以取得設定 Azure SSIS IR 的剩餘步驟。 

 
## <a name="deploy-ssis-packages"></a>部署 SSIS 套件
現在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 將您的 SSIS 套件部署至 Azure。 連線至裝載 SSIS 目錄 (SSISDB) 的 Azure SQL 伺服器。 Azure SQL 伺服器的名稱格式為：`<servername>.database.windows.net` (適用於 Azure SQL Database)。 

請參閱 SSIS 文件中的下列文章： 

- [在 Azure 上部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線到 Azure 上的 SSIS 目錄](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何： 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立及啟動 Azure-SSIS 整合執行階段

進入下列教學課程，以了解如何將資料從內部部署複製到雲端： 

> [!div class="nextstepaction"]
>[複製雲端中的資料](tutorial-copy-data-portal.md)
