---
title: 呼叫 web Api （取得應用程式權杖）-Microsoft 身分識別平台的桌面應用程式
description: 了解如何建置傳統型應用程式呼叫 web Api (應用程式權杖 |)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d84801d6368bcc29f08145f190c2a07c64050ced
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795092"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api-的傳統型應用程式取得權杖

一旦您已建立您`IPublicClientApplication`，您將使用它來取得權杖，您將使用它來呼叫 web API。

## <a name="recommended-pattern"></a>建議的模式

Web API 由定義其`scopes`。 無論您在您的應用程式中提供的體驗會想要使用的模式是：

- 有系統地嘗試從權杖快取中取得權杖，藉由呼叫 `AcquireTokenSilent`
- 如果這個呼叫失敗，使用`AcquireToken`您想要使用的流程 (此處由`AcquireTokenXX`)

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

以下是現在的各種方式來取得權杖，在桌面應用程式的詳細資料

## <a name="acquiring-a-token-interactively"></a>以互動方式取得權杖

下列範例會顯示最少的程式碼，以互動方式來讀取使用者的設定檔，使用 Microsoft Graph 取得的權杖。

```CSharp
string[] scopes = new string["user.read"];
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

### <a name="mandatory-parameters"></a>必要參數

`AcquireTokenInteractive` 只有一個必要參數``scopes``，其中包含的字串，定義語彙基元是必要的範圍列舉。 適用於 Microsoft Graph 權杖時，可以找到所需的範圍中的每個 Microsoft graph API 的 api 參考名為 「 權限 」 一節。 例如，若要[列出使用者的連絡人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，範圍 」 User.Read"，"Contacts.Read 」 將會需要用到。 另請參閱[Microsoft Graph 權限參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

您需要在 Android 上，也指定父活動 (使用`.WithParentActivityOrWindow`，如下所示)，讓權杖取回該父活動之後的互動。 呼叫時，如果您未指定它，將會擲回例外狀況`.ExecuteAsync()`。

### <a name="specific-optional-parameters"></a>特定的選擇性參數

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

互動式、 UI 是很重要。 `AcquireTokenInteractive` 有一個特定的選擇性參數，以指定平台開發人員，可以讓父 UI。 當用於桌面應用程式，`.WithParentActivityOrWindow`有不同的類型，根據平台：

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

- 在.NET Standard，預期`object`是`Activity`在 Android 上，`UIViewController`在 iOS 上，`NSWindow`在 MAC 上，而`IWin32Window`或`IntPr`在 Windows 上。
- 在 Windows 中，您必須呼叫`AcquireTokenInteractive`從 UI 執行緒，讓內嵌的瀏覽器取得適當的 UI 同步處理內容。  無法從 UI 執行緒呼叫可能會造成不正確地幫浦和/或死結情況下，使用 UI 的訊息。 其中一種方式呼叫 MSAL 從 UI 執行緒上，如果您不在 UI 執行緒上的已使用`Dispatcher`在 WPF 上。
- 如果您使用 WPF，讓視窗在 WPF 控制項中，您可以使用`WindowInteropHelper.Handle`類別。 呼叫是然後，在 WPF 控制項中 (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用來指定提示來控制使用者互動

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

此類別會定義下列常數：

- ``SelectAccount``： 將會強制 STS，以呈現包含的帳戶的使用者具有工作階段 [帳戶選取] 對話方塊。 當應用程式開發人員想要讓使用者選擇不同的身分識別時，這個選項非常有用的。 此選項可促使 MSAL 傳送``prompt=select_account``身分識別提供者。 這個選項預設值，而且它會提供可能最佳體驗 （帳戶、 使用者和等等工作階段的目前狀態可用資訊為基礎的很好。 ...).除非您有這麼好的理由不要變更它。
- ``Consent``： 可讓應用程式開發人員若要強制使用者會提示要求同意，即使之前已授與同意。 在此情況下，將 MSAL 傳送`prompt=consent`身分識別提供者。 此選項可用在某些考量著重於安全性應用程式組織管理要求使用者在使用應用程式每次會看到同意對話方塊。
- ``ForceLogin``： 可讓應用程式開發人員，以便使用者即使不需要此使用者提示，提示輸入認證服務。 此選項很有用，如果取得權杖失敗，讓使用者重新登入。 在此情況下，將 MSAL 傳送`prompt=login`身分識別提供者。 同樣地，我們已了解在組織的控管需求，使用者 relogs-在每次存取應用程式的特定部分的其中一些考量著重於安全性應用程式中使用它。
- ``Never`` （適用於.NET 4.5 和 WinRT 只） 不會提示使用者，但改為將會嘗試使用 cookie 儲存在隱藏的內嵌的 web 檢視中 (如下所示：Web 檢視中 MSAL.NET）。 使用此選項可能會失敗，並在此情況下`AcquireTokenInteractive`將會擲回例外狀況通知所需的 UI 整合，您必須使用另一個`Prompt`參數。
- ``NoPrompt``:不會傳送至身分識別提供者的任何提示。 此選項僅適用於 Azure AD B2C 編輯設定檔原則 (請參閱[B2C 細節](https://aka.ms/msal-net-b2c-specificities))。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

此修飾詞用在進階案例中您想要預先同意預付數個資源的使用者 (而不想要使用的增量同意，通常會搭配 MSAL.NET / Microsoft 身分識別平台 v2.0)。 如需詳細資訊，請參閱[how to： 讓使用者事先同意幾項資源](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)。

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是個擴充點

`WithCustomWebUi` 是擴充性點，可讓您提供您自己的 UI 中公開的用戶端應用程式，並讓使用者瀏覽 /Authorize 端點的身分識別提供者，讓他們登入，並同意。 MSAL.NET 可以、 然後兌換的驗證碼，並取得權杖。 它是執行個體用於 Visual Studio 中已的轉移應用程式 （例如 VS 意見反應） 提供 web 互動，但它留給 MSAL.NET 大部分的工作。 您也可以使用它如果您想要提供使用者介面自動化。 公開用戶端應用程式，MSAL.NET 使用 PKCE 標準 ([RFC 7636-程式碼交換 OAuth 公用用戶端的證明金鑰](https://tools.ietf.org/html/rfc7636)) 以確保會遵守安全性：只有 MSAL.NET 可以兌換代碼。

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>如何使用 WithCustomWebUi

若要使用`.WithCustomWebUI`，您需要：
  
  1. 實作`ICustomWebUi`介面 (請參閱 <<c2> [ 這裡](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 您基本上必須實作其中一種方法`AcquireAuthorizationCodeAsync`接受 （計算方式 MSAL.NET） 的程式碼授權 URL，讓使用者透過身分識別提供者互動，然後返回上一步身分識別提供者會用的 URL呼叫您的實作上一步 （包含授權碼）。 如果您有問題，您的實作應該擲回`MsalExtensionException`妥善相互合作，使用 MSAL 的例外狀況。
  2. 在您`AcquireTokenInteractive`呼叫中，您可以使用`.WithCustomUI()`傳遞您的自訂執行個體的修飾詞 web UI

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>在測試自動化-SeleniumWebUI ICustomWebUi 實作的範例

MSAL.NET 小組已重新撰寫我們的 UI 測試，以利用此擴充性機制。 如果您想要您可以看看[SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) MSAL.NET 原始程式碼中的類別

#### <a name="other-optional-parameters"></a>其他選擇性參數

深入了解所有其他選擇性參數`AcquireTokenInteractive`的參考文件從[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>整合式的 Windows 驗證

如果您想要的網域使用者登入網域或 Azure AD 已加入機器，您需要使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>條件約束

- AcquireTokenByIntegratedWindowsAuth (IWA)，才可供**同盟**僅限使用者，也就是說，Active Directory 中建立，並受到 Azure Active Directory 的使用者。 直接在 AAD 中，而不需要 AD 備份-所建立的使用者**受控**使用者-無法使用此驗證流程。 這項限制不會影響的使用者名稱/密碼的流程。
- 針對.NET Framework、.NET Core 和 UWP 平台所撰寫的應用程式是 IWA
- IWA 不會不會略過 MFA （多重要素驗證）。 如果設定了 MFA，則 IWA 可能會失敗，如果 MFA 挑戰是必要的因為，MFA 還需要使用者互動。
  > [!NOTE]
  > 這是棘手。 IWA 為非互動式的但 2FA 需要使用者互動性。 您無法控制身分識別提供者要求有 2FA 才能執行時，租用戶系統管理員。 從我們的觀察，2FA 時需要您登入來自不同國家/地區中，當未連接透過 VPN 到公司網路，並透過 VPN 連線時，有時甚至。 千萬不要將具有決定性的一組規則，Azure Active Directory 會使用 AI 來持續了解是否需要 2FA。 如果 IWA 失敗，您應該將後援使用者的提示 （互動式驗證或裝置的程式碼流程）。

- 授權單位傳入`PublicClientApplicationBuilder`必須是：
  - 租用戶 ed (的表單`https://login.microsoftonline.com/{tenant}/`其中`tenant`是任一個 guid，代表租用戶識別碼或租用戶相關聯的網域。
  - 任何工作和學校帳戶 (`https://login.microsoftonline.com/organizations/`)

  > 不支援 Microsoft 個人帳戶 （您無法使用 /common 或 /consumers 租用戶）

