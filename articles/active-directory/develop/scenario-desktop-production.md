---
title: 呼叫 web Api （移至生產環境）-Microsoft 身分識別平台的桌面應用程式
description: 了解如何建置傳統型應用程式呼叫 web Api （移至生產環境）
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
ms.openlocfilehash: 3ca66a41f26c54bf04273682d14889a36b688c70
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075126"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>桌面應用程式以呼叫 web Api-移至生產環境

本文章提供詳細資料，以改善您的應用程式中更進一步，並將它移至生產環境。

## <a name="handling-errors-in-desktop-applications"></a>在桌面應用程式中處理錯誤

在不同的流程中，您已了解如何處理無訊息流程的錯誤 （如程式碼片段所示）。 您也了解，有些情況下就互動所需的 （「 增量同意 」 和 「 條件式存取 」）。

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>如何讓使用者的同意，預先幾個資源

> [!NOTE]
> 取得數個資源適用於 Microsoft 身分識別平台，但不適用於 Azure Active Directory (Azure AD) B2C 中的同意。 Azure AD B2C 支援唯一的系統管理員同意，沒有使用者同意。

Microsoft 身分識別平台 (v2.0) 端點不允許您一次取得多項資源的權杖。 因此，`scopes`參數只能包含單一資源的範圍。 您可以確保，在使用者預先同意幾個資源使用`extraScopesToConsent`參數。

比方說，如果您有兩個資源，其中有兩個範圍各：

- `https://mytenant.onmicrosoft.com/customerapi` -2 的範圍`customer.read`和 `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -2 的範圍`vendor.read`和 `vendor.write`

您應該使用`.WithAdditionalPromptToConsent`修飾詞具有`extraScopesToConsent`參數。

例如：

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

這個呼叫會取得第一個 web API 的存取權杖。

當您需要呼叫第二個 web API 時，您可以呼叫：

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Microsoft 個人帳戶需要 reconsenting 每次執行應用程式

對於 Microsoft 個人帳戶的使用者，同意授權每個原生用戶端 （desktop/行動裝置應用程式） 呼叫 reprompting 是預期的行為。 原生用戶端身分識別是原本就是不安全 （相對於 exchange 的 Microsoft 身分識別平台，可證明其身分識別的祕密機密用戶端應用程式）。 Microsoft 身分識別平台選擇用來提示使用者同意，每個應用程式已獲授權的時間來降低這個普遍的取用者服務。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
