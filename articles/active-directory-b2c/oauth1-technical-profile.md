---
title: 在 Azure Active Directory B2C 自訂原則中定義 OAuth1 技術設定檔 |Microsoft Docs
description: 在 Azure Active Directory B2C 自訂原則中定義 OAuth1 技術設定檔。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 993fc8b2e318b59775f61de391ac75fa765485f0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513125"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 自訂原則中定義 OAuth1 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C 可提供 [OAuth 1.0 通訊協定](https://tools.ietf.org/html/rfc5849)識別提供者的支援。 本文會說明技術設定檔的詳細規格，其可和支援此標準化通訊協定的宣告提供者互動。 OAuth1 的技術設定檔，您可以與 Twitter 等 OAuth1 型的識別提供者同盟。 同盟身分識別提供者，可讓使用者使用其現有的社交登入或企業身分識別。

## <a name="protocol"></a>Protocol

**Protocol** 元素的 **Name** 屬性必須設定為 `OAuth1`。 例如，**Twitter-OAUTH1** 技術設定檔的通訊協定是 `OAuth1`。

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>輸入宣告

**InputClaims** 和 **InputClaimsTransformations** 元素是空的或不存在。

## <a name="output-claims"></a>輸出宣告

**OutputClaims** 元素包含 OAuth1 識別提供者傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至識別提供者中定義的名稱。 只要設定 **DefaultValue** 屬性，您也可以包含識別提供者未傳回的宣告。

**OutputClaimsTransformations** 元素可能包含 **OutputClaimsTransformation** 的集合，用來修改輸出宣告或產生新的輸出宣告。

下列範例顯示 Twitter 識別提供者傳回的宣告：

- **User_id**宣告對應至**issuerUserId**宣告。
- 對應至 **displayName** 宣告的 **screen_name** 宣告。
- **email** 宣告沒有名稱對應。

技術設定檔也會傳回識別提供者未傳回的宣告： 

- 包含識別提供者名稱的 **identityProvider** 宣告。
- 具有預設值 `socialIdpAuthentication` 的 **authenticationSource** 宣告。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>中繼資料

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| client_id | 是 | 識別提供者的應用程式識別碼。 |
| ProviderName | 否 | 識別提供者的名稱。 |
| request_token_endpoint | 是 | 依據 RFC 5849 的要求權杖端點 URL。 |
| authorization_endpoint | 是 | 依據 RFC 5849 的授權端點 URL。 |
| access_token_endpoint | 是 | 依據 RFC 5849 的權杖端點 URL。 |
| ClaimsEndpoint | 否 | 使用者資訊端點的 URL。 | 
| ClaimsResponseFormat | 否 | 宣告回應格式。|

## <a name="cryptographic-keys"></a>密碼編譯金鑰

**CryptographicKeys** 元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| client_secret | 是 | 識別提供者應用程式的用戶端密碼。   | 

## <a name="redirect-uri"></a>重新導向 URI

當您設定識別提供者的重新導向 URL 時，請輸入 `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`。 請務必將 **tenant** 取代為您的租用戶名稱 (例如 contosob2c.onmicrosoft.com)，並將 **policyId** 取代為原則的識別碼 (例如 b2c_1a_policy)。 重新導向 URI 必須全部小寫。 新增使用身分識別提供者登入的所有原則的重新導向 URL。 

如果使用的是 **b2clogin.com** 網域，而非使用 **login.microsoftonline.com**，請務必使用 b2clogin.com，而非使用 login.microsoftonline.com。

範例：

- [使用自訂原則新增 Twitter 作為 OAuth1 識別提供者](active-directory-b2c-custom-setup-twitter-idp.md)













