---
title: 使用 Microsoft Authentication Library for.NET-Azure 中清除權杖快取
description: 了解如何清除權杖快取使用 Microsoft Authentication Library for.NET (MSAL.NET)。
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1604d2833387b105fc7897a89f96ebcf9486d6a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468465"
---
# <a name="clear-the-token-cache-using-msalnet"></a>清除權杖快取使用 MSAL.NET

當您[取得存取權杖](msal-acquire-cache-tokens.md)使用 Microsoft Authentication Library for.NET (MSAL.NET)，會快取權杖。 當應用程式需要權杖時，它應該先呼叫`AcquireTokenSilent`方法，以確認是否可接受的權杖快取中。 

清除快取之後，即可移除快取中的帳戶。 但這不會移除瀏覽器中的工作階段 Cookie。  下列範例會具現化公用用戶端應用程式、 取得帳戶，讓應用程式，並移除帳戶。

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

若要進一步了解取得和快取權杖，請閱讀[取得存取權杖](msal-acquire-cache-tokens.md)。
