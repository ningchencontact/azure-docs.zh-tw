---
title: 教學課程 - 授與從單頁應用程式存取 ASP.NET Core Web API 的權限 - Azure Active Directory B2C | Microsoft Docs
description: 關於如何使用 Active Directory B2C 來保護 .NET Core Web API，以及如何從單頁應用程式加以呼叫的教學課程。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 609e7bc4104a445c5d77109c9470fb487b2f1336
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507737"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 授與從單頁應用程式存取 ASP.NET Core Web API 的權限

本教學課程將說明如何從單頁應用程式呼叫 Azure Active Directory (Azure AD) B2C 所保護的 ASP.NET Core Web API 資源。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

完成以下教學課程中的步驟和必要條件：[教學課程：讓單頁應用程式能夠使用 Azure Active Directory B2C 向帳戶進行驗證](active-directory-b2c-tutorials-spa.md)。

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
4. 選取 [應用程式]  ，然後選取 [新增]  。
5. 輸入應用程式的名稱。 例如，*webapi1*。
6. 針對 [包含 Web 應用程式/Web API]  和 [允許隱含流程]  ，選取 [是]  。
7. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:5000` 接聽。
8. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api`。
9. 按一下頁面底部的 [新增]  。
10. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，有些使用者可能同時具有讀取和寫入權限，而有些則可能只有唯讀權限。 在本教學課程中，您會定義 Web API 的讀取權限。

1. 選取 [應用程式]  ，然後選取 [webapi1]  。
2. 選取 [發佈的範圍]  。
3. 針對 [範圍]  ，輸入 `Hello.Read`，以及輸入 `Read access to hello` 作為描述。
4. 針對 [範圍]  ，輸入 `Hello.Write`，以及輸入 `Write access to hello` 作為描述。
5. 按一下 [檔案]  。

發佈的範圍可以用來為用戶端應用程式授與對 Web API 的權限。

## <a name="grant-permissions"></a>授與權限

若要從應用程式呼叫受保護的 Web API，您必須為應用程式授與對 API 的權限。 在必要的教學課程中，您已在 Azure AD B2C 中建立名為 *webapp1* 的 Web 應用程式。 您可使用此應用程式來呼叫 Web API。

1. 選取 [應用程式]  ，然後選取 [webapp1]  。
2. 選取 [API 存取]  ，然後選取 [新增]  。
3. 在 [選取 API]  下拉式清單中，選取 [webapi1]  。
4. 在 [選取範圍]  下拉式清單中，選取您先前定義的 **Hello.Read** 和 **Hello.Write** 範圍。
5. 按一下 [確定]  。

您的 [我的範例單頁應用程式]  會進行註冊以呼叫受保護的 [Hello Core API]  。 使用者會向 Azure AD B2C 進行驗證，以使用該單頁應用程式。 該單頁應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。

## <a name="configure-the-sample"></a>設定範例

現在已註冊 Web API 且已定義範圍，您可設定 Web API 程式碼以使用您的 Azure AD B2C 租用戶。 在本教學課程中，您會設定可從 GitHub 下載的範例 .NET Core Web 應用程式。 [下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)，或從 GitHub 複製範例 Web 應用程式。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>設定 Web API

1. 在 Visual Studio 中開啟 **B2C-WebAPI.sln** 解決方案。
2. 開啟 **appsettings.json** 檔案。 更新下列值，以將 Web API 設定為使用您的租用戶：

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>啟用 CORS

若要允許您的單頁應用程式呼叫 ASP.NET Core Web API，您必須啟用 [CORS](https://docs.microsoft.com/aspnet/core/security/cors)。

1. 在 **Startup.cs** 中，將 CORS 新增至 `ConfigureServices()` 方法。

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. 在 **Startup.cs** 中，設定 `Configure()` 方法中的 CORS。

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. 開啟 [屬性]  下的 [launchSettings.json]  檔案，找出 **iisSettings** *applicationURL* 設定，然後將連接埠號碼設定為已為 API 回覆 URL `http://localhost:5000` 註冊的連接埠號碼。

### <a name="configure-the-single-page-application"></a>設定單頁應用程式

單頁應用程式會使用 Azure AD B2C 進行使用者註冊、登入及呼叫受保護的 ASP.NET Core Web API。 您可更新單頁應用程式以呼叫 .NET Core Web API。

若要變更應用程式設定：

1. 開啟 `index.html` 檔案。
2. 使用 Azure AD B2C 租用戶註冊資訊設定此範例。 在下列程式碼中，將您的租用戶名稱新增至 **b2cScopes**，並將 **webApi** 值變更為您之前記錄的 *applicationURL* 值：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>執行 SPA 應用程式和 Web API

您需要執行 Node.js 單頁應用程式和 .NET Core Web API。

### <a name="run-the-aspnet-core-web-api"></a>執行 ASP.NET Core Web API 

按 **F5** 以在 Visual Studio 中進行 **B2C-WebAPI.sln** 解決方案偵錯。

當專案啟動時，您的預設瀏覽器中會顯示一個網頁，宣告此 Web API 適用於要求。

### <a name="run-the-single-page-app"></a>執行單頁應用程式

1. 啟動 Node.js 命令提示字元。
2. 切換至包含 Node.js 範例的目錄。 例如 `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 執行下列命令：
    ```
    npm install && npm update
    node server.js
    ```

    主控台視窗會顯示裝載應用程式的連接埠號碼。
    
    ```
    Listening on port 6420...
    ```

4. 使用瀏覽器巡覽至位址 `http://localhost:6420` 以檢視應用程式。
5. 使用[在單頁應用程式 (JavaScript) 中使用 Azure Active Directory B2C 進行使用者驗證](active-directory-b2c-tutorials-spa.md)中使用的電子郵件地址和密碼進行登入。
6. 按一下 [呼叫 API]  。

在您註冊使用者帳戶或使用使用者帳戶登入之後，此範例會呼叫受保護的 Web API 並傳回結果。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

> [!div class="nextstepaction"]
> [教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式](tutorial-add-identity-providers.md)
