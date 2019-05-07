---
title: Azure AD B2C （適用於.NET 的 Microsoft 驗證程式庫） |Azure
description: 使用 Azure AD B2C 使用 Microsoft Authentication Library for.NET (MSAL.NET) 時，請了解特定的考量。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c608518a9eb80d807297f010778ae452c0f61f5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075771"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 具社交識別的使用者登入

您可以使用登入具社交識別的使用者使用 MSAL.NET [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c)。 Azure AD B2C 是根據原則的概念。 在 MSAL.NET，指定原則轉譯來提供授權單位。

- 當您具現化公用用戶端應用程式時，您需要指定原則授權單位。
- 當您想要套用原則時，您必須呼叫的覆寫`AcquireTokenInteractive`包含`authority`參數。

此頁面是 msal 3.x。 如果您有興趣使用 MSAL 2.x，請參閱[msal 的 Azure AD B2C 細節 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>適用於 Azure AD B2C 租用戶和原則授權單位

若要使用的授權單位是`https://login.microsoftonline.com/tfp/{tenant}/{policyName}`其中：

- `tenant` Azure AD B2C 租用戶的名稱 
- `policyName` 要套用 (例如"b2c_1_susi"的符號-/-註冊) 的原則名稱。

從 Azure AD B2C 的目前指導方針是使用`b2clogin.com`為授權單位。 例如： `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`。 如需詳細資訊，請參閱此[文件](/azure/active-directory-b2c/b2clogin)。

## <a name="instantiating-the-application"></a>具現化應用程式

建置應用程式時，您必須提供授權單位。

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>取得權杖，以便套用原則

取得權杖的 Azure AD B2C 的 公用用戶端應用程式中受保護的 API 會要求您使用含有授權單位的覆寫：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

取代為

- `policy` 正在其中一個先前的字串 (例如`PolicySignUpSignIn`)。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` 是一種方法，以尋找特定原則的帳戶。 例如︰

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

套用原則 （例如讓使用者編輯其設定檔或重設其密碼） 目前是藉由呼叫`AcquireTokenInteractive`。 在這兩個原則的情況下，您不使用傳回的權杖 / 驗證結果。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile 和 ResetPassword 原則的特殊案例

當您想要提供的體驗您的使用者使用社交身分識別，登入的位置，然後編輯其設定檔時要套用的 Azure AD B2C EditProfile 原則。 若要這樣做，方法是藉由呼叫`AcquireTokenInteractive`特定的授權單位，該原則與設定為提示`Prompt.NoPrompt`以避免顯示 （因為使用者已登入） 的 [帳戶選取] 對話方塊

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>資源擁有者密碼認證 (ROPC) 與 Azure AD B2C
如需詳細的 ROPC 流程的詳細資訊，請參閱本[文件](v2-oauth-ropc.md)。

此流程很**不建議使用**因為您的應用程式向使用者取得他們的密碼並不安全。 如需有關此問題的詳細資訊，請參閱 <<c0> [ 這篇文章](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

藉由使用使用者名稱/密碼，您會讓總有許多種：
- 核心的現代的身分識別租用戶： 取得 web-inf 密碼，重新執行。 因為我們沒有這個概念可以被攔截的共用祕密。 這是與無密碼不相容。
- 需要進行 MFA 的使用者將無法登入 （因為沒有任何互動）。
- 使用者將無法執行單一登入。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 設定 ROPC 流程
在您的 Azure AD B2C 租用戶，請在建立新的使用者流程，然後選取**登入使用 ROPC**。 這可讓 ROPC 原則，為您的租用戶。 請參閱[設定資源擁有者密碼認證流程](/azure/active-directory-b2c/configure-ropc)如需詳細資訊。

`IPublicClientApplication` 包含的方法：
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

這個方法會採用做為參數：
- *範圍*來要求存取權杖。
- A *username*。
- SecureString*密碼*使用者。

請記得使用包含 ROPC 原則授權單位。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流程的限制
 - ROPC 流向**僅適用於本機帳戶**(向 Azure AD B2C 使用電子郵件或使用者名稱)。 此流程無法運作如果同盟至任何 Azure AD B2C 所支援的身分識別提供者 (Facebook、 Google、 等等。)。
 - 目前沒有**沒有 id_token 傳回從 Azure AD B2C**實作從 MSAL ROPC 流程時。 這表示無法建立帳戶物件，因此快取中，會有任何帳戶並沒有任何使用者。 AcquireTokenSilent 流程在此案例中，將無法運作。 不過，ROPC 不會顯示 UI，因此將不會影響使用者體驗。

## <a name="google-auth-and-embedded-webview"></a>Google 驗證和內嵌 web 檢視

如果您是 Azure AD B2C 的開發人員使用 Google 作為身分識別提供者我們建議您在使用系統瀏覽器中，因為不允許 Google[內嵌 web 檢視來自驗證](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前，`login.microsoftonline.com`是 google 的受信任授權單位。 使用此授權單位，將會使用內嵌 web 檢視。 不過使用`b2clogin.com`不受信任的授權單位，google，因此使用者將無法進行驗證。

我們會提供更新的 wiki，這[問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)如果項目變更。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>使用 Azure AD B2C 中 MSAL.Net 快取 

### <a name="known-issue-with-azure-ad-b2c"></a>使用 Azure AD B2C 的已知的問題

支援 MSAL.Net[權杖快取](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)。 權杖快取索引鍵為基礎的身分識別提供者所傳回的宣告。 目前 MSAL.Net 需要兩個宣告建立權杖快取索引鍵：  
- `tid` 這是 Azure AD 租用戶識別碼，以及 
- `preferred_username` 

遺漏的許多 Azure AD B2C 案例中這兩個這些宣告。 

客戶的影響是，以顯示 [使用者名稱] 欄位時，您收到 「 權杖回應中的遺漏 」 的值？ 如果是的話，這是因為 Azure AD B2C 不會傳回值中的 preferred_username IdToken 由於社交帳戶與外部識別提供者 (Idp) 的限制。 Azure AD 傳回 preferred_username 的值，因為它知道誰是使用者，但 Azure AD b2c 中，因為使用者可以登入的本機帳戶、 Facebook、 Google、 GitHub 等有不一致的值，用於 preferred_username 的 Azure AD b2c。 若要解鎖 MSAL 從快取與 ADAL 的相容性所推出，我們決定 IdToken 不傳回任何東西的 preferred_username 時處理的 Azure AD B2C 帳戶時，我們這一端使用 「 遺漏權杖回應中的 」。 MSAL 必須傳回 preferred_username 來維護快取的相容性在程式庫之間的值。

### <a name="workarounds"></a>解决方法

#### <a name="mitigation-for-the-missing-tenant-id"></a>如何避免遺失的租用戶識別碼

建議的因應措施是使用[快取原則](#acquire-a-token-to-apply-a-policy)

或者，您可以使用`tid`宣告，如果您使用[B2C 自訂原則](https://aka.ms/ief)，因為它能夠讓您傳回至應用程式的其他宣告。 若要深入了解[宣告轉型](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>「 權杖回應中遺漏 」 的風險降低
其中一個選項是使用"name"宣告做為慣用的使用者名稱。 程序在此所述[B2C 文件](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions)-> 在傳回的宣告中，選擇您想要在傳送回到您的應用程式，成功設定檔編輯體驗後的授權權杖中傳回的宣告。 比方說，選取 顯示名稱、 郵遞區號。 」

## <a name="next-steps"></a>後續步驟 

下列範例提供取得權杖以互動方式使用 MSAL.NET 的 Azure AD B2C 應用程式的更多詳細的。

| 範例 | 平台 | 描述|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、 Xamarin Android、 UWP | 展示如何透過 Azure AD B2C 中，驗證使用者，並存取 Web API 使用產生的權杖使用 MSAL.NET 的簡單 Xamarin Forms 應用程式。|
