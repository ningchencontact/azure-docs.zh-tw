---
title: 將 Azure-SSIS 整合執行階段加入虛擬網路 | Microsoft Docs
description: 了解如何將 Azure-SSIS 整合執行階段加入 Azure 虛擬網路。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 4f1100b7e4fa2250baf282b53ef83c5f1aaa1c0e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路
在下列案例中，將 Azure-SSIS 整合執行階段 (IR) 加入 Azure 虛擬網路： 

- 您要將 SQL Server Integration Service (SSIS) 目錄資料庫裝載在虛擬網路中的 Azure SQL Database 受控執行個體 (私人預覽) 上。
- 您想要從 Azure-SSIS 整合執行階段上執行的 SSIS 套件連線至內部部署資料存放區。

 Azure Data Factory 第 2 版 (預覽) 可讓您將 Azure-SSIS 整合執行階段加入透過傳統部署模型或 Azure Resource Manager 開發模型建立的虛擬網路。 

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

## <a name="access-to-on-premises-data-stores"></a>存取內部部署資料存放區
如果 SSIS 套件只會存取公用雲端資料存放區，則您不需要將 Azure-SSIS IR 加入虛擬網路中。 如果 SSIS 套件會存取內部部署資料存放區，則您必須將 Azure-SSIS IR 加入連線至內部部署網路的虛擬網路中。 

如果 SSIS 目錄裝載於虛擬網路以外的 Azure SQL Database 執行個體中，您必須開啟適當的連接埠。 

如果 SSIS 目錄裝載於虛擬網路中的 SQL Database 受控執行個體，則您可以將 Azure-SSIS IR 加入：

- 相同的虛擬網路。
- 具有網路對網路連線、且其中一個網路含有 SQL Database 受控執行個體的不同虛擬網路。 

虛擬網路可透過傳統部署模型或 Azure Resource Manager 部署模型來部署。 如果您計劃將 Azure-SSIS IR 加入含有 SQL Database 受控執行個體的*相同虛擬網路*中，請確定 Azure-SSIS IR 位於*不同的子網路*，也就是並非含有 SQL Database 受控執行個體的子網路。   

下列各節提供更多詳細資料。

以下是一些需要注意的重要事項： 

