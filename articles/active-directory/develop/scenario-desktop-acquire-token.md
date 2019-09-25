---
title: 呼叫 web Api 的桌面應用程式 (取得應用程式的權杖)-Microsoft 身分識別平臺
description: 瞭解如何建立桌面應用程式來呼叫 web Api (取得應用程式的權杖 |)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8d46e873d48de5f7e507566b5af6095b9c4e1c
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268376"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api 的桌面應用程式-取得權杖

建立好之後`IPublicClientApplication`, 您就可以使用它來取得權杖, 然後用它來呼叫 Web API。

## <a name="recommended-pattern"></a>建議模式

Web API 是由其`scopes`所定義。 無論您在應用程式中提供的體驗為何, 您會想要使用的模式如下:

- 有系統地嘗試從權杖快取取得權杖, 方法是呼叫`AcquireTokenSilent`
- 如果這個呼叫失敗, 請使用`AcquireToken`您想要使用的流程 (此處`AcquireTokenXX`以表示)

### <a name="in-msalnet"></a>在 MSAL.NET 中

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```
### <a name="in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
    
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
快速

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

以下是在桌面應用程式中取得權杖的各種方式的詳細資料

## <a name="acquiring-a-token-interactively"></a>以互動方式取得權杖

下列範例顯示使用 Microsoft Graph 以互動方式取得權杖的最少程式碼, 以讀取使用者的設定檔。

### <a name="in-msalnet"></a>在 MSAL.NET 中

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

快速

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

### <a name="mandatory-parameters"></a>必要參數

`AcquireTokenInteractive`只有一個強制參數``scopes``, 其中包含定義需要權杖之範圍的字串列舉。 如果權杖適用于 Microsoft Graph, 則在名為「許可權」的區段中, 您可以在每個 Microsoft Graph API 的 api 參考中找到所需的範圍。 例如, 若要[列出使用者的連絡人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), 則必須使用「使用者. 讀取」、「連絡人」等範圍。 另請參閱[Microsoft Graph 許可權參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上, 您也必須指定父活動 (使用`.WithParentActivityOrWindow`, 請參閱下方), 讓權杖在互動之後回到該父活動。 如果您未指定, 則會在呼叫`.ExecuteAsync()`時擲回例外狀況。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定選擇性參數

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

互動式, UI 很重要。 `AcquireTokenInteractive`具有一個特定的選擇性參數, 可讓您針對支援它的平臺 (父 UI) 指定。 在桌面應用程式中使用時`.WithParentActivityOrWindow` , 具有不同的類型 (視平臺而定):

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

備註：

- 在 .NET Standard 上, 預期`object`的`Activity`會是 Android `UIViewController`上的、 `IWin32Window` iOS 上的`NSWindow` 、MAC 上的, 以及`IntPr` Windows 上的或。
- 在 Windows 上, 您必須`AcquireTokenInteractive`從 UI 執行緒呼叫, 讓內嵌瀏覽器取得適當的 ui 同步處理內容。  不是從 UI 執行緒呼叫, 可能會導致訊息無法使用 UI 來提取正確和/或鎖死案例。 如果您不是在 ui 執行緒上, 從 ui 執行緒呼叫 MSAL 的其中一種方法, 就`Dispatcher`是在 WPF 上使用。
- 如果您使用的是 wpf, 若要從 wpf 控制項取得視窗, 您可以使用`WindowInteropHelper.Handle`類別。 然後, 從 WPF 控制項 (`this`) 呼叫:
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`是用來透過指定提示來控制使用者的互動性

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

類別會定義下列常數:

