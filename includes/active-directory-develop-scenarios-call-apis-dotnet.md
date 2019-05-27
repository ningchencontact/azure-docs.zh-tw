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
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121865"
---
### <a name="authenticationresult-properties-in-msalnet"></a>在 MSAL.NET AuthenticationResult 屬性

若要取得權杖方法會傳回`AuthenticationResult`(或者，若為非同步方法， `Task<AuthenticationResult>`。

在 MSAL.NET，`AuthenticationResult`公開 （expose):

- `AccessToken` Web api 來存取資源。 這個參數是字串，通常是以 base64 編碼的 JWT，但用戶端應該永遠不會查看內部存取權杖。 此格式不保證會維持穩定，並可針對資源將其加密。 根據用戶端上的存取權杖內容來撰寫程式碼的人員，是發生錯誤和用戶端邏輯破碎的最大來源之一。 另請參閱[存取權杖](../articles/active-directory/develop/access-tokens.md)
- `IdToken` （這個參數是編碼的 JWT） 的使用者。 請參閱[識別碼權杖](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` 會告知權杖的到期日期/時間
- `TenantId` 包含在其中發現使用者的租用戶。 來賓使用者 （Azure AD B2B 案例），租用戶識別碼會是 「 來賓 」 租用戶沒有唯一的租用戶。
權杖會傳遞使用者，當`AuthenticationResult`也包含此使用者的相關資訊。 針對機密用戶端流程，其中將權杖要求 （適用於應用程式） 沒有使用者，此使用者資訊是 null。
- `Scopes`針對發出的權杖。
- 使用者的唯一識別碼。

### <a name="iaccount"></a>IAccount

MSAL.NET 定義帳戶的概念 (透過`IAccount`介面)。 這項重大變革提供了正確的語意：相同使用者在不同的 Azure AD 目錄中可有數個帳戶的這項事實。 也 MSAL.NET 提供更好的資訊，在來賓案例的情況下，因為提供的主帳戶資訊。
下圖顯示的結構`IAccount`介面：

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId`類別會識別特定的租用戶中的帳戶。 它具有下列屬性：

| 屬性 | 說明 |
|----------|-------------|
| `TenantId` | GUID，也就是帳戶所在的租用戶識別碼的字串表示。 |
| `ObjectId` | GUID，這是擁有租用戶中的帳戶的使用者識別碼的字串表示。 |
| `Identifier` | 帳戶的唯一識別碼。 `Identifier` 串連`ObjectId`和`TenantId`分隔逗號和是 base64 編碼。 |

`IAccount`介面代表單一帳戶的相關資訊。 相同的使用者可能會出現在不同的租用戶，也就是使用者可以擁有多個帳戶。 其成員都是：

| 屬性 | 說明 |
|----------|-------------|
| `Username` | 字串，包含可顯示的值，UserPrincipalName (UPN) 格式，例如john.doe@contoso.com。 而 HomeAccountId 和 HomeAccountId.Identifier 不可以是 null，這個字串可以是 null。 這個屬性會取代`DisplayableId`屬性`IUser`MSAL.NET 舊版本中。 |
| `Environment` | 字串，包含身分識別提供者，此帳戶，例如`login.microsoftonline.com`。 這個屬性會取代`IdentityProvider`的屬性`IUser`，差異在於`IdentityProvider`也有租用戶 （除了雲端環境中） 的相關資訊，而這裡的值是主應用程式。 |
| `HomeAccountId` | AccountId 為使用者的主帳戶。 此屬性唯一識別使用者在 Azure AD 租用戶。 |

### <a name="using-the-token-to-call-a-protected-api"></a>使用權杖來呼叫受保護的 API

一次`AuthenticationResult`MSAL 傳回 (在`result`)，您需要將它新增到 HTTP 授權標頭，再進行呼叫，以存取受保護的 Web API。

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```