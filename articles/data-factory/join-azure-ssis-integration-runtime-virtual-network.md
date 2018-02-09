---
title: "將 Azure-SSIS 整合執行階段加入 VNET | Microsoft Docs"
description: "了解如何將 Azure-SSIS 整合執行階段加入 Azure 虛擬網路。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 2131aa75dcfb975f11cff9800087c3e4e7170378
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路
在下列案例中，將 Azure-SSIS 整合執行階段 (IR) 加入 Azure 虛擬網路 (VNet)： 

- 您要在屬於 VNet 一部分的 SQL Server 受控執行個體 (私人預覽) 上裝載 SSIS 目錄資料庫。
- 您想要從 Azure-SSIS 整合執行階段上執行的 SSIS 套件連線至內部部署資料存放區。

 Azure Data Factory 第 2 版 (預覽) 可讓您將 Azure-SSIS 整合執行階段加入傳統 VNet 或 Azure Resource Manager VNet 中。 

 > [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用 Data Factory 服務的 1 版 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

## <a name="access-on-premises-data-stores"></a>存取內部部署資料存放區
如果 SSIS 套件只會存取公用雲端資料存放區，則您不需要將 Azure-SSIS IR 加入 VNet 中。 如果 SSIS 套件存取內部部署資料存放區，則您必須將 Azure-SSIS IR 加入連線至內部部署網路的 VNet 中。 如果在不在 VNet 的 Azure SQL Database 中裝載 SSIS 目錄，您需要開啟適當的連接埠。 如果在 Azure Resource Manager VNet 或傳統 VNet 的 Azure SQL 受控執行個體中裝載 SSIS 目錄，您可以將 Azure-SSIS IR 加入相同的 VNet 或不同的 VNet，而此 VNet 與包含 Azure SQL 受控執行個體的 VNet 之間有 VNet 對 VNet 連線。 下列各節提供更多詳細資料。

以下是一些需要注意的重要事項： 

- 如果沒有 VNet 連線到您的內部部署網路，請先建立 Azure-SSIS 整合執行階段要加入的 [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) 或[傳統 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定從該 VNet 到您內部部署網路的站對站 [VPN 閘道連線](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 連線。
- 如果在與 Azure-SSIS 整合執行階段相同的位置中有現有的 Azure Resource Manager 或傳統 VNet 連線到您的內部部署網路，則可以將整合執行階段加入該 VNet。
- 如果在與 Azure-SSIS 整合執行階段不同的位置中有現有傳統 VNet 連線到您的內部部署網路，則可以先建立 Azure-SSIS 整合執行階段要加入的[傳統 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定[傳統對傳統 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 連線。 或者，您可以建立 [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) 讓您的 Azure-SSIS 整合執行階段加入。 然後，設定[傳統對 Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 連線。
- 如果在與 Azure-SSIS 整合執行階段不同的位置中有現有 Azure Resource Manager VNet 連線到您的內部部署網路，則可以先建立 Azure-SSIS 整合執行階段要加入的 [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md##create-a-virtual-network)。 然後，設定 Azure Resource Manager 對 Azure Resource Manager VNet 連線。 或者，您可以建立[傳統 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) 讓您的 Azure-SSIS 整合執行階段加入。 然後，設定[傳統對 Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 連線。

## <a name="domain-name-services-server"></a>網域名稱服務伺服器 
如果您需要在 Azure-SSIS 整合執行階段所加入的 VNet 中使用自己的網域名稱系統 (DNS) 伺服器，請遵循指南以[確定 VNet 中 Azure-SSIS 整合執行階段的節點可以解析 Azure 端點](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>網路安全性群組
如果您需要在 Azure-SSIS 整合執行階段所加入的 VNet 中實作網路安全性群組 (NSG)，請允許透過下列連接埠的輸入/輸出流量：

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 輸入來源/輸出目的地 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100、20100、30100 (如果您將整合執行階段加入傳統 VNet)<br/><br/>29876、29877 (如果您將整合執行階段加入 Azure Resource Manager VNet) | 輸入 | TCP | Azure 服務會使用這些連接埠與 VNet 中的 Azure SSIS 整合執行階段節點進行通訊。 | Internet | 
| 443 | 輸出 | TCP | VNet 中的 Azure-SSIS 整合執行階段節點會使用此連接埠來存取 Azure 服務 (例如，Azure 儲存體、事件中樞等等)。 | 網際網路 | 
| 1433<br/>11000-11999<br/>14000-14999  | 輸出 | TCP | VNet 中的 Azure-SSIS 整合執行階段節點會使用這些連接埠來存取 Azure SQL Database 伺服器所裝載的 SSISDB (不適用於 Azure SQL 受控執行個體所裝載的 SSISDB)。 | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure 入口網站 (Data Factory UI)
本節會示範如何使用 Azure 入口網站及 Data Factory UI，將現有的 Azure SSIS 執行階段加入 VNet (傳統或 Azure Resource Manager)。 首先，在將 Azure SSIS 整合執行階段加入 VNet 之前，必須先適當設定 VNet。 根據您的 VNet 類型 (傳統或 Azure Resource Manager)，執行下列兩節的其中一節。 然後，繼續執行第三節，將您的 Azure SSIS 整合執行階段加入 VNet。 

### <a name="use-portal-to-configure-a-classic-vnet"></a>使用入口網站設定傳統 VNet
您必須先設定 VNet，才可以將 Azure-SSIS 整合執行階段加入 VNet。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [更多服務]。 篩選並選取 [虛擬網路 (傳統)]。
3. 篩選並選取清單中的 [虛擬網路]。 
4. 在 [虛擬網路 (傳統)] 頁面中，選取 [屬性]。 

    ![傳統 VNet 資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. 按一下 [資源識別碼] 的 [複製] 按鈕，將傳統網路的資源識別碼複製至剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。
6. 按一下左功能表上的 [子網路]，並確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點。

    ![VNet 中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. 將 **MicrosoftAzureBatch** 加入 VNet 的**傳統虛擬機器參與者**角色。 
    1. 按一下左功能表上的 [存取控制 (IAM)]，然後按一下工具列中的 [新增]。
    
        ![存取控制 -> 新增](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. 在 [新增權限] 頁面中，針對 [角色] 選取 [傳統虛擬機器參與者]。 在 [選取] 文字方塊中複製/貼上 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然後從搜尋結果清單中選取 [Microsoft Azure Batch]。 
    
        ![新增權限 - 搜尋](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. 按一下 [儲存] 以儲存設定並關閉頁面。
    
        ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 確認您在參與者清單中看到 **MicrosoftAzureBatch**。
    
        ![確認 AzureBatch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. 確認在具有 VNet 的 Azure 訂用帳戶中註冊 Azure Batch 提供者，或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。
    1. 在 Azure 入口網站中，按一下入口網站中，按一下左功能表上的 [訂用帳戶]。 
    2. 選取您的 **訂用帳戶**。 
    3. 按一下左邊的 [資源提供者]，並確認 `Microsoft.Batch` 是已註冊的提供者。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    如果您在清單中看不到 `Microsoft.Batch`，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>使用入口網站設定 Azure Resource Manager VNet
您必須先設定 VNet，才可以將 Azure-SSIS 整合執行階段加入 VNet。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [更多服務]。 篩選並選取 [虛擬網路]。
3. 篩選並選取清單中的 [虛擬網路]。 
4. 在 [虛擬網路] 頁面中，選取 [屬性]。 
5. 按一下 [資源識別碼] 的 [複製] 按鈕，將虛擬網路的資源識別碼複製至剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。
6. 按一下左功能表上的 [子網路]，並確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點。
5. 確認在具有 VNet 的 Azure 訂用帳戶中註冊 Azure Batch 提供者，或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。
    1. 在 Azure 入口網站中，按一下入口網站中，按一下左功能表上的 [訂用帳戶]。 
    2. 選取您的 **訂用帳戶**。 
    3. 按一下左邊的 [資源提供者]，並確認 `Microsoft.Batch` 是已註冊的提供者。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    如果您在清單中看不到 `Microsoft.Batch`，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>將 Azure SSIS 整合執行階段加入 VNet


1. 在 [Azure 入口網站](https://portal.azure.com)中，選取左側功能表中的 [資料處理站]。 如果在功能表上沒有看到 [資料處理站]，選取 [更多服務]，然後選取 [智慧 + 分析] 區段中的 [資料處理站]。 
    
    ![資料處理站清單](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. 在清單中選取您的資料處理站與 Azure SSIS 整合執行階段。 您會看到資料處理站的首頁。 選取 [編寫與部署] 圖格。 您會在另一個索引標籤中看到 Data Factory 使用者介面 (UI)。 

    ![Data Factory 首頁](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. 在 Data Factory UI 中，切換至 [編輯] 索引標籤，選取 [連線]，然後切換至 [整合執行階段] 索引標籤。 

    ![整合執行階段索引標籤](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. 如果您的 Azure SSIS 整合執行階段正在執行，在整合執行階段清單中，請在 [動作] 資料行中針對您的 Azure SSIS 整合執行階段選取 [停止] 按鈕。 您必須停止整合執行階段才能進行編輯。 

    ![停止整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 在整合執行階段清單中，請在 [動作] 資料行中針對您的 Azure SSIS 整合執行階段選取 [編輯] 按鈕。

    ![編輯整合執行階段](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. 在 [整合執行階段設定] 視窗的 [一般設定] 頁面上，選取 [下一步]。 

    ![整合執行階段設定 - 一般設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. 在 [SQL 設定] 頁面上，輸入系統管理員**密碼**，然後選取 [下一步]。

    ![整合執行階段設定 - SQL 設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. 在 [進階設定] 頁面上，執行下列動作： 

    1. 選取 [選取您的 Azure-SSIS 整合執行階段要加入的 VNet 並允許 Azure 服務設定 VNet 權限/設定] 核取方塊。 
    2. 針對 [類型]，請指定 VNet 是傳統 VNet 或 Azure Resource Manager VNet。 
    3. 針對 [VNet 名稱]，請選取您的 VNet。
    4. 針對 [子網路名稱]，請選取 VNet 中的子網路。 
    5. 選取 [更新]。 

        ![整合執行階段設定 - 進階設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. 現在，您可以針對您的 Azure SSIS 整合執行階段使用 [動作] 資料行中的 [啟動] 按鈕，以便啟動整合執行階段。 啟動 Azure SSIS 整合執行階段需要約 20 分鐘。 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>設定 VNet
您必須先設定 VNet，才可以將 Azure-SSIS 整合執行階段加入 VNet。 新增下列指令碼來自動設定 VNet 權限/設定，讓您的 Azure-SSIS 整合執行階段可以加入此 VNet。

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>建立 Azure-SSIS IR 並將其加入 VNet
您可以建立 Azure-SSIS IR，並同時將其加入 VNet。 如需建立 Azure SSIS-IR 並同時將其加入 VNet 的完整指令碼和指示，請參閱[建立 Azure-SSIS IR](create-azure-ssis-integration-runtime.md#azure-powershell)。

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>將現有 Azure-SSIS IR 加入 VNet
[建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)一文中的指令碼會示範如何建立 Azure-SSIS IR 並在相同指令碼中將其加入 VNet。 如果您有現有的 Azure-SSIS，執行下列步驟將它加入 VNet。 

1. 停止 Azure-SSIS IR。
2. 設定將 Azure-SSIS IR 加入 VNet。 
3. 啟動 Azure-SSIS IR。 

### <a name="define-the-variables"></a>定義變數

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR
先停止 Azure-SSIS 整合執行階段，再將其加入 VNet。 此命令會釋出其所有節點並停止計費。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>進行 VNet 設定讓 Azure-SSIS IR 可以加入
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
執行 Set-AzureRmDataFactoryV2IntegrationRuntime 命令可以設定將 Azure-SSIS 整合執行階段加入 VNet： 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>啟動 Azure-SSIS IR
執行下列命令以啟動 Azure-SSIS 整合執行階段： 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
此命令需花費 **20 至 30 分鐘**來完成。

## <a name="next-steps"></a>後續步驟
如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列主題： 

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文逐步說明如何建立 Azure-SSIS IR，並使用 Azure SQL Database 裝載 SSIS 目錄。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 本文展開教學課程，並提供使用 Azure SQL 受控執行個體 (私人預覽) 以及將 IR 加入 VNet 的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 它也會示範如何將更多節點新增至 IR，藉此相應放大 Azure-SSIS IR。 
