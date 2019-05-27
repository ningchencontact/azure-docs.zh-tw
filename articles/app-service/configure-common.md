---
title: 在入口網站-Azure App Service 中設定應用程式
description: 了解如何在 Azure 入口網站中設定 App Service 應用程式的一般設定。
keywords: azure 應用程式服務、 web 應用程式、 應用程式設定、 環境變數
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957906"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>在 Azure 入口網站中設定 App Service 應用程式

本主題說明如何設定 web 應用程式、 行動後端或 API 應用程式使用的一般設定[Azure 入口網站]。

## <a name="configure-app-settings"></a>進行應用程式設定

在 App Service 中，您可以使用環境變數等的應用程式設定。 在  [Azure 入口網站]，瀏覽至您的應用程式管理頁面。 在 應用程式的左側功能表中，按一下**組態** > **應用程式設定**。

![應用程式設定](./media/configure-common/open-ui.png)

適用於 ASP.NET 和 ASP.NET Core 開發人員在 App Service 中的設定應用程式設定就像是在中設定它們`<appSettings>`中*Web.config*，但是在 App Service 中的值來覆寫中的*Web.config*。您可以保留開發設定 （例如本機 MySQL 密碼）。 *Web.config*，但生產環境祕密 （例如 Azure MySQL 資料庫密碼） 在 App Service 中的安全。 當您在本機偵錯，並使用生產環境祕密部署至 Azure 時，相同的程式碼會使用您的開發設定。

