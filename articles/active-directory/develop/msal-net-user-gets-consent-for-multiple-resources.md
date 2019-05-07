---
title: 取得同意幾項資源 (Microsoft Authentication Library for.NET) |Azure
description: 了解使用者如何取得預先同意使用 Microsoft Authentication Library for.NET (MSAL.NET) 的幾項資源。
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45bad9a03e8eff6f22ebb99fd2ef4bcd5fecf9b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158816"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>使用者取得同意使用 MSAL.NET 的幾項資源
Microsoft 身分識別平台端點不允許您一次取得多項資源的權杖。 當使用 Microsoft Authentication Library for.NET (MSAL.NET)，在取得權杖方法的範圍參數應該只包含單一資源的範圍。 不過，您可以預先同意預付數個資源指定使用的其他範圍`.WithExtraScopeToConsent`產生器方法。

> [!NOTE]
> 取得數個資源適用於 Microsoft 身分識別平台，但不適用於 Azure AD B2C 中的同意。 Azure AD B2C 支援唯一的系統管理員同意，沒有使用者同意。

例如，如果您有兩個資源具有 2 範圍每個：

- https://mytenant.onmicrosoft.com/customerapi (2 個領域`customer.read`和`customer.write`)
- https://mytenant.onmicrosoft.com/vendorapi (2 個領域`vendor.read`和`vendor.write`)

您應該使用`.WithExtraScopeToConsent`修飾詞具有*extraScopesToConsent*參數，在下列範例所示：

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

這可讓您存取權杖的第一個 web API。 然後，當您需要存取第二個 web API 時您可以以無訊息模式取得權杖的快取的權杖：

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
