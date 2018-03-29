---
title: 在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段 | Microsoft Docs
description: 了解如何建立 Azure SSIS 整合執行階段，以在 Azure 雲端中執行 SSIS 封裝。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: dc4c690633d14163eddfa70e8417a645f95a0861
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段
本文提供在 Azure Data Factory 中佈建 Azure-SSIS Integration Runtime 的步驟。 接著，您可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS)，將 SQL Server Integration Services (SSIS) 套件部署到 Azure 上的此執行階段。

教學課程：[教學課程：將 SQL Server Integration Services 套件 (SSIS) 部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md) 示範如何使用 Azure SQL Database 作為 SSIS 目錄的存放區來建立 Azure SSIS Integration Runtime (IR)。 本文會詳述此教學課程並示範如何執行下列作業： 

- 使用 Azure SQL 受控執行個體 (私人預覽) 來裝載 SSIS 目錄 (SSISDB 資料庫)。
- 將 Azure-SSIS IR 加入至 Azure 虛擬網路 (VNet)。 如需上將 Azure-SSIS IR 加入至 VNet 並在 Azure 入口網站中設定 VNet 的概念資訊，請參閱[將 Azure-SSIS IR 加入至 VNet](join-azure-ssis-integration-runtime-virtual-network.md)。 

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。


## <a name="overview"></a>概觀
本文章示範 Azure-SSIS IR 的各種佈建方法：

