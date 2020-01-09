---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423740"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET 中的 AuthenticationResult 屬性

取得權杖的方法會傳回 `AuthenticationResult` （或是非同步方法的 `Task<AuthenticationResult>`。

在 MSAL.NET 中，`AuthenticationResult` 會公開：

- `AccessToken` Web API 來存取資源。 這個參數是一個字串，通常是 base64 編碼的 JWT，但用戶端不應該在存取權杖內查看。 此格式不保證會維持穩定，並可針對資源將其加密。 根據用戶端上的存取權杖內容來撰寫程式碼的人員，是發生錯誤和用戶端邏輯破碎的最大來源之一。 另請參閱[存取權杖](../articles/active-directory/develop/access-tokens.md)
- 使用者的 `IdToken` （此參數是已編碼的 JWT）。 請參閱[識別碼權杖](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` 告知權杖到期的日期/時間
- `TenantId` 包含在其中找到使用者的租使用者。 對於來賓使用者（Azure AD B2B 案例），租使用者識別碼是來賓租使用者，而不是唯一的租使用者。
為使用者傳遞權杖時，`AuthenticationResult` 也會包含此使用者的相關資訊。 對於要求不含使用者（針對應用程式）之權杖的機密用戶端流程，此使用者資訊為 null。
- 發出權杖的 `Scopes`。
- 使用者的唯一識別碼。

### <a name="iaccount"></a>IAccount

MSAL.NET 會定義帳戶的概念（透過 `IAccount` 介面）。 這項重大變革提供了正確的語意：相同使用者在不同的 Azure AD 目錄中可有數個帳戶的這項事實。 此外，MSAL.NET 會在來賓案例的情況下提供更好的資訊，因為提供了主帳戶資訊。
下圖顯示 `IAccount` 介面的結構：

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 類別可識別特定租使用者中的帳戶。 它具有下列屬性：

| 屬性 | 說明 |
|----------|-------------|
| `TenantId` | GUID 的字串表示，這是帳戶所在租使用者的識別碼。 |
| `ObjectId` | GUID 的字串表示，也就是擁有租使用者帳戶的使用者識別碼。 |
| `Identifier` | 帳戶的唯一識別碼。 `Identifier` 是以逗號分隔的 `ObjectId` 和 `TenantId` 的串連，而且不是以 base64 編碼。 |

`IAccount` 介面代表單一帳戶的相關資訊。 相同的使用者可以出現在不同的租使用者中，也就是說，使用者可以有多個帳戶。 其成員如下：

| 屬性 | 說明 |
|----------|-------------|
| `Username` | 包含 UserPrincipalName （UPN）格式之可顯示值的字串，例如 john.doe@contoso.com。 這個字串可以是 null，而 HomeAccountId 和 HomeAccountId 則不會是 null。 這個屬性會取代舊版 MSAL.NET 中 `IUser` 的 `DisplayableId` 屬性。 |
| `Environment` | 字串，包含此帳戶的識別提供者，例如 `login.microsoftonline.com`。 這個屬性會取代 `IUser`的 `IdentityProvider` 屬性，不同之處在于 `IdentityProvider` 也有租使用者的相關資訊（除了雲端環境以外），而這裡的值只是主機。 |
| `HomeAccountId` | 使用者之主帳戶的 AccountId。 此屬性可唯一識別 Azure AD 租使用者之間的使用者。 |

### <a name="using-the-token-to-call-a-protected-api"></a>使用權杖來呼叫受保護的 API

一旦 MSAL （在 `result`中）傳回 `AuthenticationResult` 之後，您必須將它新增至 HTTP 授權標頭，才能進行呼叫以存取受保護的 Web API。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```