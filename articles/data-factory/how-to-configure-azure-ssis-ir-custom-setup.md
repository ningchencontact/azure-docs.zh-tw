---
title: Azure-SSIS 整合執行階段的自訂安裝 | Microsoft Docs
description: 本文說明如何使用 Azure-SSIS 整合執行階段 (IR) 的自訂安裝介面
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: b377b5ca9d46d66fe99a8f60383076920b098a7d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33769710"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 整合執行階段的自訂安裝

Azure SSIS 整合執行階段的自訂安裝介面可讓您變更預設作業組態或環境 (例如，用以啟動其他 Windows 服務)，或是在 Azure-SSIS IR 的每個節點上安裝其他元件 (例如組件、驅動程式或擴充功能)。 整體而言，它所提供的介面可讓您自行新增在佈建或重新設定 Azure-SSIS IR 期間的安裝步驟。

您可以準備指令碼及其相關聯的檔案，並將其上傳至您 Azure 儲存體帳戶中的 Blob 容器中，以設定您的自訂安裝。 當您佈建或重新設定 Azure-SSIS IR 時，您可以提供容器的共用存取簽章 (SAS) 統一資源識別項 (URI)。 隨後，Azure-SSIS IR 的每個節點會從您的容器下載指令碼及其相關聯的檔案，並使用提高的權限執行您的自訂安裝。 自訂安裝完成後，每個節點會將執行的標準輸出和其他記錄上傳到您的容器中。

您可以安裝免費或未授權的元件，和付費或授權的元件。 如果您是 ISV，請參閱[如何開發 Azure-SSIS IR 的付費或授權元件](how-to-develop-azure-ssis-ir-licensed-components.md)。


## <a name="current-limitations"></a>目前的限制

-   如果您想要使用 `gacutil.exe` 在全域組件快取 (GAC) 中安裝組件，您必須在自訂安裝的過程中提供它，或者使用公開預覽容器中提供的副本。

-   如果您需要將使用自訂安裝的 Azure-SSIS IR 加入 VNet，目前僅支援 Azure Resource Manager VNet。 傳統 VNet 不受支援。

## <a name="prerequisites"></a>先決條件

若要自訂 Azure-SSIS IR，您必須具備下列條件：

