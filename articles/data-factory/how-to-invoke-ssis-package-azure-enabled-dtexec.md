---
title: 使用啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services （SSIS）套件 |Microsoft Docs
description: 瞭解如何使用啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services （SSIS）套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 740e53728356755bcc42e1e0aafb64992b30e113
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249024"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>使用啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services （SSIS）套件
本文說明啟用 Azure 的**dtexec** （**AzureDTExec**）命令提示字元公用程式。  它可用來在 Azure Data Factory （ADF）中的 Azure SSIS Integration Runtime （IR）上執行 SSIS 套件。

傳統**dtexec**公用程式隨附 SQL Server，請參閱[dtexec 公用程式](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)檔以取得詳細資訊。  這通常是由協力廠商協調器/排程器叫用（例如，作用中批次、控制 M 等），以便在內部部署執行 SSIS 套件。  新式**AzureDTExec**公用程式隨附 SQL SERVER MANAGEMENT STUDIO （SSMS）工具。  它也可以由協力廠商協調器/排程器叫用，以便在 Azure 中執行 SSIS 套件。  它有助於提升將 SSIS 套件轉移至雲端的 &。  在遷移之後，如果您想要在日常作業中繼續使用協力廠商協調器/排程器，他們現在可以叫用**AzureDTExec** ，而不是**dtexec**。

**AzureDTExec**會以 adf 管線中的執行 ssis 套件活動來執行您的封裝，請參閱將[SSIS 套件當做 adf 活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)來執行一文以取得詳細資訊。  您可以透過 SSMS 設定它，以使用在 ADF 中產生管線的 Azure Active Directory （AAD）應用程式。  您也可以將它設定為存取檔案系統/檔案共用/Azure 檔案儲存體儲存封裝的位置。  根據您為其叫用選項所提供的值， **AzureDTExec**會在其中產生並執行具有 Execute SSIS 套件活動的唯一 ADF 管線。  以相同的選項值叫用**AzureDTExec** ，將會重新執行現有的管線。

