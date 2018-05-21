---
title: 從 Azure 下載市集項目 | Microsoft Docs
description: 我可以將市集項目從 Azure 下載到我的 Azure Stack 部署。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 2e92dc96a69400f689e49b70d1b855c955084362
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>將市集項目從 Azure 下載到 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*


當您決定要將哪些內容包含在 Azure Stack 市集中時，應該考慮可從 Azure Marketplace 取得的內容。 您可以從已預先測試而能在 Azure Stack 上執行的 Azure Marketplace 項目策劃清單下載。 新項目會不斷新增到此清單中，因此請務必回來查看是否有新內容。

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>在連線的案例 (具有網際網路連線能力) 中下載 Marketplace 項目

1. 若要下載市集項目，您必須先[向 Azure 註冊 Azure Stack](azure-stack-register.md)。
2. 登入 Azure Stack 系統管理員入口網站 (若使用 ASDK，請使用 https://portal.local.azurestack.external)。
3. 有些 Marketplace 項目可能很大。 按一下 [資源提供者] > [儲存體] 來檢查，以確定您的系統上有足夠的空間。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. 按一下 [更多服務] > [市集管理]。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 按一下 [從 Azure 新增] 以查看可供下載的項目清單。 您可以按一下清單中的每個項目來檢視其描述與下載大小。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 選取清單中您想要的項目，然後按一下 [下載]。 您所選項目的 VM 映像會開始下載。 下載時間會有所不同。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 下載完成之後，您可以用 Azure Stack 操作員或使用者身分來部署新市集項目。 按一下 [+新增]，在類別中搜尋新市集項目，然後選取該項目。
7. 按一下 [建立] 以開啟新下載項目的建立體驗。 依照逐步指示來部署您的項目。

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>在連線的情況 (具有網際網路連線) 下載 Marketplace 項目

當您在中斷連線的模式 (沒有任何網際網路連線) 中部署 Azure Stack 時，您無法透過 Azure Stack 入口網站下載 Marketplace 項目。 不過，您可使用 Marketplace 摘要整合工具將 Marketplace 項目下載至具有網際網路連線的電腦，再將其傳輸至 Azure Stack 環境。

### <a name="prerequisites"></a>先決條件
確定您已[向您的 Azure 訂用帳戶註冊 Azure Stack](azure-stack-register.md)，才可以使用 Marketplace 摘要整合工具。  

從具有網際網路連線的電腦，使用下列步驟來下載必要的 Marketplace 項目：

1. 以系統管理員身分開啟 PowerShell 主控台，然後[安裝 Azure Stack 特有的 PowerShell 模組](azure-stack-powershell-install.md)。 請確定您已安裝 **Azure Stack PowerShell 模組 1.2.11 版或更高版本**。  

2. 新增您用來註冊 Azure Stack 的 Azure 帳戶。 若要新增帳戶，請執行未使用任何參數的 **Add-AzureRmAccount** Cmdlet。 當系統提示您輸入 Azure 帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。  

3. 如果您有多個訂用帳戶，請執行下列命令以選取您用於註冊的訂用帳戶：  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 使用下列指令碼來下載最新版的 Marketplace 摘要整合工具：  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-master

   ```

5. 匯入摘要整合模組並執行下列命令來啟動工具：  

   ```powershell
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 執行工具時，系統會提示您輸入 Azure 帳號認證。 登入您用來註冊 Azure Stack 的 Azure 帳戶。 登入成功之後，您應會看到下列包含可用 Marketplace 項目清單的畫面。  

   ![Azure Marketplace 項目快顯視窗](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. 選取您想要下載的映像，並記下映像版本。 您可以按住 Ctrl 鍵來選取多個映像。 您可以使用映像版本，在下一節中匯入映像。  接著，按一下 [確定]，然後按一下 [是] 接受法律條款。 您也可以使用 [新增條件] 選項來篩選映像清單。 

   視映像的大小而定，下載需要一些時間。 下載映像後，可以在您先前提供的目的地路徑中取得該映像。 此下載包含 VHD 檔案 (用於虛擬機器) 或 .ZIP 檔案 (用於虛擬機器擴充功能)，以及 Azpkg 格式的資源庫項目。

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>匯入映像並將它發佈至 Azure Stack Marketplace
市集中有三種不同類型的項目：虛擬機器、虛擬機器擴充功能、解決方案範本。 以下討論解決方案範本。
> [!NOTE]
> 目前無法在 Azure Stack 中新增虛擬機器擴充功能。

1. 下載映像和資源庫套件之後，將它們及 AzureStack-Tools-master 資料夾中的內容儲存到卸除式磁碟機，並且複製到 Azure Stack 環境 (您可以將它複製到本機任何位置，例如:"C:\MarketplaceImages")。     

2. 匯入映像之前，您必須使用[設定 Azure Stack 操作人員的 PowerShell 環境](azure-stack-powershell-configure-admin.md)中所述的步驟來連線到 Azure Stack 操作人員的環境。  

3. 如果下載中包含名為 fixed3.vhd 的 3MB VHD 小型檔案，它就是解決方案範本。 不需要這個檔案，跳至步驟 5。 請確定您已依照下載說明的指示下載所有相依項目。

4. 使用新增 Add-AzsVMImage Cmdlet 將映像匯入至 Azure Stack。 使用此 Cmdlet 時，務必以您所匯入映像的值，取代 publisher、offer 及其他參數值。 您可以從先前下載之 Azpkg 檔案的 imageReference 物件中取得映像的 publisher、offer和 sku 值，以及從上一節中的步驟 6 取得 version 版。

若要尋找 imageReference，您需要將 AZPKG 檔案重新命名為 .ZIP 副檔名，將它解壓縮至暫存位置，並使用文字編輯器開啟 DeploymentTemplates\CreateUiDefinition.json 檔案。 找出此區段：

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   取代參數值並執行下列命令：

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

5. 使用入口網站將您的 Marketplace 項目 (.Azpkg) 上傳至 Azure Stack Blob 儲存體。 您可以上傳至本機 Azure Stack 儲存體，或上傳至 Azure 儲存體。 (它是套件的暫存位置。)請確定 Blob 可公開存取並記下 URI。  

6. 使用 **Add-AzsGalleryItem**，將 Marketplace 項目發佈至 Azure Stack。 例如︰

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

7. 發佈資源庫項目之後，您可以從 [新增] > [Marketplace] 窗格進行檢視。 如果您的下載是解決方案範本，請確定您也下載了相依的 VHD 映像。

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>後續步驟

[建立及發佈 Marketplace 項目](azure-stack-create-and-publish-marketplace-item.md)
