---
title: 呼叫 web Api （移至生產環境）的傳統型應用程式-Microsoft 身分識別平臺
description: 瞭解如何建立桌面應用程式來呼叫 web Api （移至生產環境）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a353b4577f8cfa9ba279ad2793e1a7ab8b27e55
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268328"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的桌面應用程式-移至生產環境

本文提供詳細的資訊，讓您進一步改善應用程式，並將其移至生產環境。

## <a name="handling-errors-in-desktop-applications"></a>處理桌面應用程式中的錯誤

在不同的流程中，您已瞭解如何處理無訊息流程的錯誤（如程式碼片段所示）。 您也會發現需要互動的情況（累加同意和條件式存取）。

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>如何讓使用者在數個資源中提前同意

> [!NOTE]
> 針對 Microsoft 身分識別平臺（但不適用於 Azure Active Directory （Azure AD） B2C，取得數項資源的同意。 Azure AD B2C 僅支援管理員同意，而非使用者同意。

Microsoft 身分識別平臺（v2.0）端點不允許您一次取得數個資源的權杖。 因此， `scopes`參數只能包含單一資源的範圍。 您可以使用`extraScopesToConsent`參數，確保使用者已預先同意至數個資源。

比方說，如果您有兩個資源，每個都有兩個範圍：

- `https://mytenant.onmicrosoft.com/customerapi`-具有2個`customer.read`範圍和`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-具有2個`vendor.read`範圍和`vendor.write`

您應該使用`.WithAdditionalPromptToConsent` `extraScopesToConsent`具有參數的修飾詞。

例如：

### <a name="in-msalnet"></a>在 MSAL.NET 中

```CSharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];
    
NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]
    
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

快速

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]
        
let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]
        
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

此呼叫會取得第一個 Web API 的存取權杖。

當您需要呼叫第二個 Web API 時，您可以`AcquireTokenSilent`呼叫 API：

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>每次執行應用程式時，Microsoft 個人帳戶都需要 reconsenting

若為 Microsoft 個人帳戶使用者，reprompting 在每個 native client （桌上型/行動裝置應用程式）呼叫上同意，以取得所需的行為。 Native client 身分識別原本就不安全（相反的是，與 Microsoft 身分識別平臺交換秘密以證明其身分識別的機密用戶端應用程式）。 Microsoft 身分識別平臺會在每次應用程式獲得授權時，藉由提示使用者同意，選擇減輕此安全的取用者服務。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
