---
title: 使用 Execute SSIS 套件活動執行 SSIS 套件 - Azure | Microsoft Docs
description: 本文描述如何使用 Execute SSIS 套件活動，在 Azure Data Factory 管線執行 SQL Server Integration Services (SSIS) 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d2fe8da1c4d49f5b57f907a5940ec9c445d0d1f7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984327"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用 Execute SSIS 套件活動執行 SSIS 套件
本文說明如何使用執行 SSIS 套件活動，在 Azure Data Factory （ADF）管線中執行 SQL Server Integration Services （SSIS）套件。 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您未依照以下教學課程中的逐步指示進行，請建立 Azure SSIS 整合執行階段 (IR)：[教學課程：佈建 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 入口網站中執行套件
在本節中，您會使用 ADF 使用者介面 (UI)/應用程式來建立 ADF 管線，其中具有要執行 SSIS 套件的執行 SSIS 套件活動。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線
在此步驟中，您可以使用 ADF UI/應用程式建立管線。 您要將 Execute SSIS 套件活動新增至管線，並設定它來執行 SSIS 套件。 

1. 在 Azure 入口網站中的 ADF 概觀/首頁上，按一下 [撰寫與監視] 圖格，以在個別索引標籤中啟動 ADF UI/應用程式。 

   ![Data Factory 首頁](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在 [讓我們開始吧] 頁面上，按一下 [建立管線]： 

   ![開始使用頁面](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. 在 [活動] 工具箱中展開 [一般]，然後將 [執行 SSIS 套件] 活動拖放至管線設計工具介面。 

   ![將執行 SSIS 套件活動拖曳至設計工具介面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. 在 Execute SSIS 套件活動的 [一般] 索引標籤上，提供活動的名稱和描述。 設定選用的逾時和重試值。

   ![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. 在 [執行 SSIS 套件活動] 的 [**設定**] 索引標籤上，選取您要在其中執行封裝的 AZURE SSIS IR。 如果您的套件使用 Windows 驗證來存取資料存放區（例如，內部部署的 SQL Server/檔案共用、Azure 檔案儲存體等），請核取 [ **windows 驗證**] 核取方塊，並輸入您的封裝執行認證（**網域**）的值。使用者名稱**密碼）** 。 / / 或者，您可以使用儲存在 Azure Key Vault （AKV）中的秘密作為其值。 若要這麼做，請按一下相關認證旁的 [ **AZURE 金鑰保存庫**] 核取方塊，選取/編輯現有的 AKV 連結服務或建立新的，然後選取認證值的密碼名稱/版本。  當您建立/編輯 AKV 連結服務時，您可以選取/編輯現有的 AKV 或建立一個新的，但如果您尚未這麼做，請將您的 AKV 的「ADF 受控識別」存取權授與您的。 您也可以直接以下列格式輸入您的密碼： `<AKV linked service name>/<secret name>/<secret version>`。 如果您的套件需要 32 位元執行階段以便執行，請核取 [32 位元執行階段] 核取方塊。 

   針對 [**封裝位置**]，選取 [ **SSISDB**]、 **[檔案系統（封裝）** ] 或 **[檔案系統（專案）** ]。 如果您選取 [ **SSISDB** ] 做為套件位置，如果您的 AZURE ssis IR 是以 Azure SQL Database server/受控執行個體所裝載的 ssis 目錄（SSISDB）布建，則您必須指定要執行的套件已部署至 SSISDB。 如果您的 Azure SSIS IR 正在執行，且未核取 [**手動輸入**] 核取方塊，您可以從 SSISDB 流覽並選取現有的資料夾/專案/套件/環境。 按一下 [重新整理] 按鈕，擷取您剛剛從 SSISDB 新增的資料夾/專案/套件/環境，讓它們可供瀏覽和選取。 若要流覽/選取封裝執行的環境，您必須事先設定專案，將這些環境新增為 SSISDB 底下相同資料夾中的參考。 如需詳細資訊，請參閱[建立/對應 SSIS 環境](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)。

   針對 [記錄層級]，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入您的自訂記錄名稱，請核取 [自訂] 核取方塊。 

   ![在 [設定] 索引標籤上設定屬性 - 自動化](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   如果您的 Azure SSIS IR 未執行，或已核取 [**手動輸入**] 核取方塊，您可以直接以下列格式從 SSISDB 輸入您的套件和`<folder name>/<project name>/<package name>.dtsx`環境`<folder name>/<environment name>`路徑：和。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   如果您選取 **[檔案系統（套件）** ] 做為套件位置（如果您的 AZURE SSIS IR 未布建，則會自動選取），您必須提供的通用命名慣例（UNC）路徑給您的，以指定要執行的套件封裝路徑中`.dtsx`的封裝檔案（）。 例如，如果您將封裝儲存在 Azure 檔案儲存體中，其封裝路徑會是`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`。 
   
   如果您在不同的檔案中設定您的封裝，您也必須在設定**路徑**中提供設定檔案`.dtsConfig`（）的 UNC 路徑。 例如，如果您將設定儲存在 Azure 檔案儲存體中，其設定路徑會是`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   如果您選取 [**檔案系統（專案）** ] 做為封裝位置，您必須在**專案路徑**中提供專案檔（`.dtsx` `.ispac`）的 UNC 路徑，並在您的**專案中，將封裝檔案（）指定為要執行的封裝。套件名稱**。 例如，如果您將專案儲存在 Azure 檔案儲存體中，其專案路徑會是`\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   接下來，您必須指定認證以存取您的專案/封裝/設定檔。 如果您先前已輸入封裝執行認證的值（請參閱上述），您可以藉由勾選 [**與封裝執行認證相同**] 核取方塊來重複使用它們。 否則，您必須輸入套件存取認證（**網域**/使用者**名稱**/**密碼**）的值。 例如，如果您將專案/封裝/設定儲存在 Azure 檔案儲存體中，則**網域**會`Azure`是;使用者名稱`<storage account name>`是，而**密碼**是`<storage account key>`。 或者，您可以使用儲存在 AKV 中的秘密作為其值（請參閱上文）。 這些認證將用來存取您的封裝和「執行封裝」工作中的子封裝，全都來自自己的路徑/相同的專案，以及設定，包括在您的套件中指定的套件。 
   
   如果您在透過 SQL Server Data Tools （SSDT）建立套件時使用了**EncryptAllWithPassword** / **EncryptSensitiveWithPassword**保護層級，則必須在加密中輸入密碼的值。 **密碼**。 或者，您可以使用儲存在 AKV 中的秘密作為其值（請參閱上文）。 如果您已使用**EncryptSensitiveWithUserKey**保護等級，則必須在 [設定檔] 或 [ **SSIS 參數**/**連線管理員**/] 屬性中重新輸入您的機密值**覆寫**索引標籤（請參閱下文）。 如果您已使用**EncryptAllWithUserKey**保護層級，則不受支援，因此您必須重新設定套件，以透過 SSDT 或`dtutil`命令列公用程式使用其他保護層級。 
   
   針對 [記錄層級]，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入您的自訂記錄名稱，請核取 [自訂] 核取方塊。 如果您想要使用可在套件中指定的標準記錄提供者來記錄封裝執行，您需要在**記錄路徑**中提供其 UNC 路徑來指定記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑將`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`會是。 在此路徑中，將會為每個個別的封裝執行建立子資料夾，並在執行 SSIS 套件活動執行識別碼之後命名，其中每五分鐘會產生一次記錄檔。 
   
   最後，您也必須指定認證以存取您的記錄檔資料夾。 如果您先前已輸入套件存取認證的值（請參閱上述），您可以藉由勾選 [**與封裝存取認證相同**] 核取方塊來重複使用它們。 否則，您必須輸入記錄存取認證（**網域**/使用者**名稱**/**密碼**）的值。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則**網域**是`Azure`; 使用者**名稱**是`<storage account name>`，而**密碼**是`<storage account key>`。 或者，您可以使用儲存在 AKV 中的秘密作為其值（請參閱上文）。 這些認證將用來儲存您的記錄。 
   
   針對上述的所有 UNC 路徑，完整檔案名必須小於260個字元，且目錄名稱必須小於248個字元。

5. 在 Execute SSIS 套件活動的 [ **SSIS 參數**] 索引標籤上，如果您的 AZURE SSIS IR 正在執行，則會選取**SSISDB**作為您的封裝位置，並取消選取 [**設定**] 索引標籤上的 [**手動專案**] 核取方塊，即現有的 SSIS系統會顯示您在所選項目/套件中從 SSISDB 選取的參數，讓您為它們指派值。 否則，您可以逐一輸入以手動將值指派給它們，請確定它們存在且已正確輸入，您的套件執行才會成功。 
   
   如果您在透過 SSDT 建立套件時使用了**EncryptSensitiveWithUserKey**保護等級，而且已選取**檔案系統（套件）** /**檔案系統（專案）** 做為封裝位置，則您也需要重新輸入您的敏感性參數，以在設定檔或此索引標籤中指派值給它們。 
   
   將值指派給您的參數時，您可以使用運算式、函式、ADF 系統變數及 ADF 管線參數/變數來新增動態內容。 或者，您可以使用儲存在 AKV 中的秘密作為其值（請參閱上文）。

   ![在 [SSIS 參數] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. 在 [執行 SSIS 套件活動] 的 [**連線管理員**] 索引標籤上，如果您的 AZURE SSIS IR 正在執行，則會選取**SSISDB**作為您的封裝位置，並取消選取 [**設定**] 索引標籤上的 [**手動專案**] 核取方塊。系統將會顯示您在所選項目/套件中從 SSISDB 選取的連線管理員，讓您將值指派給其屬性。 否則，您可以逐一輸入它們，以手動將值指派給其屬性-請確定它們存在且已正確輸入，您的套件執行才會成功。 
   
   如果您在透過 SSDT 建立套件時使用了**EncryptSensitiveWithUserKey**保護等級，而且已選取**檔案系統（套件）** /**檔案系統（專案）** 做為封裝位置，則您也需要重新輸入您的敏感性連線管理員屬性，可在設定檔或此索引標籤中指派值給它們。 
   
   將值指派給您的連線管理員屬性時，您可以使用運算式、函式、ADF 系統變數及 ADF 管線參數/變數來新增動態內容。 或者，您可以使用儲存在 AKV 中的秘密作為其值（請參閱上文）。

   ![在 [連線管理員] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. 在 [執行 SSIS 套件] 活動的 [**屬性覆寫**] 索引標籤上，您可以逐一輸入所選封裝中現有屬性的路徑，以手動為其指派值，請確定它們存在且已正確輸入您的套件執行成功，例如，若要覆寫使用者變數的值，請以下列格式輸入其路徑： `\Package.Variables[User::<variable name>].Value`。 
   
   如果您在透過 SSDT 建立套件時使用了**EncryptSensitiveWithUserKey**保護等級，而且已選取**檔案系統（套件）** /**檔案系統（專案）** 做為封裝位置，則您也需要重新輸入您的敏感性屬性，可在設定檔或此索引標籤中指派值給它們。 
   
   將值指派給您的屬性時，您可以使用運算式、函式、ADF 系統變數及 ADF 管線參數/變數來新增動態內容。

   ![在 [屬性覆寫] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   在 [設定檔] 和 [ *SSIS 參數*] 索引標籤上指派的值，可以使用 [**連接管理員**/]**屬性 [覆寫**] 索引標籤來覆寫，而在 [**連接管理員**] 索引標籤也可以使用 [**屬性覆寫**] 索引標籤來覆寫。

8. 若要驗證管線設定，按一下工具列上的 [驗證]。 若要關閉 [管線驗證報告]，按一下 **>>** 。

9. 藉由按一下 [全部發佈] 按鈕，將管線發佈到 ADF。 

### <a name="run-the-pipeline"></a>執行管道
在此步驟中，您會觸發管線執行。 

1. 若要觸發管線執行，按一下工具列上的 [觸發程序]，然後按一下 [立即觸發]。 

   ![立即觸發](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在 [管線執行] 視窗中，選取 [完成]。 

### <a name="monitor-the-pipeline"></a>監視管線

1. 切換至左側的 [監視] 索引標籤。 您會看到管線執行、其狀態，以及其他資訊 (例如執行開始時間)。 若要重新整理檢視，按一下 [重新整理]。

   ![管線執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 按一下 [動作]資料行中的 [檢視活動執行] 連結。 您只會看到一個活動執行，因為該管線只有一個活動 (Execute SSIS 套件活動)。

   ![活動回合](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 您可以依據 Azure SQL Server 中的 SSISDB 資料庫執行下列**查詢**，來確認套件已執行。 

   ```sql
   select * from catalog.executions
   ```

   ![確認封裝執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 您也可以從管線活動執行的輸出取得 SSISDB 執行識別碼，並使用此識別碼來檢查 SQL Server Management Studio （SSMS）中更完整的執行記錄和錯誤訊息。

   ![取得執行識別碼。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線

您也可以建立管線的排程觸發程序，以便管線依排程執行 (每小時、每日等等)。 如需範例，請參閱[建立資料處理站 - 資料處理站 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 執行套件
在本節中，您會使用 Azure PowerShell 來建立 ADF 管線，其中具有要執行 SSIS 套件的執行 SSIS 套件活動。 

依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)中的逐步指示，安裝最新的 Azure PowerShell 模組。

### <a name="create-an-adf-with-azure-ssis-ir"></a>建立具有 Azure-SSIS IR 的 ADF
您可以遵循下列教學課程中的逐步指示，使用已佈建 Azure-SSIS IR 的現有 ADF，或建立具有 Azure-SSIS IR 的新 ADF：[教學課程：透過 PowerShell 將 SSIS 套件部署至 Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線 
在此步驟中，您要使用執行 SSIS 套件活動建立管線。 此活動會執行您的 SSIS 套件。 

1. 使用類似下列範例的內容，在 **C:\ADF\RunSSISPackage** 資料夾中建立名為 **RunSSISPackagePipeline.json** 的 JSON 檔案：

   > [!IMPORTANT]
   > 儲存檔案之前，請先取代物件名稱/描述/路徑、屬性/參數值、密碼和其他變數值。 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

若要執行儲存在檔案系統/檔案共用/Azure 檔案儲存體中的封裝，您可以輸入封裝/記錄檔位置屬性的值，如下所示。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

若要在儲存于檔案系統/檔案共用/Azure 檔案儲存體的專案內執行封裝，您可以如下所示輸入套件位置屬性的值。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. 在 Azure PowerShell 中，切換至 `C:\ADF\RunSSISPackage` 資料夾。

3. 若要建立管線**RunSSISPackagePipeline**，請執行**set-azdatafactoryv2pipeline** Cmdlet。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   以下是範例輸出：

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>執行管道
使用**set-azdatafactoryv2pipeline** Cmdlet 來執行管線。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>監視管線

執行下列 PowerShell 程式碼以持續檢查管線執行狀態，直到完成複製資料為止。 在 PowerShell 視窗中複製/貼上下列指令碼，然後按 ENTER。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

您也可以使用 Azure 入口網站監視管線。 如需逐步指示，請參閱[監視管線](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)：

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線
在上一個步驟中，您已依需求執行管線。 您也可以建立排程觸發程序，依排程執行管線 (每小時、每天等)。

1. 使用下列內容，在 **C:\ADF\RunSSISPackage** 資料夾中建立名為 **MyTrigger.json** 的 JSON 檔案： 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. 在 **Azure PowerShell** 中，切換至 **C:\ADF\RunSSISPackage** 資料夾。
3. 執行**start-azdatafactoryv2trigger 指令程式**，以建立觸發程式。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. 觸發程序預設處於已停止狀態。 執行**start-azdatafactoryv2trigger** Cmdlet 來啟動觸發程式。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. 藉由執行**start-azdatafactoryv2trigger** Cmdlet，確認觸發程式已啟動。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. 在下一個小時後，執行下列命令。 例如，如果目前的時間是 UTC 下午 3:25，請在 UTC 下午 4:00 執行命令。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   您可以依據 Azure SQL Server 中的 SSISDB 資料庫執行下列查詢，確認已執行套件。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>後續步驟
請參閱下列部落格文章：
-   [使用 ADF 管線中的 SSIS 活動來現代化及擴充您的 ETL/ELT 工作流程](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
