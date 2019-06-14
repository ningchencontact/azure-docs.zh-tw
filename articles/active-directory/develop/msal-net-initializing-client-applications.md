---
title: 初始化用戶端應用程式 (Microsoft Authentication Library for.NET) |Azure
description: 深入了解初始化公用用戶端與機密用戶端應用程式使用 Microsoft Authentication Library for.NET (MSAL.NET)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f22ff41e380a16af2aa45df9a61eefbf293ff83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544324"
---
# <a name="initialize-client-applications-using-msalnet"></a>初始化使用 MSAL.NET 的用戶端應用程式
本文說明初始化公用用戶端與機密用戶端應用程式使用 Microsoft Authentication Library for.NET (MSAL.NET)。  若要了解用戶端應用程式類型與應用程式組態選項的詳細資訊，請閱讀[概觀](msal-client-applications.md)。

使用 MSAL.NET 3.x 中，具現化應用程式的建議的方式是使用應用程式建造者：`PublicClientApplicationBuilder`和`ConfidentialClientApplicationBuilder`。 其可提供強大的機制，來設定應用程式以從程式碼，或從組態檔，或甚至是透過混合這兩種方法。

## <a name="prerequisites"></a>必要條件
然後再初始化應用程式，您必須先[註冊](quickstart-register-app.md)，讓您的應用程式可以與 Microsoft 身分識別平台整合。  註冊之後，您可能需要下列資訊 （這可以在 Azure 入口網站中找到）：

- 用戶端識別碼 （代表 GUID 的字串）
- （具名執行個體） 的身分識別提供者 URL 和您的應用程式的登入對象。 這兩個參數統稱為授權單位。
- 如果您正在撰寫企業營運應用程式僅供您的組織 （也命名單一租用戶的應用程式） 租用戶識別碼。
- 應用程式祕密 （用戶端祕密的字串） 或憑證 （屬於類型 X509Certificate2） 如果它是機密用戶端應用程式。
- 針對 web 應用程式，以及有時公用用戶端應用程式 （尤其當您的應用程式需要使用訊息代理程式），您必須也會將 redirectUri 身分識別提供者，請連絡後利用安全性權杖的應用程式。

## <a name="ways-to-initialize-applications"></a>若要初始化應用程式的方式
有許多不同的方式來具現化用戶端應用程式。

### <a name="initializing-a-public-client-application-from-code"></a>初始化從程式碼公開用戶端應用程式

下列程式碼會具現化是公用的用戶端應用程式中，在 Microsoft Azure 公用雲端中，使用其公司和學校帳戶或其個人的 Microsoft 帳戶登入的使用者。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>初始化從程式碼的機密用戶端應用程式

同樣地，在下列程式碼會具現化的機密的應用程式 (Web 應用程式位於`https://myapp.azurewebsites.net`) 處理來自 Microsoft Azure 公用雲端與他們的工作和學校帳戶或其個人的 Microsoft 帳戶中的使用者權杖。 應用程式是由共用用戶端祕密會識別身分識別提供者：

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

您可能已經知道，在生產環境中，而不是使用用戶端祕密，您可能想要共用與 Azure AD 憑證。 然後程式碼應如下所示：

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>初始化從設定選項公開用戶端應用程式

下列程式碼會具現化的組態物件，這可能會填滿單元以程式設計方式或從組態檔讀取公用用戶端應用程式：

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>正在初始化組態選項的機密用戶端應用程式

這種模式適用於機密用戶端應用程式。 您也可以新增使用其他參數`.WithXXX`修飾詞 （在此憑證）。

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>產生器修飾詞

使用應用程式建造者，許多程式碼片段`.With`方法可以套用為修飾詞 (例如`.WithCertificate`和`.WithRedirectUri`)。 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>公用和機密用戶端應用程式通用的修飾詞

您可以設定公用用戶端或機密用戶端應用程式產生器的修飾詞為：

|參數 | 描述|
|--------- | --------- |
|`.WithAuthority()` 7 覆寫 | 將應用程式預設授權單位設定為 Azure AD 授權單位，可能會選擇 Azure 雲端，對象，租用戶 （租用戶識別碼或網域名稱），或直接提供授權單位 URI。|
|`.WithAdfsAuthority(string)` | 設定應用程式預設權限是 ADFS 授權單位。|
|`.WithB2CAuthority(string)` | 設定應用程式預設權限是 Azure AD B2C 的授權單位。|
|`.WithClientId(string)` | 覆寫用戶端識別碼。|
|`.WithComponent(string)` | 設定使用 MSAL.NET （原因遙測） 的程式庫的名稱。 |
|`.WithDebugLoggingCallback()` | 如果呼叫，應用程式會呼叫`Debug.Write`只需啟用偵錯追蹤。 請參閱[記錄](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)如需詳細資訊。|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 設定應用程式層級的額外查詢參數，將所有驗證要求中傳送。 這是在每個權杖取得方法層級可覆寫 (具有相同`.WithExtraQueryParameters pattern`)。|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | 可以啟用進階案例，例如設定適用於 HTTP proxy，或強制將 MSAL （例如在 ASP.NET Core web 應用程式/Api) 使用特定的 HttpClient。|
|`.WithLogging()` | 如果呼叫，則應用程式會呼叫回呼，以進行偵錯追蹤。 請參閱[記錄](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)如需詳細資訊。|
|`.WithRedirectUri(string redirectUri)` | 覆寫預設值重新導向 URI。 如果是公用的用戶端應用程式，這會是適用於牽涉到訊息代理程式的案例。|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 設定用來將遙測傳送的委派。|
|`.WithTenantId(string tenantId)` | 租用戶識別碼或租用戶描述，會覆寫。|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.iOS 應用程式專有的修飾詞

您可以在 Xamarin.iOS 上公開用戶端應用程式產生器設定的修飾詞為：

|參數 | 描述|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS 只**:設定 iOS 鑰匙圈的安全性群組 （適用於快取持續性）。|

### <a name="modifiers-specific-to-confidential-client-applications"></a>機密用戶端應用程式專有的修飾詞

您可以設定機密用戶端應用程式產生器的修飾詞為：

|參數 | 描述|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | 設定用來識別與 Azure AD 應用程式的憑證。|
|`.WithClientSecret(string clientSecret)` | 設定用來識別與 Azure AD 應用程式的用戶端密碼 （應用程式密碼）。|

這些修飾詞互斥。 如果您同時提供兩者，MSAL 就會擲回有意義的例外狀況。

### <a name="example-of-usage-of-modifiers"></a>修飾詞的使用方式的範例

例如，假設您的應用程式是僅供貴組織的特定業務應用程式。  然後您可以撰寫：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

它會變得有趣是國家/地區雲端程式設計現在已簡化。 如果您想要在國家雲端中的多租用戶應用程式的應用程式，您可以撰寫，例如：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

另外還有 ADFS 的覆寫 （ADFS 2019 目前不支援）：
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您是 Azure AD B2C 的開發人員，您可以指定您的租用戶，像這樣：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
