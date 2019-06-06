---
title: 利用 OpenID Connect 的 Web 登入 - Azure Active Directory B2C | Microsoft Docs
description: 建立 Azure Active Directory B2C 中使用的 OpenID Connect 驗證通訊協定的 web 應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 85639e2648131f9475ad2ae77f31d43e64bf82e7
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509211"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中利用 OpenID Connect 的 Web 登入

OpenID Connect 是建置在 OAuth 2.0 之上的驗證通訊協定，可用來將使用者安全地登入 Web 應用程式。 藉由使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 實作，您就能將 Web 應用程式中的註冊、登入及其他識別管理體驗外包給 Azure Active Directory (Azure AD)。 本指南會以與語言無關的方式示範如何執行此動作。 而是在說明如何傳送和接收 HTTP 訊息，但不使用我們的任何開放原始碼程式庫。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 擴充 OAuth 2.0 的*授權*通訊協定來做為*驗證*通訊協定。 這個驗證通訊協定可讓您執行單一登入。 它介紹的概念*識別碼權杖*，可讓用戶端驗證使用者的身分識別，並取得使用者的基本設定檔資訊。

由於它會擴充 OAuth 2.0，它也可讓應用程式安全地取得*存取權杖*。 您可以使用存取權杖，來存取受到[授權伺服器](active-directory-b2c-reference-protocols.md)保護的資源。 OpenID Connect 建議您在建置 web 應用程式裝載於伺服器且透過瀏覽器存取。 如果您想要新增至您的行動裝置的身分識別管理，或使用 Azure AD B2C 的桌面應用程式，您應該使用[OAuth 2.0](active-directory-b2c-reference-oauth-code.md)而不是 OpenID Connect。 如需有關權杖的詳細資訊，請參閱[的語彙基元中 Azure Active Directory B2C 概觀](active-directory-b2c-reference-tokens.md)

Azure AD B2C 擴充標準的 OpenID Connect 通訊協定，功能更強大，而不僅止於簡單的驗證和授權。 它引進[使用者流程參數](active-directory-b2c-reference-policies.md)，可讓您利用 OpenID Connect 來將使用者新增更多體驗您的應用程式，例如註冊、 登入和設定檔管理。

## <a name="send-authentication-requests"></a>傳送驗證要求

當您的 web 應用程式需要驗證使用者，並執行使用者流程時，它可以將使用者導向至`/authorize`端點。 使用者會根據使用者流程的動作。

在此要求中，用戶端會指出它需要向使用者索取的權限`scope`參數與執行的使用者流程`p`參數。 後續小節中提供三個範例 (含有換行符號以提高可讀性)，各使用不同的使用者流程。 為了瞭解每個要求的運作方式，請試著將要求貼到瀏覽器來執行。 您可以取代`fabrikamb2c`與您租用戶，如果您有一個，而且已建立使用者流程的名稱。