- 因為整合式 Windows 驗證是無訊息的流程：
  - 應用程式的使用者必須使用應用程式之前同意
  - 或者，租用戶系統管理員必須之前同意使用應用程式的租用戶中的所有使用者。
  - 換句話說：
    - 您身為開發人員可能是已按下**授與**，為您自己在 Azure 入口網站上的按鈕
    - 或租用戶系統管理員已按下**授與/撤銷 {租用戶網域} 的系統管理員同意**按鈕**API 的權限**應用程式註冊 索引標籤 (請參閱[新增權限存取 web Api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - 或您提供使用者同意應用程式的方式 (請參閱[要求個別使用者同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - 或您提供的租用戶系統管理員同意應用程式的方式 (請參閱[系統管理員同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- 此流程會啟用.net desktop、.net core 和 Windows 通用 (UWP) 應用程式。 在.NET core 上採取使用者名稱的多載提供功能，如.NET Core 平台不能要求到作業系統的使用者名稱。
  
如需有關同意的詳細資訊，請參閱[v2.0 權限及同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>如何使用它

您通常只需要一個參數 (`scopes`)。 不過取決於您的 Windows 系統管理員已設定原則的方式，很可能您的 windows 機器上的應用程式並不允許查詢登入的使用者。 在此情況下，使用第二種方法`.WithUsername()`，並傳入作為 UPN 的格式-登入使用者的使用者名稱`joe@contoso.com`。

下列範例會顯示最新的案例中，說明您可以取得，例外狀況和及其補救措施的種類

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

如需可能 AcquireTokenByIntegratedWindowsAuthentication 修飾詞的清單，請參閱[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>使用者名稱/密碼

您也可以藉由提供使用者名稱和密碼來取得權杖。 此流程有限，並且不建議使用，但有仍然使用情況下，很必要。

### <a name="this-flow-isnt-recommended"></a>此流程，不建議

此流程很**不建議使用**因為您的應用程式向使用者取得他們的密碼不安全。 如需有關此問題的詳細資訊，請參閱 <<c0> [ 這篇文章](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 取得權杖，以無訊息方式在加入網域的 Windows 電腦上的慣用流程[整合式 Windows 驗證](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 否則您也可以使用[裝置程式碼流程](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> 雖然這非常有用，在某些情況下 （DevOps 案例中），如果您想要使用使用者名稱/密碼的互動案例中，您可以提供您 onw UI，您真的應該考慮如何將移開。 使用使用者名稱/密碼您會讓總有許多種：
>
> - 核心的現代的身分識別租用戶： 取得 web-inf 密碼，重新執行。 因為我們沒有這個概念可以被攔截的共用祕密。
> 這是與無密碼不相容。
> - 需要進行 MFA 的使用者將無法登入 （因為沒有任何互動）
> - 使用者將無法執行單一登入

### <a name="constraints"></a>條件約束

也適用下列限制：

- 使用者名稱/密碼流程與不相容的條件式存取和 multi-factor authentication:如此一來，如果您的應用程式會在 Azure AD 租用戶執行租用戶系統管理員需要多重要素驗證的位置，您無法使用此流程。 許多組織都這麼做。
- 它只適用於工作和學校帳戶 (不是 MSA)
- 流程可用於.net 桌面應用程式和.net core，但不要依賴 UWP

### <a name="b2c-specifics"></a>B2C 詳細資料

[更多有關使用 ROPC 搭配 B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="how-to-use-it"></a>如何使用它？

`IPublicClientApplication`包含方法 `AcquireTokenByUsernamePassword`

下列範例顯示簡化的案例

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

下列範例會顯示最新的案例中，說明您可以取得，例外狀況和及其補救措施的種類

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

如需有關可套用至所有修飾詞`AcquireTokenByUsernamePassword`，請參閱[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>（不含網頁瀏覽器） 的命令列工具

### <a name="device-code-flow-why-and-how"></a>裝置程式碼流程為何？以及如何？

如果您要撰寫命令列工具 （也就不需要 Web 控制項），而且無法或不想要使用先前的流程，您必須使用`AcquireTokenWithDeviceCode`。

使用 Azure AD 的互動式驗證需要網頁瀏覽器 (如需詳細資訊，請參閱[使用量的網頁瀏覽器](https://aka.ms/msal-net-uses-web-browser))。 不過，若要驗證的裝置或作業系統不提供網頁瀏覽器上的使用者，裝置程式碼流程讓使用者使用另一個裝置 （例如另一部電腦或行動電話） 登入以互動方式。 藉由使用裝置程式碼流程，應用程式取得權杖，透過特別設計，這些裝置/作業系統雙步驟程序。 這類應用程式的範例包括 iOT、 或命令列工具 (CLI) 上執行的應用程式。 這個概念是：

1. 需要使用者驗證時，應用程式提供的程式碼，並要求使用者使用 （例如連線到網際網路的智慧型手機） 的其他裝置，巡覽至的 URL (例如， `https://microsoft.com/devicelogin`)，輸入程式碼會提示使用者。 完成之後，web 網頁將會導致透過一般的驗證體驗，包括如有必要的同意提示和 multi-factor authentication 使用者。

2. 驗證成功後，命令列應用程式將會收到需要的語彙基元，透過後頻道，並將它用來執行它所需要的 web API 呼叫。

### <a name="code"></a>程式碼

`IPublicClientApplication`包含一個名為方法 `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

這個方法會採用做為參數：

- `scopes`來要求存取權杖
- 將會收到的回呼 `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

下列的範例程式碼會將最新的案例中，提供的例外狀況，您可以取得，以及其如何避免種類的說明。

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>以檔案基礎的權杖快取

在 MSAL.NET 中，預設會提供記憶體內部權杖快取。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>序列化是可自訂的 Windows 傳統型應用程式和 web 應用程式/web Api

在.NET Framework 和.NET core，如果您沒有進行任何額外，記憶體內部權杖快取會持續期間的應用程式。 若要了解為什麼序列化未提供內建的請記住 MSAL.NET 桌面/core 應用程式可以是主控台或 Windows 應用程式 （它們會有存取檔案系統），**還**Web 應用程式或 web API。 這些 Web 應用程式和 web Api 可能會使用某些特定快取機制，例如資料庫、 分散式快取，redis 快取，依此類推。 若要讓.NET 桌面或核心中的持續性權杖快取應用程式，您必須自訂的序列化。

類別和介面所涉及權杖快取序列化是下列類型：

- ``ITokenCache``其定義權杖快取序列化要求，以及要序列化或還原序列化的各種不同的格式之快取的方法訂閱事件 (ADAL 3.0 版，MSAL 2.x 和 MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` 是傳遞至事件的回呼，以便您處理序列化。 引數的型別會呼叫它們``TokenCacheNotificationArgs``。
- ``TokenCacheNotificationArgs`` 只提供``ClientId``應用程式和使用者語彙基元是可用的參考

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 會為您建立權杖快取，並且在您呼叫應用程式的 `GetUserTokenCache` 和 `GetAppTokenCache` 方法時提供 `IToken` 快取。 您不應該自己實作的介面。 當您實作自訂權杖快取序列化時，您的責任是：
>
> - 回應`BeforeAccess`並`AfterAccess`「 事件 」 (或它們*非同步*對應)。 `BeforeAccess`委派會負責將快取中，還原序列化，而`AfterAccess`一個負責序列化的快取。
> - 其中有些事件會儲存或載入 Blob，其會透過事件引數傳遞到您想要的儲存體的。

策略並不取決於如果您要撰寫一個公用用戶端應用程式 （桌面），或機密用戶端應用程式 (web 應用程式/web API，精靈應用程式) 的權杖快取序列化。

因為 MSAL V2.x 有數個選項，取決於您想要序列化的快取，只以 MSAL.NET 格式 （統一的格式為快取常見 MSAL，同時也是跨平台），則您也想要支援[舊版](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)權杖快取的 ADAL V3 的序列化。

自訂權杖快取序列化 SSO 間共用狀態 ADAL.NET 3.x，ADAL.NET 5.x 和 MSAL.NET 會說明部分的下列範例： [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>簡單權杖快取序列化 (僅限 MSAL)

以下是適用於傳統型應用程式的自訂權杖快取序列化的單純實作範例。 以下的檔案與應用程式相同的資料夾中的使用者權杖快取。

建置應用程式之後，您會啟用序列化呼叫``TokenCacheHelper.EnableSerialization()``傳遞應用程式 `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

這個 helper 類別看起來像下列程式碼片段：

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

產品品質權杖快取的檔案為基礎 （適用於 Windows、 Mac 和 linux 上執行的桌面應用程式） 的公用用戶端應用程式的序列化程式是可從預覽[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)開放原始碼程式庫。 您可以從下列 nuget 套件，將它包含在您的應用程式中：[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> 免責聲明。 Microsoft.Identity.Client.Extensions.Msal 程式庫透過 MSAL.NET 是延伸模組。 這些程式庫中的類別可能會變成一直 MSAL.NET 以後，或具有重大變更。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>雙重權杖快取序列化 （unified MSAL 快取 + ADAL V3）

如果您想要實作權杖快取序列化都有整合快取格式 (通用於 ADAL.NET 4.x 和 MSAL.NET 2.x 中，與其他 MSALs 世代相同或更舊版本，在相同的平台上)，您可以獲得啟發，並由下列程式碼:

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

這次的協助程式類別看起來像下列程式碼中：

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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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
> [從桌面應用程式呼叫 web API](scenario-desktop-call-api.md)