- ``SelectAccount``: 將強制 STS 顯示帳戶選取對話方塊, 其中包含使用者具有會話的帳戶。 當應用程式開發人員想要讓使用者在不同的身分識別之間進行選擇時, 這個選項非常有用。 此選項會驅動 MSAL 以``prompt=select_account``傳送給識別提供者。 此選項是預設值, 而且可以根據可用的資訊 (帳戶、使用者的會話是否存在等等) 提供最佳的體驗。 ...).除非您有充分的理由, 否則請不要變更它。
- ``Consent``: 可讓應用程式開發人員強制提示使用者同意, 即使之前已授與同意亦然。 在此情況下, MSAL `prompt=consent`會將傳送至身分識別提供者。 此選項可用於某些安全性焦點應用程式, 其中組織治理會要求使用者在每次使用應用程式時呈現同意對話方塊。
- ``ForceLogin``: 讓應用程式開發人員可以讓使用者透過服務提示認證, 即使不需要此使用者提示也一樣。 如果取得權杖失敗, 此選項會很有用, 讓使用者重新登入。 在此情況下, MSAL `prompt=login`會將傳送至身分識別提供者。 同樣地, 我們已看到它用於某些安全性焦點應用程式, 組織治理會要求使用者在每次存取應用程式的特定部分時 relogs。
- ``Never``(僅適用于 .NET 4.5 和 WinRT) 不會提示使用者, 而是會嘗試使用儲存在隱藏的內嵌 web 視圖中的 cookie (請參閱下文:MSAL.NET 中的 Web Views)。 使用這個選項可能會失敗, 而且在此`AcquireTokenInteractive`情況下, 會擲回例外狀況以通知需要 UI 互動, 而且您必須使用另一個`Prompt`參數。
- ``NoPrompt``:不會將任何提示傳送給識別提供者。 此選項僅適用于 Azure AD B2C 編輯設定檔原則 (請參閱[B2C 細節](https://aka.ms/msal-net-b2c-specificities))。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

這個修飾詞用於您想要讓使用者預先同意數個資源的先進案例（而不想要使用累加式同意，這通常與 MSAL.NET/Microsoft 身分識別平臺搭配使用）。 如需詳細資訊, 請參閱[如何: 將使用者同意預先用於數個資源](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)。

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是用來叫用瀏覽器的機制。 這種機制可以是專用的 UI WebBrowser 控制項或委派開啟瀏覽器的方式。
MSAL 為大部分的平臺提供 Web UI，但仍有一些情況下，您可能會想要自行裝載瀏覽器： 

- MSAL 未明確涵蓋的平臺，例如 Blazor、Unity、在桌面上的 Mono
- 您想要 UI 測試您的應用程式，並想要使用可與 Selenium 搭配使用的自動化瀏覽器 
- 瀏覽器和執行 MSAL 的應用程式位於不同的進程中

##### <a name="at-a-glance"></a>快速概覽

為了達到此目的，您將會提供 MSAL `start Url`，這需要在選擇的瀏覽器中顯示，讓使用者可以輸入他們的使用者名稱等等。驗證完成後，您的應用程式將需要傳回 MSAL，其中`end Url`包含 Azure AD 所提供的程式碼。
的主機`end Url` `redirectUri`一定是。 若要攔截`end Url` ，您可以： 

- 監視瀏覽器重新導向`redirect Url`直到到達或
- 讓瀏覽器重新導向至您監視的 URL

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是擴充點

`WithCustomWebUi`是一種擴充點, 可讓您在公用用戶端應用程式中提供自己的 UI, 並讓使用者通過身分識別提供者的/Authorize 端點, 並讓他們登入和同意。 MSAL.NET 可以, 然後兌換驗證碼並取得權杖。 這是用於 Visual Studio 中的實例, 以讓 electrons 應用程式 (例如 VS 意見反應) 提供 web 互動, 但讓它 MSAL.NET 來執行大部分的工作。 如果您想要提供使用者介面自動化, 也可以使用它。 在公用用戶端應用程式中, MSAL.NET 會使用 PKCE 標準 ([由 OAuth 公用用戶端進行程式碼交換的 RFC 7636-證明金鑰](https://tools.ietf.org/html/rfc7636)), 以確保遵守安全性:只有 MSAL.NET 可以兌換程式碼。

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>如何使用 WithCustomWebUi

若要使用`.WithCustomWebUI`, 您必須:
  
  1. 執行介面 (請參閱[這裡)。](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) `ICustomWebUi` 基本上, 您必須執行一種方法`AcquireAuthorizationCodeAsync`來接受授權碼 URL (由 MSAL.NET 計算), 讓使用者可以進行與識別提供者的互動, 然後傳回識別提供者所要使用的 URL已呼叫您的執行後 (包括授權碼)。 如果您有任何問題, 則您的執行`MsalExtensionException`應該會擲回例外狀況, 以便與 MSAL 完美合作。
  2. 在您`AcquireTokenInteractive`的呼叫中, 您`.WithCustomUI()`可以使用修飾詞來傳遞自訂 web UI 的實例

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>在測試自動化中執行 ICustomWebUi 的範例-SeleniumWebUI

MSAL.NET 小組已重寫我們的 UI 測試, 以利用這個擴充性機制。 如果您有興趣, 可以查看 MSAL.NET 原始程式碼中的[SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)類別

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>提供 SystemWebViewOptions 的絕佳體驗

從 MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)可讓您指定：

- 要導覽的 URI （`BrowserRedirectError`），或在系統網頁瀏覽器中登入/同意錯誤時要`HtmlMessageError`顯示的 HTML 片段（）
- 要在成功登入/`BrowserRedirectSuccess`同意時，流覽至的 URI （）`HtmlMessageSuccess`或要顯示的 HTML 片段（）。
- 要執行以啟動系統瀏覽器的動作。 為此，您可以藉由設定`OpenBrowserAsync`委派來提供自己的執行。 類別也提供兩個瀏覽器的預設執行： `OpenWithEdgeBrowserAsync`和`OpenWithChromeEdgeBrowserAsync`，分別適用于 Chromium 上的 microsoft edge 和[microsoft edge](https://www.windowscentral.com/faq-edge-chromium)。

若要使用這個結構，您可以撰寫如下所示的內容：

```CSharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>其他選擇性參數

`AcquireTokenInteractive`從[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)的參考檔深入瞭解的所有其他選擇性參數

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

如果您想要在網域或 Azure AD 加入的電腦上登入網域使用者, 您需要使用:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>條件約束

- AcquireTokenByIntegratedWindowsAuth (IWA) 僅適用于同盟使用者, 也就是在 Active Directory 中建立並受到 Azure Active Directory 支援的使用者。 直接在 AAD 中建立的使用者, 沒有受 AD 支援**管理**的使用者-無法使用此驗證流程。 這項限制並不會影響使用者名稱/密碼的流程。
- IWA 適用于針對 .NET Framework、.NET Core 和 UWP 平臺所撰寫的應用程式
- IWA 不會略過 MFA (多重要素驗證)。 如果已設定 MFA, 則在需要 MFA 挑戰時, IWA 可能會失敗, 因為 MFA 需要使用者互動。
  > [!NOTE]
  > 這一點很棘手。 IWA 非互動式，但 MFA 需要使用者互動。 您不會控制身分識別提供者要求執行 MFA 的時間，租使用者管理員會這麼做。 從我們的觀察中，當您從不同國家/地區登入時，若未透過 VPN 連線到公司網路，有時甚至是透過 VPN 連線時，就需要使用 MFA。 不預期有一組具決定性的規則，Azure Active Directory 使用 AI 來持續瞭解是否需要 MFA。 如果 IWA 失敗, 您應該回到使用者提示 (互動式驗證或裝置程式碼流程)。

- 傳入的`PublicClientApplicationBuilder`授權單位必須是:
  - 租使用者-ed (的形式`https://login.microsoftonline.com/{tenant}/` , `tenant`其中是代表租使用者識別碼的 guid 或與租使用者相關聯的網域。
  - 適用于任何公司和學校帳戶`https://login.microsoftonline.com/organizations/`()
  - 不支援 Microsoft 個人帳戶 (您無法使用/common 或/consumers 租使用者)

- 因為整合式 Windows 驗證是無訊息流程:
  - 您應用程式的使用者必須先前已同意使用應用程式
  - 或者, 租使用者系統管理員必須先前已同意租使用者中的所有使用者, 才能使用該應用程式。
  - 換句話說:
    - 身為開發人員, 您可以自行按下 Azure 入口網站的 [**授**與] 按鈕,
    - 或者, 租使用者系統管理員已在應用程式註冊的 [ **API 許可權**] 索引標籤中, 按下 [**授與/撤銷系統管理員同意**] 按鈕 (請參閱[新增許可權以存取 web api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - 或者您已提供使用者同意應用程式的方式 (請參閱[要求個別使用者同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - 或者您已提供一種方法讓租使用者管理員同意應用程式 (請參閱系統[管理員同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- 此流程已針對 .net desktop、.net core 和 Windows 通用 (UWP) 應用程式啟用。 在 .NET core 中, 只有取得使用者名稱的多載可供使用, 因為 .NET Core 平臺無法向 OS 要求使用者名稱。
  
如需有關同意的詳細資訊，請參閱[Microsoft 身分識別平臺許可權和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>如何使用它

您通常只需要一個參數 (`scopes`)。 不過, 視您的 Windows 系統管理員設定原則的方式而定, 您的 windows 電腦上的應用程式可能不允許查詢已登入的使用者。 在此情況下, 請使用第`.WithUsername()`二個方法, 並將登入使用者的使用者名稱傳入為 UPN `joe@contoso.com`格式-。

下列範例會呈現最新的案例, 並說明您可以取得的例外狀況類型及其緩和措施

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

如需 AcquireTokenByIntegratedWindowsAuthentication 上可能的修飾詞清單, 請參閱[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>使用者名稱/密碼

您也可以藉由提供使用者名稱和密碼來取得權杖。 此流程受到限制且不建議, 但仍有必要的使用案例。

### <a name="this-flow-isnt-recommended"></a>不建議使用此流程

**不建議使用**此流程, 因為詢問使用者密碼的應用程式並不安全。 如需此問題的詳細資訊, 請參閱[這篇文章](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 在已加入網域的 Windows 電腦上以無訊息方式取得權杖的慣用流程是[整合式 Windows 驗證](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 否則, 您也可以使用[裝置程式碼流程](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> 雖然這在某些情況下很有用 (DevOps 案例), 但如果您想要在提供 onw UI 的互動式案例中使用使用者名稱/密碼, 您應該真的考慮如何將它移開。 藉由使用使用者名稱/密碼, 您將會產生許多事項:
>
> - 新式身分識別的核心租使用者: 密碼會取得 fished、重新執行。 因為我們有可攔截的共用密碼概念。
> 這與無密碼不相容。
> - 需要執行 MFA 的使用者將無法登入 (因為沒有互動)
> - 使用者將無法進行單一登入

### <a name="constraints"></a>條件約束

下列條件約束也適用:

- 使用者名稱/密碼流程與條件式存取和多重要素驗證不相容:因此, 如果您的應用程式在租使用者系統管理員需要多重要素驗證的 Azure AD 租使用者中執行, 您就無法使用此流程。 許多組織都會這麼做。
- 僅適用于公司和學校帳戶 (不是 MSA)
- 此流程適用于 .net desktop 和 .net core, 但不適用於 UWP

### <a name="b2c-specifics"></a>B2C 細節

有關[使用 ROPC 與 B2C 的詳細資訊](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="how-to-use-it"></a>如何使用？

`IPublicClientApplication`包含方法`AcquireTokenByUsernamePassword`

下列範例呈現簡化的案例

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

下列範例會呈現最新的案例, 並說明您可以取得的例外狀況類型及其緩和措施

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

如需可套用至`AcquireTokenByUsernamePassword`之所有修飾詞的詳細資訊, 請參閱[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>命令列工具 (不含網頁瀏覽器)

### <a name="device-code-flow-why-and-how"></a>裝置程式碼流程為何？還有如何？

如果您撰寫的是命令列工具 (沒有 Web 控制項), 而且不能或不想使用先前的流程, 則需要使用`AcquireTokenWithDeviceCode`。

具有 Azure AD 的互動式驗證需要網頁瀏覽器 (如需詳細資訊, 請參閱[web 瀏覽器的使用](https://aka.ms/msal-net-uses-web-browser)方式)。 不過, 若要在不提供網頁瀏覽器的裝置或作業系統上驗證使用者, 裝置程式碼流程可讓使用者使用另一個裝置 (例如另一部電腦或行動電話) 以互動方式登入。 藉由使用裝置程式碼流程，應用程式會透過兩個步驟的進程取得權杖，特別針對這些裝置/Os 所設計。 這類應用程式的範例包括在 iOT 上執行的應用程式, 或命令列工具 (CLI)。 其概念如下:

1. 每當需要使用者驗證時, 應用程式就會提供代碼, 並要求使用者使用另一部裝置 (例如連線到網際網路的 smartphone) 來流覽至 URL (例如, `https://microsoft.com/devicelogin`), 其中會提示使用者輸入程式碼。 如此一來, 網頁將會引導使用者完成一般驗證體驗, 包括同意提示和多重要素驗證 (如有需要)。

2. 成功驗證之後, 命令列應用程式會透過後端通道接收所需的權杖, 並使用它來執行所需的 Web API 呼叫。

### <a name="code"></a>程式碼

`IPublicClientApplication`包含名為的方法`AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

這個方法會使用做為參數:

- `scopes`要為其要求存取權杖的
- 回呼, 將接收`DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

下列範例程式碼會提供最新的案例, 並說明您可以取得的例外狀況類型及其緩和措施。

```CSharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();
           
    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification 

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using 
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the 
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are 
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the 
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled. 
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads 
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

## <a name="file-based-token-cache"></a>以檔案為基礎的權杖快取

在 MSAL.NET 中，預設會提供記憶體內部權杖快取。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>序列化可自訂于 Windows 桌面應用程式和 web 應用程式/web Api

在 .NET Framework 和 .NET core 的案例中, 如果您未執行任何額外的作業, 記憶體中的權杖快取會持續在應用程式的持續時間內。 若要瞭解為何不提供預設的序列化, 請記住 MSAL .NET desktop/core 應用程式可以是主控台或 Windows 應用程式 (這可能會有檔案系統的存取權),**同時也**是 Web 應用程式或 Web API。 這些 Web 應用程式和 web Api 可能會使用一些特定的快取機制, 例如資料庫、分散式快取、redis 快取等等。 若要在 .NET Desktop 或 Core 中擁有持續性權杖快取應用程式, 您必須自訂序列化。

與權杖快取序列化相關的類別和介面為下列類型:

- ``ITokenCache``, 其定義要訂閱權杖快取序列化要求的事件, 以及用來序列化或還原序列化各種格式 (ADAL v3.0、MSAL 2.x 和 MSAL 3.x = ADAL v 5.0) 之快取的方法。
- ``TokenCacheCallback`` 是傳遞至事件的回呼，以便您處理序列化。 系統會使用類型``TokenCacheNotificationArgs``的引數來呼叫它們。
- ``TokenCacheNotificationArgs``只會提供``ClientId``應用程式的, 以及可使用權杖之使用者的參考

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 會為您建立權杖快取，並且在您呼叫應用程式的 `UserTokenCache` 和 `AppTokenCache` 屬性時提供 `IToken` 快取。 您不應該自行執行介面。 當您實作自訂權杖快取序列化時，您的責任是：
>
> - 對`BeforeAccess`和`AfterAccess` 「事件」 (或*非同步*對應) 做出回應。 委派會負責還原序列化快取, `AfterAccess`而其中一個會負責序列化快取。`BeforeAccess`
> - 其中有些事件會儲存或載入 Blob，其會透過事件引數傳遞到您想要的儲存體的。

這些策略會根據您是撰寫公用用戶端應用程式 (桌面) 的權杖快取序列化, 還是機密用戶端應用程式 (web 應用程式/Web API、daemon 應用程式) 而有所不同。

由於 MSAL V2. x, 您有數個選項, 取決於您是否要將快取序列化為 MSAL.NET 格式 (通用於 MSAL 的統一格式快取, 以及跨平臺), 或如果您也想要支援[舊版](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)權杖快取ADAL V3 的序列化。

在下列範例中, 會說明自訂權杖快取序列化以共用 ADAL.NET 3.x、ADAL.NET 5.x 和 MSAL.NET 之間的 SSO 狀態: [active-目錄-dotnet-v1 至 v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>簡單權杖快取序列化 (僅限 MSAL)

以下是適用於傳統型應用程式的自訂權杖快取序列化的單純實作範例。 這裡的使用者權杖快取位於與應用程式相同的資料夾中。

建立應用程式之後, 您可以藉由呼叫``TokenCacheHelper.EnableSerialization()``傳遞應用程式來啟用序列化`UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

此 helper 類別如下列程式碼片段所示:

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

適用于公用用戶端應用程式 (適用于在 Windows、Mac 和 linux 上執行的桌面應用程式) 的產品品質權杖快取檔案型序列化程式預覽, 可從[Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)開放原始碼程式庫取得。 您可以從下列 nuget 套件，將它包含在您的應用程式中：[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> 免責聲明. Msal 程式庫是透過 MSAL.NET 的延伸模組。 這些程式庫中的類別可能會在未來 MSAL.NET, 也就是透過重大變更來進行。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>雙重權杖快取序列化 (MSAL 整合快取 + ADAL V3)

如果您想要使用統一快取格式來執行權杖快取序列化 (通用於 ADAL.NET 4.x 和 MSAL.NET 2.x, 另一種是相同層代或更舊版本的其他 MSALs, 請在相同的平臺上), 您可以透過下列程式碼取得靈感:

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

這次 helper 類別看起來會像下列程式碼:

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [從桌面應用程式呼叫 Web API](scenario-desktop-call-api.md)