## <a name="prerequisites"></a>必要條件
若要使用**AzureDTExec**，請從[這裡](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下載並安裝最新的 SSMS （18.3 版或更新版本）。

## <a name="configure-azuredtexec-utility"></a>設定 AzureDTExec 公用程式
在您的本機電腦上安裝 SSMS 也會安裝**AzureDTExec**。  若要設定其設定，請使用 [以**系統管理員身分執行**] 選項啟動 SSMS，然後選取 [串聯] 下拉式功能表專案**工具-> 遷移至 Azure-> 設定啟用 azure 的 DTExec**。

![設定啟用 Azure 的 dtexec 功能表](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

此動作會彈出**AzureDTExecConfig**視窗，必須以系統管理許可權開啟，才能寫入**AzureDTExec 配置**檔。  如果您未以系統管理員身分執行 SSMS，將會顯示 [使用者帳戶控制（UAC）] 視窗，讓您輸入管理員密碼，以提高許可權。

![設定啟用 Azure 的 dtexec 設定](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在 [ **AzureDTExecConfig** ] 視窗中，您可以輸入您的設定，如下所示：

- **ApplicationId**：輸入您所建立之 AAD app 的唯一識別碼，該應用程式具有在 ADF 中產生管線的適當許可權。如需詳細資訊，請參閱透過 Azure 入口網站一文[建立 AAD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

- **AuthenticationKey**：輸入 AAD 應用程式的驗證金鑰。

- **TenantId**：輸入 AAD 租使用者的唯一識別碼，以在其下建立 AAD 應用程式。

- **SubscriptionId**：輸入您的 ADF 建立所在的 Azure 訂用帳戶唯一識別碼。

- **資源群組**：輸入您的 ADF 建立所在的 Azure 資源組名。

- **DataFactory**：輸入 ADF 的名稱，其中會根據叫用**AzureDTExec**時所提供的選項值，產生其中具有 Execute SSIS 套件活動的唯一管線。

- **IRName**：在您的 ADF 中輸入 Azure SSIS IR 的名稱，當叫用**AzureDTExec**時，其通用命名慣例（UNC）路徑中所指定的套件將會執行。

- **PackageAccessDomain**：輸入網域認證，以在叫用**AzureDTExec**時指定的 UNC 路徑中存取您的封裝。

- **PackageAccessUserName**：輸入使用者名稱認證，以在叫用**AzureDTExec**時指定的 UNC 路徑中存取您的封裝。

- **PackageAccessPassword**：輸入密碼認證，以在叫用**AzureDTExec**時所指定的 UNC 路徑中存取您的封裝。

- **LogPath**：輸入記錄檔資料夾的 UNC 路徑，將會在其中寫入 Azure SSIS IR 上的封裝執行記錄檔。

- **LogLevel**：針對 Azure SSIS IR 上的套件執行，輸入從預先定義的**null**/**Basic**/**Verbose**/**效能**選項中選取的記錄範圍。

- **LogAccessDomain**：當寫入記錄檔時，請輸入網域認證以存取其 UNC 路徑中的記錄檔資料夾，但在指定**LogPath**且**LogLevel**不是**null**時則需要。

- **LogAccessUserName**：輸入使用者名稱認證，以在寫入記錄檔時，存取其 UNC 路徑中的記錄檔資料夾，但在指定**LogPath**且**LogLevel**不是**null**時則需要。

- **LogAccessPassword**：在寫入記錄檔時，請輸入密碼認證以存取其 UNC 路徑中的記錄檔資料夾，但在指定**LogPath**且**LogLevel**不是**null**時則需要。

- **PipelineNameHashStrLen**：輸入當您叫用**AzureDTExec**時，要從您提供的選項值產生的雜湊字串長度。  這些字串將用於針對在 Azure SSIS IR 上執行套件的 ADF 管線，形成唯一的名稱。  通常長度為32個字元就已足夠。

如果您打算將封裝和記錄檔儲存在內部部署的檔案系統/檔案共用中，您應該將您的 Azure SSIS IR 加入連線到內部部署網路的 VNet，讓它可以提取您的套件並寫入您的記錄檔，請參閱將[AZURE SSIS Ir 加入至 vnet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)文章以取得詳細資訊。

若要避免以純文字顯示寫入**AzureDTExec**檔中的機密值，我們會將它們編碼成 Base64 編碼的字串。  當您叫用**AzureDTExec**時，所有 Base64 編碼的字串都會解碼回其原始值。  您可以藉由限制可存取的帳戶，進一步保護**AzureDTExec 的配置**檔案。

## <a name="invoke-azuredtexec-utility"></a>叫用 AzureDTExec 公用程式
您可以在命令列提示字元中叫用**AzureDTExec** ，並針對使用案例中的特定選項提供相關的值。

公用程式會安裝在 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`。 您可以將其路徑新增至「路徑」環境變數，以便從任何地方叫用它。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

叫用**AzureDTExec**會提供類似的選項，以叫用**dtexec**，請參閱[dtexec 公用程式](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)檔以取得詳細資訊。  以下是目前支援的選項：

- **/F [ile]** ：載入儲存在檔案系統/檔案共用/Azure 檔案儲存體中的封裝。  作為此選項的值，您可以在檔案系統/檔案共用/Azure 檔案儲存體中，以 .dtsx 副檔名指定封裝檔案的 UNC 路徑。  如果指定的 UNC 路徑包含任何空格，您就必須在整個路徑前後加上引號。

- **/Conf [igFile]** ：指定要擷取值的來源組態檔。  使用此選項，您可以針對封裝設定與設計階段所指定不同的執行時間設定。  您可以將不同的設定儲存在 XML 設定檔中，然後在封裝執行之前載入它們。  如需詳細資訊，請參閱[SSIS 封裝](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)設定一文。  作為此選項的值，您可以在檔案系統/檔案共用/Azure 檔案儲存體中，以 Ssistutorial.dtsconfig 副檔名指定設定檔的 UNC 路徑。  如果指定的 UNC 路徑包含任何空格，您就必須在整個路徑前後加上引號。

- **/Conn [ection]** ：指定封裝中現有連接管理員的連接字串。  使用此選項，您可以針對封裝中現有的連線管理員，設定與設計階段所指定不同的執行時間連接字串。  作為此選項的值，您可以指定它，如下所示： `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`。

- **/Set**：覆寫您封裝中的參數、變數、屬性、容器、記錄提供者、Foreach 列舉值或連接的設定。  可以多次指定這個選項。  做為此選項的值，您可以指定它，如下所示： `property_path;value`，例如 `\package.variables[counter].Value;1` 會將 `counter` 變數的值覆寫為1。  您可以使用 [套件設定] Wizard 來尋找、複製及貼上您想要覆寫其值之套件中的專案 `property_path` 的值，請參閱[套件設定向導](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)檔以取得詳細資訊。

- **/De [crypt]** ：設定以**EncryptAllWithPassword**/**EncryptSensitiveWithPassword**保護等級設定之套件的解密密碼。

> [!NOTE]
> 以新值叫用**AzureDTExec**的選項將會產生新的管線，但 option **/De [腳本]** 除外。

## <a name="next-steps"></a>後續步驟

一旦在呼叫**AzureDTExec**之後，就會產生並執行具有 Execute SSIS 套件活動的唯一管線，它們可以在 ADF 入口網站上受到監視。 如需詳細資訊，請參閱以[ADF 活動執行 SSIS 套件](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)一文。

> [!WARNING]
> 產生的管線預期只能由**AzureDTExec**使用。 在未來，其屬性/參數可能會變更，因此您不應該針對任何其他目的修改/重複使用它們，這可能會中斷**AzureDTExec**。 萬一發生這種情況，您可以隨時刪除管線， **AzureDTExec**會在下一次叫用時產生新的管線。
