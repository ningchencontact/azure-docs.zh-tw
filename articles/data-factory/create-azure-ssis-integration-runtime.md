---
title: 在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段，您就可以在 Azure 部署並執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f75d08074fc6020541226318d6422da373136a2d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267900"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段
本文提供在 Azure Data Factory 中佈建 Azure-SSIS Integration Runtime 的步驟。 接著，您可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS)，將 SQL Server Integration Services (SSIS) 套件部署到 Azure 中的此執行階段並執行。

教學課程：[教學課程：將 SQL Server Integration Services 套件 (SSIS) 部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md) 示範如何使用 Azure SQL Database 裝載 SSIS 目錄來建立 Azure SSIS Integration Runtime (IR)。 本文會詳述此教學課程並示範如何執行下列作業： 

- 選擇性地使用具有虛擬網路服務端點/受控執行個體 (預覽) 的 Azure SQL Database 作為資料庫伺服器，以裝載 SSIS 目錄 (SSISDB 資料庫)。 前提是，您必須將 Azure-SSIS IR 加入虛擬網路，並且視需要設定虛擬網路權限和設定，請參閱[將 Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。  
- 選擇性地使用 Azure Data Factory (AAD) 驗證搭配 Azure Data Factory 受控服務識別 (MSI)，以便 Azure-SSIS IR 連線到資料庫伺服器。 前提是，您必須將 Data Factory MSI 新增至具有資料庫伺服器存取權限的 AAD 群組，請參閱[啟用 Azure-SSIS IR 的 AAD 驗證](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。


## <a name="overview"></a>概觀
本文章示範 Azure-SSIS IR 的各種佈建方法：

- [Azure 入口網站](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 範本](#azure-resource-manager-template)

當您建立 Azure-SSIS IR 時，Data Factory 會連線到您的 Azure SQL Database，以準備 SSIS 目錄資料庫 (SSISDB)。 若已指定，則指令碼也會設定您虛擬網路的權限和設定，並將 Azure-SSIS 整合執行階段的新執行個體加入到虛擬網路。

當您佈建 Azure-SSIS 整合執行階段的執行個體時，也會安裝適用於 SSIS 的 Azure Feature Pack 和 Access 可轉散發套件。 除了內建元件所支援的資料來源以外，這些元件還提供對 Excel 和 Access 檔案以及各種 Azure 資料來源的連線能力。 您也可以安裝其他元件。 如需詳細資訊，請參閱 [Azure-SSIS 整合執行階段自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="prerequisites"></a>先決條件

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可以建立[免費試用](http://azure.microsoft.com/pricing/free-trial/)帳戶。
- **Azure SQL Database 伺服器/受控執行個體 (預覽)**。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 此伺服器裝載 SSIS 目錄資料庫 (SSISDB)。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 根據選取的資料庫伺服器，SSISDB 可建立為代表您的獨立資料庫、彈性集區的一部份，或建立在受控執行個體中 (預覽)，並且可在公用網路中或透過加入虛擬網路來加以存取。 如需 Azure SQL Database 支援的定價層清單，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

    請確認您的 Azure SQL Database 伺服器/受控執行個體 (預覽) 沒有 SSIS 目錄 (SSIDB 資料庫)。 Azure-SSIS IR 的佈建不支援使用現有的 SSIS 目錄。
- **傳統/Azure Resource Manager 虛擬網路 (選擇性)**。 如果至少有下列一個條件成立，則您必須具備 Azure 虛擬網路：
    - 您要將 SSIS 目錄資料庫裝載於具有虛擬網路中虛擬網路服務端點/受控執行個體 (預覽) 的 Azure SQL Database 中。
    - 您想要從 Azure-SSIS 整合執行階段上執行的 SSIS 套件連線至內部部署資料存放區。
- **Azure PowerShell**(英文)。 如果您使用 PowerShell 來執行指令碼，請依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)中的指示操作，以佈建在雲端執行 SSIS 套件的 Azure-SSIS 整合執行階段。 

> [!NOTE]
> - 您可以在下列區域中建立第 2 版的資料處理站：美國東部、美國東部 2、東南亞和西歐。 
> - 您可以在下列區域中建立 Azure-SSIS IR：美國東部、美國東部 2、美國中部、美國西部 2、北歐、西歐、英國南部和澳大利亞東部。

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
2. 在 [整合執行階段設定] 的 [一般設定] 頁面上，完成下列步驟： 

    ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. 針對 [名稱]，輸入取您整合執行階段的名稱。
    
    b. 針對 [描述]，輸入整合執行階段的描述。
    
    c. 針對 [位置]，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。
    
    d. 針對 [節點大小]，選取整合執行階段叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算/記憶體密集套件，請選取大型節點大小 (相應增加)。
    
    e. 針對 [節點數目]，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (相應放大)。
     
    f. 針對 [版本/授權]，選取適合您的整合執行階段的 SQL Server 版本/授權：標準或企業版。 如果您想要在整合執行階段上使用進階/高階功能，請選取 [企業版]。 
    
    g. 針對 [節省費用]，選取您整合執行階段的 Azure Hybrid Benefit (AHB) 選項：是或否。 如果您想要自備附有軟體保證的 SQL Server 授權，以混搭使用來享有節省費用的權益，請選擇 [是]。
    
    h. 按 [下一步] 。 
3. 在 [SQL 設定]  頁面上，完成下列步驟： 

    ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. 針對 [訂用帳戶]，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 
    
    b. 針對 [位置]，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。
    
    c. 針對 [目錄資料庫伺服器端點]，選取裝載 SSISDB 的資料庫伺服器端點。 根據選取的資料庫伺服器，SSISDB 可建立為代表您的獨立資料庫、彈性集區的一部份，或建立在受控執行個體中 (預覽)，並且可在公用網路中或透過加入虛擬網路來加以存取。
    
    d. 在 [使用 AAD 驗證...] 核取方塊上，為裝載 SSISDB 的資料庫伺服器選取驗證方法：使用 Azure Data Factory 受控服務識別 (MSI) 的 SQL 或 Azure Active Directory (AAD)。 如果您核取該方塊，您必須將 Data Factory MSI 新增至具有資料庫伺服器存取權的 AAD 群組，請參閱[啟用 Azure-SSIS IR 的 AAD 驗證](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。
    
    e. 針對 [管理使用者名稱]，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。
    
    f. 針對 [管理密碼]，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。  
    
    g. 針對 [目錄資料庫服務層]，選取資料庫伺服器要裝載 SSISDB 的服務層：基本/標準/進階或彈性集區名稱。
    
    h. 按一下 [測試連線]，如果成功的話，按 [下一步]。 
4.  在 [進階設定] 頁面上，完成下列步驟：   

    ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
    
    a. 針對 [每節點的平行執行數上限]，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用一個以上的核心，來執行計算/記憶體密集的單一大型/重量套件，請選取較低的數字。 如果您想要在單一核心中執行一個或多個小型/輕量套件，請選取較高的數字。 
    
    b. 針對 [自訂安裝容器的 SAS URI]，選擇性地輸入 Azure 儲存體 Blob 容器的共用存取簽章 (SAS) 統一資源識別項 (URI)，該容器是您安裝指令碼且儲存指令碼相關檔案的地方，請參閱[自訂 Azure-SSIS IR 的安裝](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。 
5. 在 [選取虛擬網路...] 核取方塊上，選取是否要將整合執行階段加入虛擬網路。 如果您使用具有虛擬網路服務端點/受控執行個體 (預覽) 的 Azure SQL Database 來裝載 SSISDB，或要求存取內部部署資料，請核取該方塊；也就是說，您的 SSIS 套件中有內部部署資料來源/目的地，請參閱[將 Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 如果您核取該方塊，請完成下列步驟：


    ![虛擬網路進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    a. 針對 [訂用帳戶]，選取具有您的虛擬網路的 Azure 訂用帳戶。 
    
    b. 針對 [位置]，選取整合執行階段的相同位置。
    
    b. 針對 [類型]，選取您的虛擬網路類型：傳統或 Azure Resource Manager。 建議您選取 Azure Resource Manager 虛擬網路，因為傳統虛擬網路很快就會被取代。
    
    c. 針對 **[VNet 名稱]**，選取您虛擬網路的名稱。 此虛擬網路應該與用於具有虛擬網路服務端點/受控執行個體 (預覽) 的 Azure SQL Database 來裝載 SSISDB，或連線到內部部署網路的虛擬網路相同。

    d. 針對 [子網路名稱]，選取虛擬網路的子網路名稱。 此虛擬網路應該與用於受控執行個體 (預覽) 來裝載 SSISDB 的子網路不同。
6. 按一下 [VNet 驗證]，如果成功，按一下 [完成] 開始建立 Azure-SSIS 整合執行階段。 

    > [!IMPORTANT]
    > - 此程序需要大約 20 分鐘才能完成。
    > - 此 Data Factory 服務會連線到您的 Azure SQL Database，以準備 SSIS 目錄資料庫 (SSISDB)。 若已指定，則指令碼也會設定您虛擬網路的權限和設定，並將 Azure-SSIS 整合執行階段的新執行個體加入到虛擬網路。
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
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>登入並選取訂用帳戶
在指令碼中新增下列程式碼，以登入並選取您的 Azure 訂用帳戶： 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>驗證資料庫的連線
新增下列指令碼來驗證您的 Azure SQL Database 伺服器端點。 

```powershell
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
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
使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>建立 Data Factory
執行下列命令來建立資料處理站。

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>建立整合執行階段
執行下列命令，以建立在 Azure 中執行 SSIS 套件的 Azure-SSIS 整合執行階段。 

如果您未使用具有虛擬網路服務端點/受控執行個體 (預覽) 的 Azure SQL Database 來裝載 SSISDB，也或要求存取內部部署資料，您可以略過 VNetId 和 Subnet 參數或為其傳遞空白值。 否則，您不能省略它們，而必須從您的虛擬網路組態傳遞有效值，請參閱[將 Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果您使用受控執行個體 (預覽) 來裝載 SSISDB，您可以省略 CatalogPricingTier 參數或為其傳遞空白值。 否則，您無法省略它，而必須從 Azure SQL Database 支援的定價層清單傳遞有效值，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。 

如果您使用 Azure Active Directory (AAD) 驗證搭配 Azure Data Factory 受控服務識別 (MSI) 來連線到資料庫伺服器，您可以省略 CatalogAdminCredential 參數，但您必須將 Data Factory MSI 新增至具有資料庫伺服器存取權限的 AAD 群組，請參閱[啟用 Azure-SSIS IR 的 AAD 驗證](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

您必須透過加入虛擬網路的 Azure SQL 受控執行個體 (預覽)，傳遞 VnetId 和 Subnet 參數的值。 CatalogPricingTier 參數不適用於 Azure SQL 受控執行個體 (預覽)。 

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

以下是建立 Azure-SSIS 整合執行階段的完整指令碼。 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
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

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
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

1. 使用下列 Azure Resource Manager 範本建立 JSON 檔案。 將角括弧 (預留位置) 中的值取代為您自己的值。 

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
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
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
    
2. 若要部署 Azure Resource Manager 範本，請執行下列範例中顯示的 New-AzureRmResourceGroupDeployment 命令，其中 ADFTutorialResourceGroup 是資源群組的名稱，而 ADFTutorialARM.json 檔案包含資料處理站和 Azure-SSIS IR 的 JSON 定義。 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    這個命令會建立資料處理站，並且在其中建立 Azure-SSIS IR，但不會啟動 IR。 
3. 若要啟動 Azure-SSIS IR，請執行 Start-AzureRmDataFactoryV2IntegrationRuntime 命令： 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件
現在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 將您的 SSIS 套件部署至 Azure。 連線至裝載 SSIS 目錄 (SSISDB) 的資料庫伺服器。 資料庫伺服器名稱的格式：&lt;Azure SQL Database 伺服器名稱&gt;.database.windows.net 或&lt;受控執行個體 (預覽) name.DNS prefix&gt;.database.windows.net. 如需指示，請參閱[部署套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)一文。

## <a name="next-steps"></a>後續步驟
請參閱本文件中的其他 Azure-SSIS IR 主題：

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文逐步說明如何建立 Azure-SSIS IR，並使用 Azure SQL Database 裝載 SSIS 目錄。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 它也會示範如何將更多節點新增至 IR，藉此相應放大 Azure-SSIS IR。 
- [將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 這篇文章提供將 Azure-SSIS IR 加入至 Azure 虛擬網路的概念資訊。 它也提供使用 Azure 入口網站來設定虛擬網路，好讓 Azure SSIS IR 可加入虛擬網路的步驟。 

