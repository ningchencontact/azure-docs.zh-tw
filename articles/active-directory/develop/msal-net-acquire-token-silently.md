---
title: 以無訊息模式取得權杖 (Microsoft Authentication Library for.NET) |Azure
description: 了解如何取得存取權杖以無訊息模式 （從權杖的快取） 使用 Microsoft Authentication Library for.NET (MSAL.NET)。
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544243"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>從權杖快取使用 MSAL.NET 取得權杖

當您取得使用 Microsoft Authentication Library for.NET (MSAL.NET) 的存取權杖時，會快取權杖。 當應用程式需要權杖時，它應該先呼叫`AcquireTokenSilent`方法，以確認是否可接受的權杖快取中。 在許多情況下，就可以取得另一個權杖快取中的權杖為基礎的更多範圍。 它也可重新整理權杖，當它即將屆滿 （因為權杖快取也會包含重新整理權杖）。

建議的模式是呼叫`AcquireTokenSilent`方法第一次。  如果`AcquireTokenSilent`失敗，然後取得權杖，使用其他方法。

在下列範例中，應用程式會先嘗試從權杖快取權杖。  如果`MsalUiRequiredException`擲回例外狀況、 應用程式取得權杖以互動方式。 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```