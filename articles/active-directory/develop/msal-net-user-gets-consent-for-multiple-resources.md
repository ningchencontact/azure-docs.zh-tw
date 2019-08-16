---
title: 取得幾項資源的同意 (適用于 .NET 的 Microsoft 驗證程式庫) |Azure
description: 瞭解使用者如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET), 來預先同意數個資源。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ded7a6fc465b4cfc98d26f65195f89de8381ac6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532366"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>使用者使用 MSAL.NET 取得數個資源的同意
Microsoft 身分識別平臺端點不允許您一次取得數個資源的權杖。 使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 時, 取得權杖方法中的範圍參數應僅包含單一資源的範圍。 不過, 您可以使用`.WithExtraScopeToConsent`產生器方法來指定其他範圍, 預先同意數個資源。

> [!NOTE]
> 針對 Microsoft 身分識別平臺取得數項資源的同意, 但不適用於 Azure AD B2C。 Azure AD B2C 僅支援管理員同意, 而非使用者同意。

例如, 如果您有兩個資源, 每個都有2個範圍:

- HTTPs:\//mytenant.onmicrosoft.com/customerapi (具有2個`customer.read`範圍`customer.write`和)
- HTTPs:\//mytenant.onmicrosoft.com/vendorapi (具有2個`vendor.read`範圍`vendor.write`和)

您應該使用`.WithExtraScopeToConsent`具有*extraScopesToConsent*參數的修飾詞, 如下列範例所示:

```csharp
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

這會讓您取得第一個 Web API 的存取權杖。 然後, 當您需要存取第二個 Web API 您可以從權杖快取以無訊息方式取得權杖:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
