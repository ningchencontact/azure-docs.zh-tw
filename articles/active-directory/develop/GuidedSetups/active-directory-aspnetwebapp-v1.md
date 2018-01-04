---
title: "開始使用 azure AD v1 ASP.NET Web 伺服器 |Microsoft 文件"
description: "使用 OpenID Connect 標準，搭配傳統網頁瀏覽器型應用程式，在 ASP.NET 方案上實作 Microsoft 登入"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>將「使用 Microsoft 登入」新增至 ASP.NET Web 應用程式

本指南示範如何使用 ASP.NET MVC 方案實作「使用 Microsoft 登入」，解決方案中包含使用 OpenID Connect 的傳統網頁瀏覽器型應用程式。 

在本指南最後，將會接受您的應用程式的登入的公司及學校帳戶，來自已整合與 Azure Active Directory 的組織。

> [!NOTE]
> 此 「 引導式的安裝程式可協助您讓從 ASP.NET 應用程式中的公司及學校帳戶登入。 如果您有興趣讓個人的帳戶，除了公司及學校帳戶登入，您可以使用[v2 端點](../active-directory-v2-compare.md)。 請參閱[此 ASP.NET 指引安裝 v2 端點](./active-directory-aspnetwebapp.md)以及[這份文件](../active-directory-v2-limitations.md)解釋 v2 端點目前的限制。
<br/><br/>