#### <a name="use-a-sign-in-user-flow"></a>使用登入使用者流程
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>使用註冊使用者流程
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>使用編輯設定檔使用者流程
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| client_id | 是 | 應用程式識別碼[Azure 入口網站](https://portal.azure.com/)指派給您的應用程式。 |
| response_type | 是 | 必須包含 OpenID Connect 的識別碼權杖。 如果您的 web 應用程式也會需要權杖來呼叫 web API，您可以使用`code+id_token`。 |
| redirect_uri | 否 | `redirect_uri`應用程式，可以傳送及接收您的應用程式驗證回應的參數。 其必須完全符合其中一個`redirect_uri`註冊，讓您在 Azure 入口網站中，不同之處在於它必須是 URL 編碼的參數。 |
| scope | 是 | 範圍的空格分隔清單。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 `offline_access`範圍都是選擇性的 web 應用程式。 它表示您的應用程式將需要*重新整理權杖*擴充存取資源。 |
| response_mode | 否 | 您的應用程式，傳回用來傳送產生的授權碼的方法。 它可以是 `query`、`form_post` 或 `fragment`。  如需最佳安全性，建議使用 `form_post` 回應模式。 |
| state | 否 | 也會在權杖回應中傳回的要求中包含的值。 它可以是您所想要內容中的字串。 隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。 狀態也用於應用程式中的使用者的狀態資訊編碼之前發生的驗證要求，例如他們先前所在的網頁。 |
| nonce | 是 | 包含在產生的識別碼權杖，以宣告形式的要求 （由應用程式產生） 中包含的值。 應用程式接著可以確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| p | 是 | 使用者流程會執行。 這是在 Azure AD B2C 租用戶中建立使用者流程的名稱。 使用者流程的名稱應該以開頭`b2c\_1\_`。 |
| prompt | 否 | 需要的使用者互動類型。 此時唯一有效的值是 `login`，可強制使用者針對該要求輸入其認證。 |

此時，會要求使用者完成此工作流程。 使用者可能必須輸入使用者名稱和密碼，註冊的社交身分識別或登入的目錄。 可能有其他任何數目的步驟，根據使用者流程的定義方式。

您的應用程式，在指定的使用者完成使用者流程之後，會傳回回應`redirect_uri`參數，所使用的方法，指定在`response_mode`參數。 回應是針對每個上述所有情況下，獨立於使用者流程相同。

使用 `response_mode=fragment` 的成功回應如下所示：

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --------- | ----------- |
| id_token | 應用程式要求的識別碼權杖。 您可以使用識別碼權杖來確認使用者的身分識別，然後開始與使用者的工作階段。 |
| code | 應用程式要求，如果您使用的授權碼`response_type=code+id_token`。 應用程式可以使用授權碼要求目標資源的存取權杖。 授權碼通常會約 10 分鐘後過期。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該確認`state`要求和回應中的值完全相同。 |

錯誤回應也可以傳送到`redirect_uri`參數以便讓應用程式能夠適當地處理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --------- | ----------- |
| error | 可用來分類發生的錯誤類型的程式碼。 |
| error_description | 可協助您識別驗證錯誤的根本原因的特定錯誤訊息。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該確認`state`要求和回應中的值完全相同。 |

## <a name="validate-the-id-token"></a>驗證識別碼權杖

只收到識別碼權杖並不足以驗證使用者。 驗證識別碼權杖的簽章，並確認語彙基元，根據您的應用程式需求中的宣告。 Azure AD B2C 使用 [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密編譯來簽署權杖及驗證其是否有效。 視您偏好的語言而定，有許多針對驗證 JWT 的開放原始碼程式庫可用。 我們建議您探索這些程式庫，而不是實作您自己的驗證邏輯。 

Azure AD B2C 具有 OpenID Connect 中繼資料端點，可讓應用程式以取得 Azure AD B2C 會在執行階段資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 B2C 租用戶中的每個使用者流程都有一份 JSON 中繼資料文件。 例如，`fabrikamb2c.onmicrosoft.com` 中適用於 `b2c_1_sign_in` 使用者流程的中繼資料文件位於：

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此設定文件的屬性之一是 `jwks_uri`，就上述使用者流程而言，它的值是：

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in` 。

若要判斷哪些使用者流程已用於簽署識別碼權杖 （及從何處取得中繼資料），會有兩個選項。 首先，使用者流程名稱包含於識別碼權杖的 `acr` 宣告中。 另一個選項是當您發出要求時在 `state` 參數的值中將使用者流程編碼，然後將它解碼以判斷使用了哪個使用者流程。 任一種方法都有效。

取得從 OpenID Connect 中繼資料端點的中繼資料文件之後，您可以使用 RSA 256 公開金鑰來驗證識別碼權杖的簽章。 可能有多個列於此端點的索引鍵，每個識別`kid`宣告。 識別碼權杖的標頭也會包含 `kid` 宣告，指出簽署該識別碼權杖所使用的金鑰。

當您驗證識別碼權杖的簽章之後，必須驗證數個宣告。 例如：

- 驗證 `nonce` 宣告，以防止權杖重新執行攻擊。 其值應該是您在登入要求中所指定的內容。
- 驗證`aud`宣告，以確保識別碼權杖已簽發應用程式。 其值應該是您的應用程式的應用程式識別碼。
- 驗證`iat`和`exp`宣告，以確保識別碼權杖尚未過期。

另外還有數個您應該執行的驗證。 驗證會詳述[OpenID Connect 核心規格](https://openid.net/specs/openid-connect-core-1_0.html)。視您的案例而定，您可能也會想要驗證其他宣告。 一些常見的驗證包括：

- 確保使用者/組織已註冊的應用程式。
- 確保使用者有適當的授權/權限。
- 確保驗證方式有一定的強度，例如 Azure Multi-Factor Authentication。

驗證識別碼權杖之後，您可以開始與使用者的工作階段。 您可以使用識別碼權杖中的宣告，以在您的應用程式中取得使用者的相關資訊。 這項資訊的用途包括顯示、記錄和授權。

## <a name="get-a-token"></a>取得權杖

如果您需要您的 web 應用程式只能執行 使用者流程，您可以略過接下來的章節。 下列各節是僅適用於 web 應用程式，需要進行已驗證的 web API 的呼叫，並同時受 Azure AD B2C。

您可以藉由將 `POST` 要求傳送至 `/token` 端點，將取得 (使用 `response_type=code+id_token`) 的權杖授權碼兌換成所需的資源。 目前，您可以要求權杖的唯一資源是您的應用程式本身的後端 web API。 來為您自己要求權杖的慣例是使用您的應用程式用戶端識別碼作為範圍：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| p | 是 | 用來取得授權碼的使用者流程。 您無法在此要求中使用不同的使用者流程。 在查詢字串，而不是 POST 主體，請新增此參數。 |
| client_id | 是 | 應用程式識別碼[Azure 入口網站](https://portal.azure.com/)指派給您的應用程式。 |
| grant_type | 是 | 授與的類型，針對授權碼流程來說，必須是 `authorization_code` 。 |
| scope | 否 | 範圍的空格分隔清單。 `openid` 範圍指示使用 id_tokens 參數形式的權限，以登入使用者及取得使用者相關資料。 它可用來取得您應用程式本身的後端 web API，其由相同的應用程式識別碼與用戶端的權杖。 `offline_access`範圍表示您的應用程式需要重新整理權杖，以擴充存取資源。 |
| code | 是 | 您在使用者流程的開頭取得授權碼。 |
| redirect_uri | 是 | 應用程式的 `redirect_uri` 參數，您會在此處收到授權碼。 |
| client_secret | 是 | 在所產生的應用程式密碼[Azure 入口網站](https://portal.azure.com/)。 這個應用程式密碼是重要的安全性構件， 您應該要用安全的方法把它儲存在您的伺服器上。 變更定期執行此用戶端祕密。 |

成功的權杖回應看起來如下：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 參數 | 描述 |
| --------- | ----------- |
| not_before | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type | 權杖類型值。 `Bearer` 是唯一支援的類型。 |
| access_token | 您所要求已簽署的 JWT 權杖。 |
| scope | 權杖有效的範圍。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用此權杖來取得其他權杖，在目前的權杖過期之後。 重新整理權杖可用來保留資源存取權很長的時間。 範圍`offline_access`必須已經用於授權和權杖要求以便接收重新整理權杖。 |

錯誤回應看起來如下：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| error | 可用來分類發生的錯誤類型的程式碼。 |
| error_description | 可協助您識別驗證錯誤的根本原因的訊息。 |

## <a name="use-the-token"></a>使用權杖

現在您已成功取得存取權杖，因此可在對後端 Web API 發出的要求中使用該權杖，方法是在 `Authorization` 標頭中加入該權杖：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>重新整理權杖

識別碼權杖會在短時間內過期。 若要繼續存取資源到期後，請重新整理權杖。 您可以藉由送出另一個重新整理權杖`POST`要求`/token`端點。 此時，請提供 `refresh_token` 參數，而不是 `code` 參數：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| p | 是 | 用來取得原始重新整理權杖的使用者流程。 您無法在此要求中使用不同的使用者流程。 在查詢字串，而不是 POST 主體，請新增此參數。 |
| client_id | 是 | 應用程式識別碼[Azure 入口網站](https://portal.azure.com/)指派給您的應用程式。 |
| grant_type | 是 | 授與，必須重新整理權杖的授權碼流程此組件的類型。 |
| scope | 否 | 範圍的空格分隔清單。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 它可用來將權杖傳送到應用程式本身的後端 web API 時，會以相同的應用程式識別碼與用戶端來表示。 `offline_access`範圍表示您的應用程式需要重新整理權杖，以擴充存取資源。 |
| redirect_uri | 否 | 應用程式的 `redirect_uri` 參數，您會在此處收到授權碼。 |
| refresh_token | 是 | 原始重新整理權杖取得流程的第二個部分。 `offline_access`必須同時在授權和權杖要求中使用範圍，以便接收重新整理權杖。 |
| client_secret | 是 | 在所產生的應用程式密碼[Azure 入口網站](https://portal.azure.com/)。 這個應用程式密碼是重要的安全性構件， 您應該要用安全的方法把它儲存在您的伺服器上。 變更定期執行此用戶端祕密。 |

成功的權杖回應看起來如下：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 參數 | 描述 |
| --------- | ----------- |
| not_before | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type | 權杖類型值。 `Bearer` 是唯一支援的類型。 |
| access_token | 要求已簽署的 JWT 權杖。 |
| scope | 權杖的有效範圍。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用此權杖來取得其他權杖，在目前的權杖過期之後。 重新整理權杖可用來保留資源存取權很長的時間。 |

錯誤回應看起來如下：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| error | 可用來分類發生的錯誤類型的程式碼。 |
| error_description | 可協助您識別驗證錯誤的根本原因的訊息。 |

## <a name="send-a-sign-out-request"></a>傳送登出要求

當您想要將使用者登出應用程式時，它並無法清除應用程式的 cookie 或否則結束使用者工作階段。 將使用者重新導向至 Azure AD B2C 來登出。如果您無法這樣做，使用者可能可以重新驗證您的應用程式，不需要再次輸入其認證。

您可以直接將使用者重新導向至`end_session`稍早所述的 OpenID Connect 中繼資料文件中列出的端點：

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| p | 是 | 您想要用來將使用者登出應用程式的使用者流程。 |
| post_logout_redirect_uri | 否 | 使用者應該重新導向至在成功登出之後的 URL。如果它不包含在內，Azure AD B2C 會向使用者顯示一般訊息。 |

將使用者導向`end_session`端點會清除的部分使用者的單一登入狀態與 Azure AD B2C，但它不會登入將使用者登出其社交身分識別提供者 (IDP) 工作階段。 如果使用者在後續登入時選取相同的 IDP，它們會重新驗證，而不需要輸入其認證。 如果使用者想要登出應用程式時，它不一定表示他們想要登出自己的 Facebook 帳戶。 不過，如果使用本機帳戶，使用者的工作階段結束正確。