-   [Azure 訂用帳戶](https://azure.microsoft.com/)

-   [Azure SQL Database 或受控執行個體伺服器](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [佈建您的 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 進行自訂安裝時，您可以將自訂安裝指令碼及其相關聯的檔案上傳並儲存在 Blob 容器中。 自訂安裝程序也會將其執行記錄上傳至相同的 Blob 容器。

## <a name="instructions"></a>範例的指示

2.  下載並安裝 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (5.4 版或更新版本)。

3.  準備您的自訂安裝指令碼和及相關聯的檔案 (例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 檔案)。

    1.  您必須具有名為 `main.cmd` 的指令碼檔案，這是您自訂安裝的進入點。

    2.  如果您想要將其他工具 (例如 `msiexec.exe`) 所產生的其他記錄上傳到您的容器中，請將預先定義的環境變數 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 指定為指令碼中的記錄資料夾 (例如 `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`)。

4.  下載、安裝並啟動 [Azure 儲存體總管](http://storageexplorer.com/)。

    1.  在 [(本機與已連結)] 下方，以滑鼠右鍵選取 [儲存體帳戶]，然後選取 [連線到 Azure 儲存體]。

       ![連線到 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  選取 [使用儲存體帳戶名稱和金鑰]，然後選取 [下一步]。

       ![使用儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  輸入您的 Azure 儲存體帳戶名稱和金鑰，選取 [下一步]，然後選取 [連線]。

       ![提供儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  在已連線的 Azure 儲存體帳戶下，以滑鼠右鍵按一下 [Blob 容器]，選取 [建立 Blob 容器]，然後為新容器命名。

       ![建立 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  選取新容器，並上傳您的自訂安裝指令碼及其相關聯的檔案。 請確實將 `main.cmd` 上傳至容器的最上層，而非任何資料夾中。 

       ![將檔案上傳至 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  以滑鼠右鍵按一下容器，然後選取 [取得共用存取簽章]。

       ![取得容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  為您的容器建立具有夠長的到期時間和讀取 + 寫入 + 列出權限的 SAS URI。 您必須具有 SAS URI，才能在 Azure-SSIS IR 的任何節點重新安裝映像時下載並執行您的自訂安裝指令碼及其相關聯的檔案。 您必須具備寫入權限才能上傳安裝執行記錄。

       ![產生容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  複製並儲存容器 SAS URI。

       ![複製並儲存共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  當您使用 PowerShell 佈建或重新設定 Azure-SSIS IR 時，在啟動 Azure-SSIS IR 之前，請先執行 `Set-AzureRmDataFactoryV2IntegrationRuntime` Cmdlet，並以容器的 SAS URI 作為新 `SetupScriptContainerSasUri` 參數的值。 例如︰

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  在自訂安裝完成且 Azure-SSIS IR 啟動後，您可以在儲存體容器的 `main.cmd.log` 資料夾中找到 `main.cmd` 的標準輸出和其他執行記錄。

2.  若要查看其他自訂安裝範例，請透過 Azure 儲存體總管連線至公用預覽容器。

    a.  在 [(本機與已連結)] 下方，以滑鼠右鍵按一下 [儲存體帳戶]，然後依序選取 [連線到 Azure 儲存體]、[使用連接字串或共用存取簽章 URI] 和 [下一步]。

       ![使用共用存取簽章連線至 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  選取 [使用 SAS URI]，並為公用預覽容器輸入下列 SAS URI。 選取 [下一步]，然後選取 [連線]。

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![提供容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. 選取已連線的公用預覽容器，然後按兩下 `CustomSetupScript` 資料夾。 此資料夾中包含下列項目：

       1. 一個 `Sample` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝基本工作。 此工作不會執行任何動作，而會休眠數秒鐘。 資料夾還包含 `gacutil` 資料夾，其中包含 `gacutil.exe`。

       2. 一個 `UserScenarios` 資料夾，其中包含實際使用者案例的數個自訂安裝。

    ![公開預覽容器的內容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. 按兩下 `UserScenarios` 資料夾。 此資料夾中包含下列項目：

       1. 一個 `.NET FRAMEWORK 3.5` 資料夾，其中包含要安裝舊版 .NET Framework (Azure-SSIS IR 每個節點上的自訂元件可能需要它) 的自訂安裝。

       2. 一個 `BCP` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝 SQL Server 命令列公用程式 (`MsSqlCmdLnUtils.msi`)，包括大量複製程式 (`bcp`)。

       3. 一個 `EXCEL` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝開放原始碼組件 (`DocumentFormat.OpenXml.dll`、`ExcelDataReader.DataSet.dll` 和 `ExcelDataReader.dll`)。

       4. 一個 `MSDTC` 資料夾，其中包含自訂安裝，可修改 Azure-SSIS IR 每個節點上 Microsoft Distributed Transaction Coordinator (MSDTC) 執行個體的網路與安全性組態。

       5. 一個 `ORACLE ENTERPRISE` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`) 和無訊息安裝組態檔 (`client.rsp`)，用以在 Azure-SSIS IR Enterprise Edition (私人預覽) 的每個節點上安裝 Oracle OCI 驅動程式。 此安裝可讓您使用 Oracle 連線管理員、來源和目的地。 首先，您必須先從 [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 下載 `winx64_12102_client.zip`，再將其連同 `main.cmd` 和 `client.rsp` 一起上傳到您的容器中。 如果您使用 TNS 連線至 Oracle，則也必須下載 `tnsnames.ora`、加以編輯，然後將其上傳到您的容器中，使其能夠在安裝期間複製到 Oracle 安裝資料夾中。

       6. 一個 `ORACLE STANDARD` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODP.NET 驅動程式。 此安裝可讓您使用 ADO.NET 連線管理員、來源和目的地。 首先，從 [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) 下載 `ODP.NET_Managed_ODAC122cR1.zip`，再將其連同 `main.cmd` 上傳到您的容器中。

       7. 一個 `SAP BW` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR Enterprise Edition (私人預覽) 的每個節點上安裝 SAP .NET 連接器組件 (`librfc32.dll`)。 此安裝可讓您使用 SAP BW 連線管理員、來源和目的地。 首先，從 SAP 安裝資料夾將 64 位元或 32 位元版的 `librfc32.dll` 連同 `main.cmd` 上傳到您的容器中。 接著，此指令碼會在安裝期間將 SAP 組件複製到 `%windir%\SysWow64` 或 `%windir%\System32` 資料夾中。

       8. 一個 `STORAGE` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝 Azure PowerShell。 此安裝可讓您部署和執行 SSIS 套件，以執行 [PowerShell 指令碼來管理您的 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 將 `main.cmd` (一個範例 `AzurePowerShell.msi`) 和 `storage.ps1` 複製到您的容器 (或安裝最新版本)。 使用 PowerShell.dtsx 作為您套件的範本。 此套件範本結合了 [Azure Blob 下載工作](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task) (會下載 `storage.ps1` 作為可修改的 PowerShell 指令碼) 和[執行程序工作](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) (會在每個節點上執行指令碼)。

       9. 一個 `TERADATA` 資料夾，其中包含自訂安裝指令碼 `main.cmd)` 及其相關聯的檔案 (`install.cmd`)，和安裝程式套件 (`.msi`)。 這些檔案會在 Azure-SSIS IR Enterprise Edition (私人預覽) 的每個節點上安裝 Teradata 連接器、TPT API 和 ODBC 驅動程式。 此安裝可讓您使用 Teradata 連線管理員、來源和目的地。 首先，從 [Teradata](http://partnerintelligence.teradata.com) 下載 Teradata Tools and Utilities (TTU) 15.x zip 檔案 (例如 `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`)，然後將其連同上述的 `.cmd` 和 `.msi` 檔案一起上傳到您的容器中。

    ![使用者案例資料夾中的資料夾](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. 若要嘗試使用這些自訂安裝範例，請複製所選資料夾中的內容，並貼到您的容器中。 當您使用 PowerShell 佈建或重新設定 Azure-SSIS IR 時，請執行 `Set-AzureRmDataFactoryV2IntegrationRuntime` Cmdlet，並以容器的 SAS URI 作為新 `SetupScriptContainerSasUri` 參數的值。

## <a name="next-steps"></a>後續步驟

-   [Azure-SSIS 整合執行階段的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [如何開發 Azure-SSIS 整合執行階段的付費或授權自訂元件](how-to-develop-azure-ssis-ir-licensed-components.md)