<!--separator-->

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  沒有嗎？  [免費下載 Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

本指南是以透過瀏覽器存取 ASP.NET 網站，並要求使用者透過登入按鈕進行驗證的案例為基礎。 在這個案例中，大部分轉譯網頁的工作會在伺服器端執行。

> [!NOTE]
> 此 「 引導式的安裝程式會示範如何登入上的空白範本，請從開始將 ASP.NET Web 應用程式的使用者，並加入步驟，例如按鈕和每個控制站和方法，加入符號，同時也說明一些概念。 或者，您也可以建立登入 Azure Active Directory 的專案使用的使用者 （公司及學校帳戶） [Visual Studio web 範本](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options)，然後選取*組織帳戶*然後其中一個的雲端選項-此選項會更豐富的範本，請使用具有額外的控制站、 方法和檢視。

## <a name="libraries"></a>程式庫

本指南會使用下列套件：

|程式庫|說明|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|可讓應用程式使用 OpenIdConnect 進行驗證的中介軟體|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|可讓應用程式使用 Cookie 維持使用者工作階段的中介軟體|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|可讓 OWIN 型應用程式使用 ASP.NET 要求管線在 IIS 上執行|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>設定專案

本節說明使用 OpenID Connect 在 ASP.NET 專案中，透過 OWIN 中介軟體安裝及設定驗證管線的步驟。 

> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip)並跳至[設定步驟](#configure-your-webconfig-and-register-an-application)，以在執行之前先設定程式碼範例。

## <a name="create-your-aspnet-project"></a>建立 ASP.NET 專案
1. 在 Visual Studio 中：`File` > `New` > `Project`<br/>
2. 在 *Visual C#\Web* 底下，選取 `ASP.NET Web Application (.NET Framework)`。
3. 為您的應用程式命名並按一下 [確定]
4. 選取`Empty`，然後選取核取方塊，以新增`MVC`參考

## <a name="add-authentication-components"></a>新增驗證元件

1. 在 Visual Studio 中：`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 在 [套件管理器主控台] 視窗中輸入下列內容以新增「OWIN 中介軟體 NuGet 套件」：

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>關於這些封裝
>上面的程式庫可透過 Cookie 型驗證，使用 OpenID Connect 啟用單一登入 (SSO)。 完成驗證並將代表使用者的權杖傳送至您的應用程式之後，OWIN 中介軟體就會建立工作階段 Cookie。 使用者不需要重新驗證，因此沒有額外的驗證需要瀏覽器然後在後續要求中使用此 cookie。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>設定驗證管線
下列步驟可用來建立 OWIN 中介軟體*啟動類別*來設定 OpenID Connect 驗證。 這個類別時會自動執行。

> [!TIP]
> 如果您專案的根資料夾中沒有 `Startup.cs` 檔案：<br/>
> 1. 以滑鼠右鍵按一下專案的根資料夾： >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. 將它命名為 `Startup.cs`<br/>
>
>> 確定選取的類別是 OWIN 啟動類別，而非標準 C# 類別。 如果命名空間上方顯示 `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`，請勾選以確認此項目。


1. 新增*OWIN*和*Microsoft.IdentityModel*的命名空間`Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. 啟動類別取代為下列程式碼：

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> 您在 *OpenIDConnectAuthenticationOptions* 中提供的參數會作為供應用程式與 Azure AD 進行通訊的座標。 由於 OpenID Connect 的中介軟體會使用 cookie，您也需要設定的 cookie 驗證上述程式碼所示。 *ValidateIssuer* 值會告知 OpenIdConnect 不要針對某一特定組織限制存取。
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>新增控制器以處理登入和登出要求

這個步驟說明如何建立新的控制器來公開登入和登出方法。

1.  以滑鼠右鍵按一下`Controllers`資料夾，然後選取`Add` > `Controller`
2.  選取 `MVC (.NET version) Controller – Empty`。
3.  按一下 [新增]
4.  將它命名為 `HomeController`，然後按一下 [新增]
5.  新增*OWIN*類別的命名空間：

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. 加入下列方法來處理登入和登出才能將控制器起始程式碼透過將驗證挑戰：

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>建立應用程式的首頁來透過登入按鈕將使用者登入

在 Visual Studio 中建立新的檢視來新增登入按鈕，並在驗證之後顯示使用者資訊：

1.  以滑鼠右鍵按一下`Views\Home`資料夾，然後選取`Add View`
2.  將它命名為 `Index`
3.  在以下檔案中新增下列 HTML (包括登入按鈕)：

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> 此頁面會以 SVG 格式新增一個具有黑色背景的登入按鈕：<br/>![使用 Microsoft 登入](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> 如需登入按鈕，請移至[本頁](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "的商標指導")。
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>顯示使用者的宣告加入控制器
此控制器示範如何使用 `[Authorize]` 屬性來保護控制器。 此屬性會設定限制，只允許經過驗證的使用者存取控制器。 下列程式碼會顯示登入過程中所擷取的使用者宣告的屬性使用。

1.  以滑鼠右鍵按一下`Controllers`資料夾：`Add` > `Controller`
2.  選取 `MVC {version} Controller – Empty`。
3.  按一下 [新增]
4.  將它命名為 `ClaimsController`
5.  控制器類別的程式碼取代為下列程式碼-這會將`[Authorize]`屬性加入該類別：

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> 因為使用了 `[Authorize]` 屬性，所以此控制器的所有方法都只能在使用者已通過驗證的情況下才能執行。 如果使用者未經過驗證，並嘗試存取控制站，OWIN 初始驗證要求，並強制使用者進行驗證。 上述程式碼會查看使用者輸入使用者的權杖中包含的特定屬性的宣告集合。 這些屬性包括使用者的完整名稱和使用者名稱，以及全域使用者識別元主體。 它也包含「租用戶識別碼」，這代表使用者所屬組織的識別碼。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>建立檢視來顯示使用者的宣告

在 Visual Studio 中，建立新的檢視以在網頁中顯示使用者的宣告：

1.  以滑鼠右鍵按一下`Views\Claims`資料夾和：`Add View`
2.  將它命名為 `Index`
3.  將下列 HTML 新增至檔案：

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>設定您*web.config*和註冊應用程式

1. 在 Visual Studio 中，將下列內容加入`web.config`（位於根資料夾） 的區段底下`configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. 在 [方案總管] 中選取專案，並查看<i>屬性</i>視窗 （如果您沒有看到 [屬性] 視窗中，按 F4）
3. 將 [SSL 已啟用] 變更為 <code>True</code>
4. 將專案的 SSL URL 複製到剪貼簿：<br/><br/>![專案屬性](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. 在<code>web.config</code>，取代<code>Enter_the_Redirect_URL_here</code>專案的 SSL url 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>在 Azure 入口網站中註冊您的應用程式，然後將其資訊加入*web.config*

1. 移至[Microsoft Azure 入口網站的應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)註冊應用程式
2. 選取 `New application registration`
3. 輸入您的應用程式的名稱
4. 貼上的 Visual Studio 專案*SSL URL*中`Sign-on URL`（此 URL 也會加入自動為您註冊應用程式回覆 Url 的清單）
5. 按一下`Create`註冊應用程式。 這個動作可讓您回到應用程式清單
6. 現在，搜尋和/或選取您剛開啟其內容的應用程式
7. 複製下的 guid`Application ID`到剪貼簿
8. 返回 Visual studio，並在`web.config`，取代`Enter_the_Application_Id_here`您剛登錄的應用程式從應用程式識別碼

> [!TIP]
> 如果您的帳戶設定存取多個目錄，請確定您已選取正確的目錄，組織您想要註冊您的帳戶名稱，在頂端按一下應用程式在 Azure 入口網站，以滑鼠右鍵，然後驗證選取為指定的目錄：<br/>![選取正確的目錄](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>設定登入選項

您可以設定您的應用程式，只允許使用者隸屬於一個組織的 Azure Active Directory 執行個體，登入，或接受來自屬於任何組織的使用者登入。 請依照下列選項的其中一個的指示進行：

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>設定以允許應用程式的登入的任何公司或組織 （多租用戶） 從公司及學校帳戶

遵循下列步驟，如果您想要接受的登入的任何公司或組織與 Azure Active Directory 整合的公司及學校帳戶的次數。 這是常見的案例*SaaS 應用程式*:

1. 請返回[Microsoft Azure 入口網站的應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)並找出您剛登錄的應用程式
2. 在下`All Settings`選取`Properties`
3. 變更`Multi-tenanted`屬性`Yes`按一下`Save`

如需有關此設定，且多租用戶應用程式概念的詳細資訊，請參閱[本文](../active-directory-devhowto-multi-tenant-overview.md "多租用戶概觀")。

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>限制使用者只能有一個組織的 Active Directory 執行個體，來登入您的應用程式 （單一租用戶）

這個選項是常見的案例*LOB 應用程式*： 如果您想要接受只會從屬於特定的 Azure Active Directory 執行個體的帳戶登入您的應用程式 (包括*來賓帳戶*該執行個體的)，取代`Tenant`中的參數*web.config*從`Common`與租用戶的組織名稱-例如*contoso.onmicrosoft.com*。在這之後，變更`ValidateIssuer`引數中的您[ *OWIN 啟動 「 類別*](#configure-the-authentication-pipeline)至`true`。

若只要允許來自某一特定組織清單的使用者登入，請將 `ValidateIssuer` 設定為 true 並使用 `ValidIssuers` 參數指定組織清單。

若要實作自訂的方法，以驗證使用的簽發者的另一個選項是*IssuerValidator*參數。 如需有關`TokenValidationParameters`，請參閱[MSDN 本文](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN 文章")。

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>測試您的程式碼

按 `F5` 以在 Visual Studio 中執行您的專案。 瀏覽器會開啟並引導您至*http://localhost: {port}*了解*使用 Microsoft 登入* 按鈕。 接著請按一下該按鈕來登入。

當您準備要測試，請使用工作帳戶 (Azure Active Directory) 登入。 

![使用 Microsoft 瀏覽器視窗中登入](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![使用 Microsoft 瀏覽器視窗中登入](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>預期的結果
登入之後，使用者會重新導向至首頁上，您的網站，也就是指定於應用程式的登錄資訊中 Microsoft 應用程式註冊入口網站的 HTTPS URL。 此頁面現在會顯示*Hello {User}*和登出，以及連結以查看使用者的宣告-也就是授權控制器連結之前建立。

### <a name="see-users-claims"></a>查看使用者的宣告
選取超連結來查看使用者的宣告。 這個動作會引導您至控制器和檢視，只可驗證的使用者。

#### <a name="expected-results"></a>預期的結果
 您應該會看到一個表格，其中包含已登入使用者的基本屬性：

| 屬性 | 值 | 說明|
|---|---|---|
| 名稱 | {使用者完整名稱} | 使用者的名字和姓氏
|使用者名稱 | <span>user@domain.com</span>| 用來識別已登入使用者的使用者名稱
| 主體| {主體}|用來跨網站唯一識別使用者登入的字串|
| 租用戶識別碼| {Guid}| 唯一代表使用者之 Azure Active Directory 組織的 *guid*。|

此外，您會看到資料表，包括驗證要求中包含的所有使用者宣告。 如需 ID 權杖和其說明中的所有宣告的清單，請參閱此[文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "中的 ID 語彙基元--宣告")。


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>對具有 *[Authorize]* 屬性 (選用) 的方法進行存取測試
在此步驟中，您可以測試存取為匿名使用者的宣告控制站：<br/>
選取連結以將使用者登出，並完成登出程序。<br/>
現在您的瀏覽器中輸入 http://localhost: {port} / 宣告來存取您受保護的控制器`[Authorize]`屬性

#### <a name="expected-results"></a>預期的結果
您應該會收到提示，要求您進行驗證以查看檢視。

## <a name="additional-information"></a>其他資訊

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>保護您的整個網站
若要保護您的整個網站，請將 `AuthorizeAttribute` 新增至 `Global.asax` `Application_Start` 方法中的 `GlobalFilters`：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->