- 如果目前沒有虛擬網路連線至您的內部部署網路，請先建立 Azure-SSIS 整合執行階段要加入的 [Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)或[傳統虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定從該虛擬網路到內部部署網路的站對站 [VPN 閘道連線](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 連線。
- 如果在與 Azure-SSIS 整合執行階段相同的位置中有現有的 Azure Resource Manager 或傳統虛擬網路連線至您的內部部署網路，您可以將整合執行階段加入該虛擬網路。
- 如果在與 Azure-SSIS 整合執行階段不同的位置中有現有的傳統虛擬網路連線至您的內部部署網路，您可以先建立 Azure-SSIS IR 要加入的[傳統虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定[傳統對傳統虛擬網路](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)連線。 或者，您可以建立 [Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md#create-a-virtual-network)，讓您的 Azure-SSIS 整合執行階段加入。 然後，設定[傳統對 Azure Resource Manager 虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)連線。
- 如果在與 Azure-SSIS IR 不同的位置中有現有的 Azure Resource Manager 虛擬網路連線至您的內部部署網路，您可以先建立 Azure-SSIS IR 要加入的 [Azure Resource Manager 虛擬網路](../virtual-network/quick-create-portal.md##create-a-virtual-network)。 然後，設定 Azure Resource Manager 對 Azure Resource Manager 虛擬網路連線。 或者，您可以建立[傳統虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)，讓您的 Azure-SSIS IR 加入。 然後，設定[傳統對 Azure Resource Manager 虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)連線。

## <a name="domain-name-services-server"></a>網域名稱服務伺服器 
如果您需要在 Azure-SSIS 整合執行階段所加入的虛擬網路中使用自己的網域名稱系統 (DNS) 伺服器，請遵循指引，以[確定虛擬網路中的 Azure-SSIS 整合執行階段的節點可解析 Azure 端點](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>網路安全性群組
如果您需要在 Azure-SSIS 整合執行階段所加入的虛擬網路中實作網路安全性群組 (NSG)，請允許通過下列連接埠的輸入/輸出流量：

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 輸入來源/輸出目的地 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100、20100、30100 (如果您將 IR 加入傳統虛擬網路)<br/><br/>29876、29877 (如果您將 IR 加入 Azure Resource Manager 虛擬網路) | 輸入 | TCP | Azure 服務會使用這些連接埠與虛擬網路中的 Azure SSIS 整合執行階段節點進行通訊。 | Internet | 
| 443 | 輸出 | TCP | 您在虛擬網路中的 Azure-SSIS 整合執行階段節點會使用此連接埠來存取 Azure 服務，例如 Azure 儲存體和 Azure 事件中樞。 | Internet | 
| 1433<br/>11000-11999<br/>14000-14999  | 輸出 | TCP | 您在虛擬網路中的 Azure-SSIS 整合執行階段節點會使用這些連接埠來存取 Azure SQL Database 伺服器所裝載的 SSISDB (此用途不適用於 SQL Database 受控執行個體所裝載的 SSISDB)。 | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure 入口網站 (Data Factory UI)
本節說明如何使用 Azure 入口網站及 Data Factory UI，將現有的 Azure SSIS 執行階段加入虛擬網路 (傳統或 Azure Resource Manager)。 首先，在將 Azure SSIS IR 加入虛擬網路之前，必須先適當設定虛擬網路。 根據您的虛擬網路類型 (傳統或 Azure Resource Manager)，執行下列兩節的其中一節。 然後，繼續執行第三節，將您的 Azure SSIS IR 加入虛擬網路。 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>使用入口網站設定傳統虛擬網路
您必須先設定虛擬網路，才能將 Azure-SSIS IR 加入虛擬網路。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。
2. 登入 [Azure 入口網站](https://portal.azure.com)。
3. 選取 [更多服務]。 篩選並選取 [虛擬網路 (傳統)]。
4. 篩選並選取清單中的 [虛擬網路]。 
5. 在 [虛擬網路 (傳統)] 頁面上，選取 [屬性]。 

    ![傳統虛擬網路資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. 選取 [資源識別碼] 的 [複製] 按鈕，將傳統網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。
6. 選取左側功能表上的 [子網路]。 確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點數。

    ![虛擬網路中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. 將 **MicrosoftAzureBatch** 加入虛擬網路的**傳統虛擬機器參與者**角色。

    a. 選取左側功能表上的 [存取控制 (IAM)]，然後選取工具列上的 [新增]。 
       ![[存取控制] 和 [新增] 按鈕](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. 在 [新增權限] 頁面上，針對 [角色] 選取 [傳統虛擬機器參與者]。 在 [選取] 方塊中複製 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然後從搜尋結果清單中選取 [Microsoft Azure Batch]。   
       ![在 [新增權限] 頁面上的搜尋結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. 選取 [儲存] 以儲存設定並關閉頁面。  
       ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. 確認您在參與者清單中看到 **Microsoft Azure Batch**。  
       ![確認 Azure Batch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或者，註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。

   a. 在 Azure 入口網站中，選取左側功能表上的 [訂用帳戶]。

   b. 選取您的訂用帳戶。

   c. 選取左側的 [資源提供者]，並確認 **Microsoft.Batch** 是已註冊的提供者。     
      ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>使用入口網站設定 Azure Resource Manager 虛擬網路
您必須先設定虛擬網路，才能將 Azure-SSIS IR 加入虛擬網路。

1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。
2. 登入 [Azure 入口網站](https://portal.azure.com)。
3. 選取 [更多服務]。 篩選並選取 [虛擬網路]。
4. 篩選並選取清單中的 [虛擬網路]。 
5. 在 [虛擬網路] 頁面上，選取 [屬性]。 
6. 選取 [資源識別碼] 的 [複製] 按鈕，將虛擬網路的資源識別碼複製到剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。
7. 選取左側功能表上的 [子網路]。 確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點數。
8. 確認已在具有虛擬網路的 Azure 訂用帳戶中註冊 Azure Batch 提供者。 或者，註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。

   a. 在 Azure 入口網站中，選取左側功能表上的 [訂用帳戶]。

   b. 選取您的訂用帳戶。 
   
   c. 選取左側的 [資源提供者]，並確認 **Microsoft.Batch** 是已註冊的提供者。     
      ![確認「已註冊」狀態](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果您在清單中未看到 **Microsoft.Batch**，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>將 Azure-SSIS IR 加入虛擬網路


1. 啟動 Microsoft Edge 或 Google Chrome。 目前只有這些網頁瀏覽器支援 Data Factory UI。
2. 在 [Azure 入口網站](https://portal.azure.com)中，選取左側功能表中的 [資料處理站]。 如果在功能表上未看到 [資料處理站]，請選取 [更多服務]，然後選取 [智慧 + 分析] 區段中的 [資料處理站]。 
    
   ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. 在清單中選取具有 Azure-SSIS 整合執行階段的資料處理站。 您會看到資料處理站的首頁。 選取 [編寫與部署] 圖格。 您會在個別的索引標籤上看到 Data Factory UI。 

   ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. 在 Data Factory UI 中，切換至 [編輯] 索引標籤，選取 [連線]，然後切換至 [整合執行階段] 索引標籤。 

   ![[整合執行階段] 索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. 如果您的 Azure-SSIS 整合執行階段正在執行，在整合執行階段清單中，請在 [動作] 資料行中針對您的 Azure SSIS IR 選取 [停止] 按鈕。 您必須停止整合執行階段才能進行編輯。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 在整合執行階段清單中，在 [動作] 資料行中針對您的 Azure-SSIS IR 選取 [編輯] 按鈕。

   ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. 在 [整合執行階段設定] 視窗的 [一般設定] 頁面上，選取 [下一步]。 

   ![IR 設定的一般設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. 在 [SQL 設定] 頁面上，輸入系統管理員密碼，然後選取 [下一步]。

   ![IR 設定的 SQL Server 設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. 在 [進階設定] 頁面上，執行下列動作： 

   a. 選取 [選取您的 Azure-SSIS 整合執行階段要加入的 VNet 並允許 Azure 服務設定 VNet 權限/設定] 核取方塊。

   b. 針對 [類型]，指定虛擬網路是傳統虛擬網路還是 Azure Resource Manager 虛擬網路。 

   c. 針對 [VNet 名稱]，選取您的虛擬網路。

   d. 針對 [子網路名稱]，選取您在虛擬網路中的子網路。

   e. 選取 [更新]。 

   ![IR 設定的進階設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. 現在，您可以針對您的 Azure-SSIS IR 使用 [動作] 資料行中的 [啟動] 按鈕，以啟動 IR。 啟動 Azure-SSIS IR 需要約 20 分鐘。 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>設定虛擬網路
您必須先設定虛擬網路，才能將 Azure-SSIS IR 加入虛擬網路。 若要自動設定虛擬網路權限/設定，讓您的 Azure-SSIS 整合執行階段加入虛擬網路，請新增下列指令碼：

```powershell
# Register to the Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>建立 Azure-SSIS IR 並將其加入虛擬網路
您可以建立 Azure-SSIS IR，同時將其加入虛擬網路。 在完整的指令碼和指示，請參閱[建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md#azure-powershell)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>將現有的 Azure-SSIS IR 加入虛擬網路
[建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)一文中的指令碼會說明如何以相同的指令碼建立 Azure-SSIS IR 並將其加入虛擬網路。 如果您有現有的 Azure-SSIS IR，請執行下列步驟將其加入虛擬網路： 

1. 停止 Azure-SSIS IR。
2. 設定要加入虛擬網路的 Azure-SSIS IR。 
3. 啟動 Azure-SSIS IR。 

### <a name="define-the-variables"></a>定義變數

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

#### <a name="guidelines-for-selecting-a-subnet"></a>選取子網路的指導方針
-   請勿選取 GatewaySubnet 來部署 Azure-SSIS Integration Runtime，因為它是虛擬網路閘道所專用。
-   確定您選取的子網路有足夠的可用位址空間，以供 Azure-SSIS IR 使用。 在可用的 IP 位址中保留至少 2 * IR 節點數目。 Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱[在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)


### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR
必須先停止 Azure-SSIS 整合執行階段，才能將其加入虛擬網路。 此命令會釋出其所有節點並停止計費：

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>進行虛擬網路設定讓 Azure-SSIS IR 可以加入
向 Azure Batch 資源提供者註冊：

```powershell
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

### <a name="configure-the-azure-ssis-ir"></a>設定 Azure-SSIS IR
若要設定要加入虛擬網路的 Azure-SSIS 整合執行階段，請執行 `Set-AzureRmDataFactoryV2IntegrationRuntime` 命令： 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>啟動 Azure-SSIS IR
若要啟動 Azure-SSIS 整合執行階段，請執行下列命令： 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
此命令約需要 20 到 30 分鐘才能完成。

## <a name="use-azure-expressroute-with-the-azure-ssis-ir"></a>將 Azure ExpressRoute 與 Azure-SSIS IR 搭配使用

您可以將 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 線路連線至虛擬網路基礎結構，以將內部部署網路延伸至 Azure。 

常見的設定是使用強制通道 (通告 BGP 路由，0.0.0.0/0 至 VNet)，其可將 VNet 流程的輸出網際網路流量強制傳送到內部網路設備，以用於檢查和記錄。 此流量會中斷 VNet 中之 Azure-SSIS IR 與相依 Azure Data Factory 服務間的連線能力。 解決方法是在包含 Azure-SSIS IR 的子網路上，定義一個 (或多個) [使用者定義路由 (UDR)](../virtual-network/virtual-networks-udr-overview.md)。 UDR 會定義要代替 BGP 路由使用的子網路特定路由。

如果可行，請使用下列設定：
-   ExpressRoute 設定會通告 0.0.0.0/0，且預設會使用強制通道將所有輸出流量傳送至內部部署。
-   套用至包含 Azure-SSIS IR 之子網路的 UDR，會使用下一個設為「網際網路」的躍點類型來定義 0.0.0.0/0 路由。
- 
這些步驟的組合效果，會使子網路層級 UDR 優先於 ExpressRoute 強制通道，因而確保來自 Azure-SSIS IR 的輸出網際網路存取。

如果您擔心會遺失檢查來自該子網路之輸出網際網路流量的能力，您也可以在子網路上加入 NSG 規則，來將輸出目的地限制為 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\)。

如需範例，請參閱[這個 PowerShell 指令碼](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) \(英文\)。 您必須每週執行這個指令碼，讓這份 Azure 資料中心 IP 位址清單保持在最新狀態。

## <a name="next-steps"></a>後續步驟
如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列主題： 

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文將逐步說明如何建立 Azure-SSIS IR。 它會使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [建立 Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)。 本文會補充教學課程，並提供使用 Azure SQL Database 受控執行個體 (私人預覽) 以及將 IR 加入虛擬網路的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 此外也會說明如何藉由新增節點來相應放大 Azure-SSIS IR。 
