---
title: 如何使用適用於 Azure Mobile Apps 的 JavaScript SDK
description: 如何使用適用於 Azure Mobile Apps 的 v
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 410571320e5ffae9cf94c5035079e5b202190863
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027361"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>如何使用適用於 Azure Mobile Apps 的 JavaScript 用戶端程式庫
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊 App Center [App center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>總覽
本指南將教導您使用最新的 [適用於 Azure Mobile Apps 的 JavaScript SDK]執行一般案例。 如果您不熟悉 Azure Mobile Apps，請先完成 [Azure 行動應用程式快速入門] 建立後端，並建立資料表。 在本指南中，我們會著重在使用 HTML/JavaScript Web 應用程式中的行動後端。

## <a name="supported-platforms"></a>支援的平台
瀏覽器的支援限於在下列主流瀏覽器的目前版本和最新版本：Google Chrome、Microsoft Edge、Microsoft Internet Explorer、Mozilla Firefox。  我們預期 SDK 可與任何相對近期的瀏覽器搭配運作。

套件會以通用 JavaScript 模組的形式來散發，因此能夠支援全域、AMD 和 CommonJS 格式。

## <a name="Setup"></a>設定和必要條件
本指南假設您已建立包含資料表的後端。 本指南假設資料表的結構描述與這些教學課程中的資料表相同。

安裝 Azure Mobile Apps JavaScript SDK 可透過 `npm` 命令完成︰

```
npm install azure-mobile-apps-client --save
```

程式庫也可在 CommonJS 環境 (例如 Browserify 和 Webpack) 內部做為 ES2015 模組和 AMD 程式庫使用。  例如:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

您也可以直接從我們 CDN 下載預先建置 SDK 版本來使用︰

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>操作說明：驗證使用者
Azure App Service 支援使用各種外部識別提供者來驗證與授權應用程式使用者：Facebook、Google、Microsoft 帳戶及 Twitter。 您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。 您也可以在伺服器指令碼中，使用驗證的使用者的身分識別來實作授權規則。 如需詳細資訊，請參閱 [開始使用驗證] 教學課程。

支援兩個驗證流程：伺服器流程和用戶端流程。  由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。 用戶端流程依賴提供者專屬的 SDK，可以與裝置特有的功能深入整合，例如單一登入。

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>操作說明：設定行動 App Service 以使用外部重新導向 URL。
有數種類型的 JavaScript 應用程式會使用回送功能來處理 OAuth UI 流程。  這些功能包括：

* 在本機執行服務
* 搭配 Ionic 架構使用即時重新載入
* 重新導向至 App Service 以進行驗證。

在本機執行會造成問題，因為根據預設，App Service 驗證只設定為允許從您的行動裝置應用程式後端來存取。 請使用下列步驟來變更 App Service 設定，以便在本機執行伺服器時啟用驗證：

1. 登入 [Azure 入口網站]
2. 瀏覽至行動裝置應用程式後端。
3. 選取 [開發工具] 功能表中的 [資源總管]。
4. 按一下 [執行] ，在新的索引標籤或視窗中開啟行動裝置應用程式後端的資源總管。
5. 展開應用程式的 [config]  >  [authsettings] 節點。
6. 按一下 [編輯] 按鈕來啟用資源的編輯。
7. 尋找 **allowedExternalRedirectUrls** 元素，此元素應該是 null。 在陣列中新增 URL：

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    使用您服務的 URL 取代陣列中的 URL，在此範例中為本機 Node.js 範例服務的 `http://localhost:3000` 。 根據您應用程式的設定方式而定，您也可以使用 Ripple 服務的 `http://localhost:4400` 或一些其他的 URL。
8. 在頁面頂端，按一下 [讀取/寫入]，然後按一下 [PUT] 以儲存您的更新。

您還需要將相同的回送 URL 加入至 CORS 白名單設定：

1. 瀏覽回 [Azure 入口網站]。
2. 瀏覽至行動裝置應用程式後端。
3. 按一下 [API] 功能表中的 [CORS]。
4. 在空的 [允許的原點] 文字方塊中輸入每一個 URL。  隨即會建立新的文字方塊。
5. 按一下 [儲存]

後端更新之後，您就可以在應用程式中使用新的回送 URL。

<!-- URLs. -->
[Azure 行動應用程式快速入門]: app-service-mobile-cordova-get-started.md
[開始使用驗證]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure 入口網站]: https://portal.azure.com/
[適用於 Azure Mobile Apps 的 JavaScript SDK]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