- [Azure 入口網站](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 範本](#azure-resource-manager-template)

當您建立 Azure-SSIS IR 時，Data Factory 會連線到您的 Azure SQL Database，以準備 SSIS 目錄資料庫 (SSISDB)。 若已指定，則指令碼也會設定您 VNet 的權限和設定，並將 Azure-SSIS 整合執行階段的新執行個體加入到 VNet。

當您佈建 Azure-SSIS 整合執行階段的執行個體時，也會安裝適用於 SSIS 的 Azure Feature Pack 和 Access 可轉散發套件。 除了內建元件所支援的資料來源以外，這些元件還提供對 Excel 和 Access 檔案以及各種 Azure 資料來源的連線能力。 您此時無法安裝適用於 SSIS 的第三方元件 (包括來自 Microsoft 的第三方元件，例如 Attunity 所提供的 Oracle 和 Teradata 元件及 SAP BI 元件)。

## <a name="prerequisites"></a>先決條件

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可以建立[免費試用](http://azure.microsoft.com/pricing/free-trial/)帳戶。
- **Azure SQL Database 伺服器**或 **SQL Server 受控執行個體 (私人預覽) (延伸的私人預覽)**。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 此伺服器裝載 SSIS 目錄資料庫 (SSISDB)。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 記下您的 Azure SQL 伺服器定價層。 如需 Azure SQL Database 支援的定價層清單，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

    請確認您的 Azure SQL Database 伺服器或 SQL Server 受控執行個體 (延伸私人預覽) 並無 SSIS 目錄 (SSIDB 資料庫)。 Azure-SSIS IR 的佈建不支援使用現有的 SSIS 目錄。
- **傳統或 Azure Resource Manager 虛擬網路 (VNet) (選擇性)**。 如果至少有下列一個條件成立，則您必須具備 Azure 虛擬網路 (VNet)：
    - 您要在屬於 VNet 一部分的 SQL Server 受控執行個體 (私人預覽) 上裝載 SSIS 目錄資料庫。
    - 您想要從 Azure-SSIS 整合執行階段上執行的 SSIS 套件連線至內部部署資料存放區。
- **Azure PowerShell**(英文)。 遵循[如何安裝並設定 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的指示。 您需要使用 PowerShell 來執行指令碼，以佈建在雲端中執行 SSIS 套件的 Azure-SSIS 整合執行階段。 

> [!NOTE]
> - 您可以在下列區域中建立第 2 版的資料處理站：美國東部、美國東部 2、東南亞和西歐。 
> - 您可以在下列區域中建立 Azure-SSIS IR：美國東部、美國東部 2、美國中部、北歐、西歐和澳大利亞東部。

## <a name="azure-portal"></a>Azure 入口網站
在本節中，會使用 Azure 入口網站 (特別是 Data Factory UI) 建立 Azure-SSIS IR。 

### <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
2. 登入 [Azure 入口網站](https://portal.azure.com/)。    
3. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
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

### <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure SSIS 整合執行階段

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
5. 此為**選用**步驟。 如果您想要讓整合執行階段加入您的 VNet (傳統或 Azure Resource Manager)，請選取 [選取 Azure-SSIS 整合執行階段要加入的 VNet，並允許 Azure 服務設定 VNet 的權限/設定] 選項，然後執行下列步驟： 

    ![VNet 進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. 在 [訂用帳戶]，指定具有該 VNet 的 Azure **訂用帳戶**。 
    2. 針對類型，指定 VNet 的**類型** (傳統虛擬網路或 Azure Resource Manager 虛擬網路)。 
    3. 針對 [VNet 名稱]，選取您的 **VNet** 名稱。 
    4. 針對 [子網路名稱]，選取 VNet 中的**子網路**名稱。
1. 按一下 [完成] 以啟動 Azure-SSIS 整合執行階段的建立。 

    > [!IMPORTANT]
    > - 此程序需要大約 20 分鐘才能完成。
    > - 此 Data Factory 服務會連線到您的 Azure SQL Database，以準備 SSIS 目錄資料庫 (SSISDB)。 若已指定，則指令碼也會設定您 VNet 的權限和設定，並將 Azure-SSIS 整合執行階段的新執行個體加入到 VNet。
7. 如有必要，切換到 [連線] 視窗中的 [整合執行階段]。 按一下 [重新整理]  即可重新整理狀態。 

    ![建立狀態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. 使用 [動作] 資料行下方的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。 

    ![Azure SSIS IR - 動作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>入口網站中的 Azure SSIS 整合執行階段

1. 在 Azure Data Factory 使用者介面中，切換至 [編輯] 索引標籤，按一下 [連線]，然後切換到 [整合執行階段] 索引標籤，以檢視資料處理站中的現有整合執行階段。 
    ![檢視現有的 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. 按一下 [新增] 建立新的 Azure-SSIS IR。 

    ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. 若要建立 Azure-SSIS 整合執行階段，按一下 [新增]，如圖中所示。 
3. 在 [整合執行階段設定] 視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]，然後按一下 [下一步]。

    ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. 請參閱[佈建 Azure SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節，以取得設定 Azure SSIS IR 的剩餘步驟。

## <a name="azure-powershell"></a>Azure PowerShell
在本節中，會使用 Azure PowerShell 建立 Azure-SSIS IR。

### <a name="create-variables"></a>建立變數
定義變數以用於本教學課程中的指令碼：

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>登入並選取訂用帳戶
在指令碼中新增下列程式碼，以登入並選取您的 Azure 訂用帳戶： 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>驗證資料庫的連線
新增下列指令碼以驗證您的 Azure SQL Database 伺服器 (server.database.windows.net)，或是您的 Azure SQL 受控執行個體 (私人預覽) 伺服器端點。 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

### <a name="configure-virtual-network"></a>設定虛擬網路
新增下列指令碼來自動設定虛擬網路權限/設定，讓您的 Azure-SSIS 整合執行階段可以加入。

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>建立資源群組
使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>建立 Data Factory
執行下列命令來建立資料處理站：

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>建立整合執行階段
執行下列命令來建立在 Azure 中執行 SSIS 封裝的 Azure-SSIS 整合執行階段：請根據您使用的資料庫類型 (Azure SQL Database 與Azure SQL 受控執行個體 (私人預覽)) 使用本節提供的指令碼。 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>使用 Azure SQL Database 來裝載 SSISDB 資料庫 (SSIS 目錄) 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

您不需要傳遞 VNetId 和 Subnet 的值，除非您需要內部部署資料存取，也就是說，您的 SSIS 套件中有內部部署資料來源/目的地。 您必須傳遞 CatalogPricingTier 參數的值。 如需 Azure SQL Database 支援的定價層清單，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>使用 Azure SQL 受控執行個體 (私人預覽) 來裝載 SSISDB 資料庫

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

您需要透過加入 VNet 的 Azure SQL 受控執行個體 (私人預覽)，傳遞 VnetId 和 Subnet 參數的值。 CatalogPricingTier 參數不適用於 Azure SQL 受控執行個體。 

### <a name="start-integration-runtime"></a>啟動整合執行階段
執行下列命令以啟動 Azure-SSIS 整合執行階段： 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
此命令需花費 **20 至 30 分鐘**來完成。 


### <a name="full-script"></a>完整指令碼
以下是建立 Azure-SSIS IR 並將之加入 VNet 的完整指令碼。 該指令碼假設您使用 Azure SQL 受控執行個體 (MI) 來裝載 SSIS 目錄。 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
在本節中，會使用 Azure Resource Manager 範本來建立 Azure-SSIS 整合執行階段。 以下是範例逐步解說： 

1. 使用下列 Resource Manager 範本建立 JSON 檔案。 將角括弧 (預留位置) 中的值取代為您自己的值。 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. 若要部署 Resource Manager 範本，請執行 New-AzureRmResourceGroupDeployment 命令，如以下範例所示。 在此範例中，ADFTutorialResourceGroup 是資源群組的名稱。 ADFTutorialARM.json 是包含 Data Factory 和 Azure-SSIS IR JSON 定義的檔案。 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    這個命令會建立 Data Factory，並且在其中建立 Azure SSIS IR，但不會啟動 IR。 
3. 若要啟動 Azure-SSIS IR，請執行 Start-AzureRmDataFactoryV2IntegrationRuntime 命令： 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件
現在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 將您的 SSIS 套件部署至 Azure。 連線至裝載 SSIS 目錄 (SSISDB) 的 Azure SQL 伺服器。 Azure SQL 伺服器的名稱格式為：&lt;servername&gt;.database.windows.net (適用於 Azure SQL Database)。 如需指示，請參閱[部署套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)一文。 

## <a name="next-steps"></a>後續步驟
請參閱本文件中的其他 Azure-SSIS IR 主題：

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文逐步說明如何建立 Azure-SSIS IR，並使用 Azure SQL Database 裝載 SSIS 目錄。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 它也會告訴您如何將更多節點新增至 IR，藉此相應放大 Azure SSIS IR。 
- [將 VNet 加入至 Azure SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md)。 這篇文章提供將 Azure SSIS IR 加入至 Azure 虛擬網路 (VNet) 的概念資訊。 它也提供使用 Azure 入口網站來設定 VNet，好讓 Azure SSIS IR 可加入 VNet 的步驟。 