同樣地，其他語言堆疊中，會在執行階段取得應用程式設定為環境變數。 如需語言堆疊的特定步驟，請參閱：

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [自訂容器](containers/configure-custom-container.md#configure-environment-variables)

應用程式設定在儲存時一律加密 (待用加密)。

> [!NOTE]
> 應用程式設定也可以從已解決[Key Vault](/azure/key-vault/)使用[Key Vault 參考](app-service-key-vault-references.md)。

### <a name="show-hidden-values"></a>顯示隱藏的值

根據預設，應用程式設定的值會隱藏在入口網站中的安全性。 若要查看隱藏的應用程式設定值，請按一下**值**該設定的欄位。 若要查看所有的應用程式設定的值，請按一下**顯示值** 按鈕。

### <a name="add-or-edit"></a>新增或編輯

若要新增新的應用程式設定，請按一下**新的應用程式設定**。 在對話方塊中，您可以[堅持目前的位置設定](deploy-staging-slots.md#which-settings-are-swapped)。

若要編輯的設定，請按一下**編輯**右邊的按鈕。

完成後，按一下**更新**。 別忘了按一下**儲存**回到**組態**頁面。

> [!NOTE]
> 在預設 Linux 容器或自訂的 Linux 容器，例如在應用程式設定名稱的任何巢狀的 JSON 索引鍵結構`ApplicationInsights:InstrumentationKey`必須為 App Service 中設定`ApplicationInsights__InstrumentationKey`金鑰名稱。 換句話說，任何`:`應取代為`__`（雙底線）。
>

### <a name="edit-in-bulk"></a>大量編輯

若要新增或編輯大量的應用程式設定，按一下**進階編輯** 按鈕。 完成後，按一下**更新**。 別忘了按一下**儲存**回到**組態**頁面。

應用程式設定必須將下列 JSON 格式設定：

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>設定連接字串

在  [Azure 入口網站]，瀏覽至應用程式的管理頁面。 在 應用程式的左側功能表中，按一下**組態** > **應用程式設定**。

![應用程式設定](./media/configure-common/open-ui.png)

適用於 ASP.NET 和 ASP.NET Core 開發人員，App Service 中的設定連接字串，就像是在中設定它們`<connectionStrings>`中*Web.config*，但您在 App Service 中設定的值覆寫中的*Web.config*.您可以保留開發設定 （例如資料庫檔案） *Web.config*和生產環境祕密 （例如 SQL Database 認證） 在 App Service 中的安全。 當您在本機偵錯，並使用生產環境祕密部署至 Azure 時，相同的程式碼會使用您的開發設定。

針對其他語言堆疊，最好是使用[應用程式設定](#configure-app-settings)相反的因為連接字串需要特殊的格式，在變數中的索引鍵順序來存取值。 以下是一個例外狀況，不過： 特定的 Azure 資料庫備份類型和應用程式如果應用程式中設定其連接字串。 如需詳細資訊，請參閱 <<c0> [ 備份的項目](manage-backup.md#what-gets-backed-up)。 如果您不需要這個自動的備份，然後使用應用程式設定。

在執行階段，連接字串可做為環境變數，加上下列連線類型：

* SQL Server： `SQLCONNSTR_`
* MySQL： `MYSQLCONNSTR_`
* SQL Database： `SQLAZURECONNSTR_`
* 自訂：`CUSTOMCONNSTR_`

例如，名為 MySql 連接字串*connectionstring1*可以存取的環境變數為`MYSQLCONNSTR_connectionString1`。 如需語言堆疊的特定步驟，請參閱：

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [自訂容器](containers/configure-custom-container.md#configure-environment-variables)

連接字串在儲存時一律加密 (待用加密)。

> [!NOTE]
> 連接字串也可以從已解決[Key Vault](/azure/key-vault/)使用[Key Vault 參考](app-service-key-vault-references.md)。

### <a name="show-hidden-values"></a>顯示隱藏的值

根據預設，連接字串的值會隱藏在入口網站中的安全性。 若要檢視隱藏的連接字串的值，只要按一下**值**該字串的欄位。 若要查看所有的連接字串的值，請按一下**顯示值** 按鈕。

### <a name="add-or-edit"></a>新增或編輯

若要新增新的連接字串，請按一下**新的連接字串**。 在對話方塊中，您可以[堅持目前位置的連接字串](deploy-staging-slots.md#which-settings-are-swapped)。

若要編輯的設定，請按一下**編輯**右邊的按鈕。

完成後，按一下**更新**。 別忘了按一下**儲存**回到**組態**頁面。

### <a name="edit-in-bulk"></a>大量編輯

若要新增或編輯大量的連接字串，請按一下**進階編輯** 按鈕。 完成後，按一下**更新**。 別忘了按一下**儲存**回到**組態**頁面。

連接字串具有下列的 JSON 格式：

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>設定一般設定

在  [Azure 入口網站]，瀏覽至應用程式的管理頁面。 在 應用程式的左側功能表中，按一下**組態** > **應用程式設定**。

![一般設定](./media/configure-common/open-general.png)

在這裡，您可以設定應用程式的一些常見設定。 某些設定需要您[相應增加以較高的定價層](web-sites-scale.md)。

- **堆疊設定**:要執行的應用程式，包括語言及 SDK 版本的軟體堆疊。 如需 Linux 應用程式和自訂的容器應用程式，您也可以設定的選擇性啟動命令或檔案。
- **平台設定**:可讓您設定裝載的平台，包括：
    - **位元**:32 位元或 64 位元。
    - **WebSocket 通訊協定**:針對[ASP.NET SignalR]或是[socket.io](https://socket.io/)，例如。
    - **Always On**:讓應用程式即使在沒有流量時，才載入。 您需要啟用它，讓連續 WebJobs，或使用 CRON 運算式觸發的 webjobs。
    - **受控的管線版本**:IIS[管線模式]。 將它設定為**傳統**如果您有舊版的應用程式需要較舊版本的 IIS。
    - **HTTP 版本**:設定為 **2.0** 來啟用 [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 通訊協定支援。
    > [!NOTE]
    > 大部分的新式瀏覽器僅支援透過 TLS 的 HTTP/2 通訊協定，非加密的流量則會繼續使用 HTTP/1.1。 若要確保該用戶端瀏覽器連線到您的應用程式，使用 HTTP/2，或是[購買 App Service 憑證](web-sites-purchase-ssl-web-site.md)應用程式的自訂網域或[協力廠商憑證繫結](app-service-web-tutorial-custom-ssl.md)。
    - **ARR 同質性**:在多重執行個體部署中，請確定用戶端會路由至相同的執行個體的工作階段存留期。 您可以將此選項設定為**關閉**無狀態應用程式。
- **偵錯**:啟用遠端偵錯[ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)， [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)，或[Node.js](containers/configure-language-nodejs.md#debug-remotely)應用程式。 48 小時後自動關閉此選項。
- **內送的用戶端憑證**： 要求中的用戶端憑證[相互驗證](app-service-web-configure-tls-mutual-auth.md)。

## <a name="configure-default-documents"></a>設定預設文件

此設定為只針對 Windows 應用程式。

在  [Azure 入口網站]，瀏覽至應用程式的管理頁面。 在 應用程式的左側功能表中，按一下**組態** > **預設文件**。

![一般設定](./media/configure-common/open-documents.png)

預設文件會顯示在網站的根 URL 的網頁。 系統會使用清單中第一個相符的檔案。 若要新增新的預設文件，請按一下**新的文件**。 別忘了按一下**儲存**。

如果應用程式會使用路由基礎 URL，而非處理靜態內容的模組，則不需要預設文件。

## <a name="configure-path-mappings"></a>設定路徑對應

在  [Azure 入口網站]，瀏覽至應用程式的管理頁面。 在 應用程式的左側功能表中，按一下**組態** > **路徑對應**。

![一般設定](./media/configure-common/open-path.png)

**路徑對應**頁面會顯示不同的項目根據 OS 類型。

### <a name="windows-apps-uncontainerized"></a>Windows 應用程式 (uncontainerized)

針對 Windows 應用程式，您可以自訂的 IIS 處理常式對應和虛擬應用程式和目錄。

處理常式對應可讓您加入自訂指令碼處理器以處理特定副檔名的要求。 若要新增的自訂處理常式，請按一下**新的處理常式**。 設定處理常式如下所示：

- **副檔名**。 您想要處理這類副檔名 *\*.php*或是*handler.fcgi*。
- **指令碼處理器**。 您的指令碼處理器的絕對路徑。 指令碼處理器會處理符合該副檔名的檔案要求。 使用路徑 `D:\home\site\wwwroot` 以指出應用程式的根目錄。
- **引數**。 指令碼處理器選用命令列引數。

每個應用程式具有預設的根路徑 (`/`) 對應至`D:\home\site\wwwroot`，其中預設會部署您的程式碼。 如果您的應用程式的根目錄位於不同的資料夾，或如果您的儲存機制有多個應用程式，您可以編輯或新增虛擬應用程式和這裡的目錄。 按一下 **新的虛擬應用程式或目錄**。

若要設定虛擬應用程式和目錄，請指定每個虛擬目錄和其對應的實體路徑，相對於網站根目錄 (`D:\home`)。 或者，您可以選取 [ **應用程式** ] 核取方塊，勾選虛擬目錄做為應用程式。

### <a name="containerized-apps"></a>容器化應用程式

您可以[新增您的容器化應用程式的自訂儲存體](containers/how-to-serve-content-from-azure-storage.md)。 容器化應用程式包括所有的 Linux 應用程式以及 App Service 上執行 Windows 和 Linux 的自訂容器。 按一下 **新 Azure 儲存體掛接**並設定您自訂的儲存體，如下所示：

- **名稱**：顯示名稱。
- **組態選項**:**基本**或是**進階**。
- **儲存體帳戶**：容器具有您想要儲存體帳戶。
- **儲存體類型**：**Azure Blob**或是**Azure 檔案**。
  > [!NOTE]
  > Windows 容器應用程式只會支援 Azure 檔案服務。
- **儲存體容器**︰基本組態時，您想要的容器。
- **共用名稱**:進階組態，如檔案共用名稱。
- **存取金鑰**:進行進階設定時，存取金鑰。
- **掛接路徑**:若要掛接的自訂儲存體容器中的絕對路徑。

如需詳細資訊，請參閱 <<c0> [ 從 Linux 上的 App Service 中的 Azure 儲存體提供內容](containers/how-to-serve-content-from-azure-storage.md)。

## <a name="configure-language-stack-settings"></a>設定語言堆疊設定

針對 Linux 應用程式，請參閱：

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>設定自訂容器

請參閱[適用於 Azure App Service 中設定自訂的 Linux 容器](containers/configure-custom-container.md)

## <a name="next-steps"></a>後續步驟

- [在 Azure App Service 中設定自訂網域名稱]
- [設定 Azure App Service 中的預備環境]
- [針對 Azure App Service 中的 App 啟用 HTTPS]
- [啟用診斷記錄檔](troubleshoot-diagnostic-logs.md)
- [在 Azure App Service 中調整應用程式規模]
- [在 Azure App Service 中監視基本概念]
- [變更與 applicationHost.xdt applicationHost.config 設定](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure 入口網站]: https://portal.azure.com/
[在 Azure App Service 中設定自訂網域名稱]: ./app-service-web-tutorial-custom-domain.md
[設定 Azure App Service 中的預備環境]: ./deploy-staging-slots.md
[針對 Azure App Service 中的 App 啟用 HTTPS]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[在 Azure App Service 中監視基本概念]: ./web-sites-monitor.md
[管線模式]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[在 Azure App Service 中調整應用程式規模]: ./web-sites-scale.md
