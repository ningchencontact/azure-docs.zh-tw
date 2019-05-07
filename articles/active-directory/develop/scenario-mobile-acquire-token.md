---
title: 呼叫 web Api-取得權杖的應用程式的行動裝置應用程式 |Microsoft 身分識別平台
description: 了解如何建置行動應用程式呼叫 web Api （取得應用程式權杖）
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6933bfbbff574495655ef9065a786fa313b02bd6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075171"
---
# <a name="mobile-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api-的行動裝置應用程式取得權杖

您可以開始呼叫受保護之前的 web Api，您的應用程式需要存取權杖。 本節會引導您使用 Microsoft Authentication Library (MSAL) 取得權杖的程序。

## <a name="scopes-to-request"></a>要求的範圍

當要求權杖時，範圍永遠都會是必要的。 範圍可決定您的應用程式可以存取哪些資料。  

簡單的方法是將所需的 web API 的`App ID URI`與範圍`.default`。 這會告訴 Microsoft 身分識別應用程式需要在入口網站中設定的所有範圍。

Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="acquiring-tokens"></a>取得權杖

### <a name="via-msal"></a>via MSAL

MSAL 可讓應用程式取得權杖，以無訊息方式和互動方式。 只會呼叫這些方法，MSAL 會傳回所要求的範圍的存取權杖。 正確的模式是要執行無訊息的要求，然後回到互動式要求。

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently
// Result is in our silent callback (success or error)
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or >1 account */
        }
    }
});    

[...]

// No accounts found, interactively request a token 
// TODO: Create an interactive callback to catch successful or failed request
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize our app 
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means we need to ask the user to sign-in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition 
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

```CSharp
string[] scopes = new string["https://graph.microsoft.com/.default"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException e)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="via-protocol"></a>透過通訊協定

不建議使用進行直接對通訊協定。 您的應用程式將無法支援的許多單一登入 (SSO) 的案例，並不能支援所有的裝置管理與條件式存取案例。

當使用通訊協定的行動裝置應用程式取得權杖，您必須提出 2 的要求： 取得授權碼，並用它交換權杖。 

#### <a name="getting-authorization-code"></a>取得授權碼

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="getting-access-and-refresh-token"></a>取得存取和重新整理權杖

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web API](scenario-mobile-call-api.md)
