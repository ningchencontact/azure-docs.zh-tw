---
title: "使用 Azure Data Factory 佈建 SSIS 整合執行階段 | Microsoft Docs"
description: "本文說明如何使用 Azure Data Factor 建立 Azure-SSIS 整合執行階段。"
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/29/2018
ms.author: douglasl
ms.openlocfilehash: 2e3e1fff385de87e69ea36d8ffec128f662f6e50
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-azure-data-factory-ui"></a>使用 Azure Data Factory UI 佈建 Azure-SSIS 整合執行階段
本教學課程提供使用 Azure 入口網站在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段 (IR) 的步驟。 接著，您可以使用 SQL Server Data Tools 或 SQL Server Management Studio，將 SQL Server Integration Services (SSIS) 套件部署到 Azure 上的此執行階段。 如需 Azure-SSIS IR 的概念資訊，請參閱 [Azure-SSIS 整合執行階段概觀](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教學課程中，您會完成下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 佈建 Azure-SSIS 整合執行階段。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="prerequisites"></a>先決條件
- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。 
- **Azure SQL Database 伺服器**。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 Azure Data Factory 會在此資料庫伺服器上建立 SSIS 目錄 (SSISDB 資料庫)。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB 資料庫，而不需要跨 Azure 區域。 
- 確認資料庫伺服器的 [允許存取 Azure 服務] 設定已啟用。 如需詳細資訊，請參閱[保護 Azure SQL Database 資料庫](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。
- 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
- 確認您的 Azure SQL Database 伺服器沒有 SSIS 目錄 (SSISDB 資料庫)。 Azure-SSIS IR 的佈建不支援使用現有的 SSIS 目錄。

> [!NOTE]
> - 您可以在下列區域中建立第 2 版的資料處理站：美國東部、美國東部 2 和西歐。 
> - 您可以在下列區域中建立 Azure-SSIS IR：美國東部、美國東部 2、美國中部、北歐、西歐和澳洲東部。 

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
2. 登入 [Azure 入口網站](https://portal.azure.com/)。    
3. 選取左側功能表上的 [新增]、[資料 + 分析]，然後選取 [資料處理站]。 
   
   ![在 [新增] 窗格中選取資料處理站](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. 在 [新增資料處理站] 頁面上，於 [名稱] 之下輸入 **MyAzureSsisDataFactory**。 
      
   ![[新增資料處理站] 頁面](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 *全域唯一的*。 如果您收到下列錯誤，請變更資料處理站的名稱 (例如 **&lt;yourname&gt;MyAzureSsisDataFactory**)，然後試著重新建立。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory - 命名規則](naming-rules.md)一文。
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. 針對 [訂用帳戶]，選取您要用來建立資料處理站的 Azure 訂用帳戶。 
5. 針對**資源群組**，請執行下列其中一個步驟︰
     
   - 選取 [使用現有的]，然後從清單中選取現有的資源群組。 
   - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
   若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
6. 針對 [版本]，選取 [V2 (預覽版)]。
7. 針對 [位置]，選取資料處理站的位置。 此清單只會顯示支援建立資料處理站的位置。
8. 選取 [釘選到儀表板]。     
9. 選取 [建立] 。
10. 在儀表板上，您會看到 [正在部署資料處理站] 狀態的下列圖格︰ 

   ![[部署 Data Factory] 圖格](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. 建立完成之後，您會看到 [Data Factory] 頁面。
   
   ![資料處理站首頁](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. 選取 [編寫與監視]，以在個別索引標籤上開啟 Data Factory 使用者介面 (UI)。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS 整合執行階段

1. 在 [讓我們開始吧] 頁面上，選取 [設定 SSIS 整合執行階段] 圖格。 

   ![[設定 SSIS 整合執行階段] 圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. 在 [整合執行階段設定] 的 [一般設定] 頁面上，完成下列步驟： 

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. 針對 [名稱]，指定整合執行階段的名稱。

   b. 針對 [位置]，選取整合執行階段的位置。 系統只會顯示支援的位置。

   c. 針對 [節點大小]，選取將透過 SSIS 執行階段設定的節點大小。

   d. 針對 [節點數目]，指定叢集中的節點數目。
   
   e. 選取 [下一步] 。 
3. 在 [SQL 設定]  頁面上，完成下列步驟： 

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. 針對 [訂用帳戶]，指定具有 Azure 資料庫伺服器的 Azure 訂用帳戶。

   b. 針對 [目錄資料庫伺服器端點]，選取您的 Azure 資料庫伺服器。

   c. 針對 [管理員使用者名稱]，輸入網域管理員使用者名稱。

   d. 針對 [管理員密碼]，輸入管理員的密碼。

   e. 針對 [目錄資料庫伺服器服務層]，選取 SSISDB 資料庫的服務層。 預設值為 [基本]。

   f. 選取 [下一步] 。 
4. 在 [進階設定] 頁面上，選取 [每個節點的平行執行上限] 值。   

   ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. 此為*選用*步驟。 如果您想要讓整合執行階段加入虛擬網路 (透過傳統部署模型建立或透過 Azure Resource Manager 建立)，請選取 [選取要加入 Azure-SSIS 整合執行階段的 VNet，並允許 Azure 服務設定 VNet 的權限/設定] 核取方塊。 然後完成下列步驟： 

   ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. 針對 [訂用帳戶]，指定具有虛擬網路的訂用帳戶。

   b. 針對 [VNet 名稱]，選取虛擬網路的名稱。

   c. 針對 [子網路名稱]，選取虛擬網路中的子網路名稱。 
6. 選取 [完成] 以啟動 Azure-SSIS 整合執行階段的建立。 

   > [!IMPORTANT]
   > 此程序需要大約 20 分鐘才能完成。
   >
   > 此 Data Factory 服務會連線到您的 Azure SQL 資料庫，以準備 SSIS 目錄 (SSISDB 資料庫)。 指令碼也會設定虛擬網路的權限和設定 (若已指定)。 而且會將 Azure-SSIS 整合執行階段的新執行個體加入虛擬網路。
   > 
   > 當您佈建 Azure-SSIS IR 的執行個體時，也會安裝適用於 SSIS 的 Azure Feature Pack 和 Access 可轉散發套件。 除了內建元件所支援的資料來源以外，這些元件還提供對 Excel 和 Access 檔案以及各種 Azure 資料來源的連線能力。 您此時無法安裝適用於 SSIS 的第三方元件。 (這包括來自 Microsoft 的第三方元件，例如 Attunity 所提供的 Oracle 和 Teradata 元件及 SAP BI 元件)。

7. 如有必要，在 [連線] 索引標籤上，切換到 [整合執行階段]。 選取 [重新整理] 可重新整理狀態。 

   ![建立狀態，以及 [重新整理] 按鈕](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. 使用 [動作] 資料行中的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。 

   ![[動作] 資料行中的連結](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>建立 Azure-SSIS 整合執行階段

1. 在 Azure Data Factory UI 中，切換到 [編輯] 索引標籤，選取 [連線]，然後切換到 [整合執行階段] 索引標籤，以檢視資料處理站中的現有整合執行階段。 
   ![可檢視現有 IR 的選取項目](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. 選取 [新增] 以建立 Azure-SSIS IR。 

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. 在 [整合執行階段設定] 視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]，然後選取 [下一步]。

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. 如需設定 Azure-SSIS IR 的剩餘步驟，請參閱[佈建 Azure-SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節。 

 
## <a name="deploy-ssis-packages"></a>部署 SSIS 套件
現在，使用 SQL Server Data Tools 或 SQL Server Management Studio 將您的 SSIS 套件部署至 Azure。 連線至裝載 SSIS 目錄 (SSISDB 資料庫) 的 Azure 資料庫伺服器。 Azure 資料庫伺服器的名稱格式為：`<servername>.database.windows.net` (適用於 Azure SQL Database)。 

請參閱 SSIS 文件中的下列文章： 

- [在 Azure 上部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線到 Azure 上的 SSIS 目錄](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何： 

> [!div class="checklist"]
> * 建立資料處理站。
> * 佈建 Azure-SSIS 整合執行階段。

若要了解如何將資料從內部部署環境複製到雲端，請進入下列教學課程： 

> [!div class="nextstepaction"]
> [複製雲端中的資料](tutorial-copy-data-portal.md)
