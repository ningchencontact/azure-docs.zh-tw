---
title: 權杖-Azure Active Directory B2C 概觀 |Microsoft Docs
description: 深入了解 Azure Active Directory B2C 中使用 token。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680259"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的語彙基元的概觀

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD) B2C 會在處理每個發出數種安全性權杖[驗證流程](active-directory-b2c-apps.md)。 本文件說明每種權杖的格式、安全性特性和內容。

## <a name="token-types"></a>權杖類型

Azure AD B2C 支援[OAuth 2.0 和 OpenID Connect 通訊協定](active-directory-b2c-reference-protocols.md)，其使用的權杖進行驗證和安全資源的存取權。 使用 Azure AD B2C 中的所有 token 都需[JSON web 權杖 (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)包含判斷提示的資訊持有者及權杖的主體。

使用 Azure AD B2C 通訊中使用下列語彙基元：

- *識別碼權杖*-JWT，其中包含宣告，您可用來識別您的應用程式中的使用者。 HTTP 要求相同的應用程式或服務的兩個元件之間的通訊安全地傳送此權杖。 您可以依需要在 ID 權杖中使用宣告。 它們通常用來顯示帳戶資訊或要在應用程式中的存取控制決定。 識別碼權杖已簽署，但不是加密。 當您的應用程式或 API 收到 ID 權杖時，它必須驗證簽章，以證明權杖的真實性。 您的應用程式或 API 也必須驗證權杖中的幾個宣告，以證明它有效。 根據案例需求，應用程式所驗證的宣告可以改變，但您的應用程式必須在每個案例中執行一些常見的宣告驗證。
- *存取權杖*-JWT，其中包含宣告，您可用來識別您的 Api 來授與的權限。 存取權杖已簽署，但不加密。 存取權杖用來提供伺服器 Api 和資源的存取。  當您的 API 收到存取權杖時，它必須驗證簽章，以證明權杖的真實性。 您的 API 也必須驗證權杖中的幾個宣告，以證明它有效。 根據案例需求，應用程式所驗證的宣告可以改變，但您的應用程式必須在每個案例中執行一些常見的宣告驗證。
- *重新整理權杖*-重新整理權杖用來取得新的 ID 權杖和存取權杖，在 OAuth 2.0 流程中的。 它們提供您的應用程式長期存取資源代表的使用者，而不需要與使用者互動。 重新整理權杖是您的應用程式的不透明的。 它們由 Azure AD B2C 所發出並可以檢查及解譯只能由 Azure AD B2C。 它們屬於長效權杖，但預期的重新整理權杖將持續一段特定時間的情況下，不應該撰寫您的應用程式。 重新整理權杖可能會因為各種原因而隨時失效。 您的應用程式知道重新整理權杖是否有效的唯一方式就是以嘗試兌換藉由 Azure AD B2C 的權杖要求。 當您兌換重新整理權杖做為新權杖時，會在權杖回應中收到新的重新整理權杖。 儲存新的重新整理權杖。 它會取代您先前使用中要求的重新整理權杖。 這個動作有助於確保您的重新整理權杖保持有效盡可能長時間。 

## <a name="endpoints"></a>端點

A[註冊應用程式](tutorial-register-applications.md)接收權杖，並會將要求傳送至這些端點通訊協定與 Azure AD B2C:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

您的應用程式接收來自 Azure AD B2C 的安全性權杖可以來自`/authorize`或`/token`端點。 從取得 ID 權杖時`/authorize`端點，它們已經完成使用[隱含流程](active-directory-b2c-reference-spa.md)，這通常用來登入以 javascript 為基礎的 web 應用程式的使用者。 從 `/token` 端點取得 ID 權杖時，它們已經使用[機密程式碼流程](active-directory-b2c-reference-oidc.md)來完成，這會使權杖保持規避瀏覽器。

## <a name="claims"></a>Claims

當您使用 Azure AD B2C 時，您可以精確控制權杖的內容。 您可以設定[使用者流程](active-directory-b2c-reference-policies.md)並[自訂原則](active-directory-b2c-overview-custom.md)傳送所需的應用程式的宣告中的特定資料集的使用者。 這些宣告可包含標準的屬性，例如**displayName**並**emailAddress**。 您的應用程式可以使用這些宣告來安全地驗證使用者和要求。 

識別碼權杖中的宣告不會傳回任何特定順序。 隨時可以在識別碼權杖中導入新的宣告。 引入新的宣告，則不應中斷您的應用程式。 您也可以包含[自訂使用者屬性](active-directory-b2c-reference-custom-attr.md)您宣告中。

下表列出您可以預期在 ID 權杖和存取 Azure AD B2C 所簽發的權杖的宣告。

| 名稱 | 宣告 | 範例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 對象 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 識別權杖的預定接收者。 Azure AD B2C 中，對象是應用程式識別碼。 您的應用程式應該驗證此值，並拒絕權杖，如果它不符合。 對象是資源的同義詞。 |
| 簽發者 | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 識別負責建構並傳回權杖的 Security Token Service (STS)。 它也會識別在其中驗證使用者的目錄。 您的應用程式應該驗證簽發者宣告，以確保權杖來自適當的端點。 |
| 發出時間 | `iat` | `1438535543` | 簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。 |
| 到期時間 | `exp` | `1438539443` | 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。 您的應用程式應該使用此宣告來驗證權杖的存留期的有效性。 |
| 生效時間 | `nbf` | `1438535543` | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 此時間通常是簽發權杖的時間相同。 您的應用程式應該使用此宣告來驗證權杖的存留期的有效性。 |
| 版本 | `ver` | `1.0` | 識別碼權杖，Azure AD B2C 所定義的版本。 |
| 代碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | ID 權杖與 OAuth 2.0 授權碼一起簽發權杖時，才包含代碼雜湊。 代碼雜湊可用來驗證授權碼的真實性。 如需如何執行這項驗證的詳細資訊，請參閱[OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。  |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | ID 權杖與 OAuth 2.0 存取權杖一起簽發權杖時，才包含存取權杖雜湊。 存取權杖雜湊可用來驗證存取權杖的真實性。 如需如何執行這項驗證的詳細資訊，請參閱[OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce 是用來緩和權杖重新執行攻擊的策略。 您的應用程式可以使用授權要求中指定 nonce`nonce`查詢參數。 您在要求中提供的值，就會發出在未經修改`nonce`只有識別碼權杖的宣告。 這個宣告可讓您的應用程式根據在要求上指定的值驗證此值。 您的應用程式應該執行這項驗證識別碼權杖驗證程序期間。 |
| 主體 | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | 權杖判斷提示的相關資訊，例如應用程式的使用者主體。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查，例如當權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。 |
| 驗證內容類別參考 | `acr` | 不適用 | 只搭配使用較舊的原則。 |
| 信任架構原則 | `tfp` | `b2c_1_signupsignin1` | 用來取得識別碼權杖的原則名稱。 |
| 驗證期間 | `auth_time` | `1438535543` | 時間的使用者上次輸入認證，以新紀元時間表示。 |
| 影響範圍 | `scp` | `Read`| 權限，授與存取權杖的資源。 以空格分隔多個授與的權限。 |
| 授權的合作對象 | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | 起始要求之用戶端應用程式的應用程式識別碼。 |

## <a name="configuration"></a>組態

下列屬性會用來[管理的安全性權杖的存留期](configure-tokens.md)Azure AD B2C 所發出：

- **存取和識別碼權杖存留期 (分鐘)** - 用來存取受保護資源之 OAuth 2.0 持有人權杖的存留期。 預設值為 60 分鐘。 （含） 之間的最小值為 5 分鐘。 （含） 之間的最大值為 1440 分鐘。

- **重新整理權杖存留期 （天）** -之前重新整理權杖可用來取得新的存取或識別碼權杖的最大的時間週期。 時間週期也涵蓋了取得新的重新整理權杖，如果您的應用程式已被授與`offline_access`範圍。 預設值為 14 天。 最小值 （含） 是一天。 （含） 之間的最大值為 90 天。

- **重新整理權杖滑動視窗存留期 （天）** -之後這次經過這段期間強制使用者重新驗證，無論有效期間的最新重新整理應用程式取得的權杖。 唯有將參數設定為 [受限制] 時才能提供。 它必須大於或等於 [重新整理權杖存留期 (天)]  值。 如果將參數設定為 [無限制] ，您便無法提供特定的值。 預設為 90 天。 最小值 （含） 是一天。 （含） 之間的最大值為 365 天。

使用這些屬性可啟用以下使用案例：

- 只要使用者持續使用行動應用程式，便允許他們無限期地維持已登入應用程式的狀態。 您可以在登入使用者流程中將 [重新整理權杖滑動視窗存留期 (天)] 設定為 [無限制]。
- 請設定適當的存取權杖存留期，以滿足業界的安全性和循規規範。

這些設定不適用於密碼重設使用者流程。 

## <a name="compatibility"></a>相容性

下列屬性會用來[管理權杖的相容性](configure-tokens.md):

- **簽發者 (iss) 宣告** - 此屬性可識別發出權杖的 Azure AD B2C 租用戶。 預設值為 `https://<domain>/{B2C tenant GUID}/v2.0/`。 值`https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`包含 Azure AD B2C 租用戶和使用者流程權杖要求中所使用的識別碼。 如果您的應用程式庫都必須符合 Azure AD B2C [OpenID Connect Discovery 1.0 規格](https://openid.net/specs/openid-connect-discovery-1_0.html)，使用此值。

- **主體 (子) 宣告** - 此屬性可識別權杖判斷提示資訊的實體。 預設值是**ObjectID**，其中會填入`sub`與使用者的物件識別碼權杖中宣告。 值**不支援**僅供回溯相容性。 建議您改用**ObjectID** ，您就可以。

- **宣告代表原則識別碼**-這個屬性會識別在其中填入權杖要求中使用的原則名稱的宣告型別。 預設值為 `tfp`。 值`acr`僅供回溯相容性。

## <a name="pass-through"></a>傳遞

使用者旅程圖啟動時，Azure AD B2C 會收到來自身分識別提供者的存取權杖。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您[啟用您的使用者流程中的宣告](idp-pass-through-user-flow.md)或[您的自訂原則中定義的宣告](idp-pass-through-custom.md)傳遞的權杖，透過您在 Azure AD B2C 中註冊的應用程式。 必須使用您的應用程式[v2 使用者流程](user-flow-versions.md)善用並傳遞做為宣告的權杖。

Azure AD B2C 目前僅支援傳遞存取權杖的 OAuth 2.0 識別提供者，包括 Facebook 和 Google。 對於所有其他識別提供者，宣告會傳回空白。 

## <a name="validation"></a>驗證

若要驗證權杖，您的應用程式應該檢查簽章和宣告的權杖。 許多開放原始碼程式庫可供驗證 Jwt，根據您慣用的語言。 建議您探索這些選項，而非實作您自己的驗證邏輯。

### <a name="validate-signature"></a>驗證簽章

JWT 包含三個區段中，*標頭*，則*主體*，和*簽章*。 簽章區段可用來驗證權杖的真實性，以便它可以信任的應用程式。 Azure AD B2C 權杖是經由業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。 

權杖標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

值**alg**宣告是用來簽署權杖的演算法。 值**kid**宣告是用來簽署權杖的公開金鑰。 在任何時候，Azure AD B2C 可以使用任何一組的公開-私密金鑰組來簽署權杖。 Azure AD B2C 會定期替換一組可能的索引鍵。 您的應用程式應撰寫成自動處理這些金鑰變更中。 檢查 Azure AD B2C 所用公開金鑰的更新的合理頻率為每 24 小時。

Azure AD B2C 具有 OpenID Connect 中繼資料端點。 使用此端點，應用程式可以要求 Azure AD B2C 會在執行階段資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 Azure AD B2C 租用戶包含每個原則的 JSON 中繼資料文件。 中繼資料文件是包含幾項實用資訊的 JSON 物件。 中繼資料包含**jwks_uri**，讓擁有的公用金鑰組的位置，用來簽署權杖。 位置在這裡，提供，但它的最佳動態擷取該位置，藉由使用中繼資料文件並剖析**jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
位於此 URL 的 JSON 文件包含特定時刻使用的所有公開金鑰資訊。 您的應用程式可以使用 JWT 標頭中的 `kid` 宣告，以選取 JSON 文件中用來簽署特定權杖的公開金鑰。 接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

中繼資料文件`B2C_1_signupsignin1`中的原則`contoso.onmicrosoft.com`租用戶位於：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

若要判斷哪個原則用來簽署權杖 （以及何處可移至要求的中繼資料），您有兩個選項。 首先，原則名稱包含在權杖的 `acr` 宣告中。 您可以將 JWT 主體進行 base 64 解碼，並將產生的 JSON 字串還原序列化，以剖析 JWT 主體中的宣告。 `acr`宣告是用來簽發權杖的原則名稱。 另一個選項是將原則中的值編碼`state`參數，當您發出要求，並再將它解碼以判斷使用了哪個原則。 任一種方法都有效。

描述如何執行簽章驗證已超出本文的範圍。 許多開放原始碼程式庫可協助您驗證權杖。

### <a name="validate-claims"></a>驗證宣告

當您的應用程式或 API 收到 ID 權杖時，它還應該執行數次檢查針對宣告識別碼權杖中。 應該檢查下列宣告：

- **對象**-驗證識別碼權杖預定要提供給您的應用程式。
- **生效**並**到期時間**-驗證識別碼權杖尚未過期。
- **簽發者**-確認權杖已簽發給您的應用程式，Azure AD b2c。
- **nonce** -權杖重新執行攻擊風險降低策略。

如需您的應用程式應執行之驗證的完整清單，請參閱[OpenID Connect 規格](https://openid.net)。  

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](active-directory-b2c-access-tokens.md)。

