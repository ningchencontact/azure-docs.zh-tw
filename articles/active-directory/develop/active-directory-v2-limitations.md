---
title: Azure Active Directory v2.0 端點限制 | Microsoft Docs
description: Azure AD v2.0 端點的限制清單。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: edf0b52e5889fe8fa875de65fcaa8c2a22df1a7f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590744"
---
# <a name="should-i-use-the-v20-endpoint"></a>我應該使用 v2.0 端點嗎？

當您建立與 Azure Active Directory (Azure AD) 整合的應用程式時，您必須判斷 v2.0 端點和驗證通訊協定是否符合您的需求。 Azure AD 的原始端點仍受到完整的支援，而且在某些方面比 v2.0 的功能更豐富。 不過，v2.0 端點為開發人員[帶來極大的好處](azure-ad-endpoint-comparison.md)。

以下是我們在這個時間點針對開發人員所提供的簡化建議：

* 如果您必須在應用程式中支援個人 Microsoft 帳戶，請使用 v2.0 端點。 但在這樣做之前，請務必了解本文中討論的限制。
* 如果您的應用程式只需要支援 Microsoft 公司和學校帳戶，請不要使用 v2.0 端點。 請改為參考 [Azure AD 開發人員指南](azure-ad-developers-guide.md)。

v2.0 端點會進化到排除此處所列的限制，屆時您只需要使用 v2.0 端點即可。 在此同時，請使用本文來判斷 v2.0 端點是否適合您。 我們將持續更新本文，以反映 v2.0 端點目前的狀態。 請記得回來重新評估您對 v2.0 功能的需求。

如果您有一個未使用 v2.0 端點的現有 Azure AD 應用程式，就不需要從頭開始。 未來，我們會為您提供可將現有 Azure AD 應用程式與 v2.0 端點搭配使用的方法。

## <a name="restrictions-on-app-types"></a>應用程式類型的限制

v2.0 端點目前不支援下列類型的應用程式。 如需所支援應用程式類型的描述，請參閱 [Azure Active Directory v2.0 端點的應用程式類型](v2-app-types.md)。

### <a name="standalone-web-apis"></a>獨立的 Web API

您可以使用 v2.0 端點來[建置使用 OAuth 2.0 保護的 Web API](v2-app-types.md#web-apis)。 不過，該 Web API 只能從具有相同「應用程式識別碼」的應用程式接收權杖。 您無法從具有不同「應用程式識別碼」的用戶端存取 Web API。 該用戶端無法要求或取得您 Web API 的權限。

若要了解如何建置 Web API 以接受來自具有相同「應用程式識別碼」之用戶端的權杖，請參閱[開始使用](active-directory-appmodel-v2-overview.md#getting-started)一節中的 v2.0 端點 Web API 範例。

## <a name="restrictions-on-app-registrations"></a>應用程式註冊的限制

目前，針對您想要與 v2.0 端點整合的每個應用程式，您都必須在新的 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中建立應用程式註冊。 現有的 Azure AD 或 Microsoft 帳戶應用程式與 v2.0 端點不相容。 在「應用程式註冊入口網站」以外的任何入口網站註冊的應用程式也與 v2.0 端點不相容。 未來，我們打算提供使用現有應用程式作為 v2.0 應用程式的方法。 目前還沒有可讓現有應用程式與 v2.0 端點搭配運作的移轉途徑。

此外，您在[應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中建立的應用程式註冊還需注意下列幾點︰

* 每個「應用程式識別碼」只允許兩個應用程式密碼。
* 由使用個人 Microsoft 帳戶之使用者所註冊的應用程式註冊，只能透過單一開發人員帳戶來檢視及管理。 不能在多位開發人員之間共用。 如果您要和多位開發人員共用您的應用程式註冊，可以使用 Azure AD 帳戶登入註冊入口網站來建立應用程式。
* 所允許的重新導向 URI 的格式有幾項限制。 如需有關重新導向 URI 的詳細資訊，請參閱下一節。

## <a name="restrictions-on-redirect-uris"></a>重新導向 URI 的限制

針對已在「應用程式註冊入口網站」中註冊的應用程式，僅限使用一組受限的重新導向 URI 值。 Web 應用程式和服務的重新導向 URI 必須以配置 `https` 開頭，而且所有重新導向 URI 值必須共用單一 DNS 網域。 例如，您不能註冊具有下列其中一個重新導向 URI 的 Web 應用程式：

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

註冊系統會將現有重新導向 URI 的整個 DNS 名稱與您要新增之重新導向 URI 的 DNS 名稱做比較。 如果下列任一條件成立，新增 DNS 名稱的要求就會失敗︰  

* 新重新導向 URI 的整個 DNS 名稱與現有重新導向 URI 的 DNS 名稱不相符。
* 新重新導向 URI 的整個 DNS 名稱不是現有重新導向 URI 的子網域。

例如，如果應用程式具有下列重新導向 URI：

`https://login.contoso.com`

您可以在其中新增，就像這樣：

`https://login.contoso.com/new`

在此情況下，DNS 名稱會完全相符。 或者，您可以這樣做：

`https://new.login.contoso.com`

在此情況下，您參照的是 login.contoso.com 的 DNS 子網域。 如果您希望應用程式以 login-east.contoso.com 和 login-west.contoso.com 作為重新導向 URI，您必須依下列序新增這些重新導向 URI︰

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

您可以新增後面兩個，因為它們是第一個重新導向 URI (contoso.com) 的子網域。 即將推出的版本將會移除這項限制。

也請注意，一個特定應用程式只可以有 20 個回覆 URL。

若要了解如何在「應用程式註冊入口網站」中註冊應用程式，請參閱[如何使用 v2.0 端點註冊 App](quickstart-v2-register-an-app.md)。

## <a name="restrictions-on-libraries-and-sdks"></a>程式庫和 SDK 的限制

目前對 v2.0 端點提供的程式庫支援相當有限。 如果您想要在實際執行應用程式中使用 v2.0 端點，您有下列選項：

* 如果您要建置 Web 應用程式，您可以安心地使用 Microsoft 正式運作的伺服器端中介軟體來執行登入和權杖驗證。 這些包括了適用於 ASP.NET 的 OWIN Open ID Connect 中介軟體和 Node.js Passport 外掛程式。 如需使用 Microsoft 中介軟體的程式碼範例，請參閱[開始使用](active-directory-appmodel-v2-overview.md#getting-started)一節。
* 如果您正在建置傳統型或行動應用程式，可以使用其中一個預覽 Microsoft Authentication Library (MSAL)。 這些程式庫處於支援生產環境的預覽階段，因此可以在生產應用程式中安全地使用。 您可以在[驗證程式庫參考](reference-v2-libraries.md)中，閱讀更多和預覽條款與可用程式庫相關的詳細資訊。
* 針對 Microsoft 程式庫未包含的其他平台，您可以透過直接在應用程式程式碼中傳送和接收通訊協定訊息，來與 v2.0 端點整合。 v2.0 OpenID Connect 和 OAuth 通訊協定[有明確的文件記載](active-directory-v2-protocols.md)，可協助您執行這種整合。
* 最後，您可以使用開放原始碼 OpenID Connect 和 OAuth 程式庫來與 v2.0 端點整合。 v2.0 通訊協定應該不需進行重大變更，即可與許多開放原始碼通訊協定程式庫相容。 這類的程式庫的可用性會依語言和平台而有所不同。 [Open ID Connect](http://openid.net/connect/) 和 [OAuth 2.0](http://oauth.net/2/) 網站保有一份常用的實作清單。 如需詳細資訊，以及已用 v2.0 端點測試的開放原始碼用戶端程式庫和範例的清單，請參閱 [Azure Active Directory v2.0 和驗證程式庫](reference-v2-libraries.md)。

## <a name="restrictions-on-protocols"></a>通訊協定的限制

v2.0 端點不支援 SAML 或 WS-同盟，只支援 Open ID Connect 和 OAuth 2.0。 v2.0 端點中並未納入 OAuth 通訊協定的所有功能與特性。

v2.0 端點中目前「並未提供」下列通訊協定功能與特性：

* 目前，只有在已設定選擇性宣告，以及範圍是要求中指定的 scope=email 時，才會傳回 `email` 宣告。 不過，當 v2.0 端點更新為進一步遵循 Open ID Connect 和 OAuth2.0 標準時，此行為也會變更。
* v2.0 端點上未實作 OpenID Connect UserInfo 端點。 不過，所有您可能會在此端點收到的使用者設定檔資料都可從 Microsoft Graph `/me` 端點取得。
* v2.0 端點不支援在識別碼權杖中發出角色或群組宣告。
* v2.0 端點不支援 [OAuth 2.0 資源擁有者密碼認證授與 (英文)](https://tools.ietf.org/html/rfc6749#section-4.3)。

此外，v2.0 端點不支援任何形式的 SAML 或 WS-同盟通訊協定。

若要進一步了解 v2.0 端點中支援的通訊協定功能範圍，請參閱我們的 [OpenID Connect 與 OAuth 2.0 通訊協定參考](active-directory-v2-protocols.md)。

## <a name="restrictions-for-work-and-school-accounts"></a>工作和學校帳戶的限制

如果您已在 Windows 應用程式中使用 Active Directory Authentication Library (ADAL)，您可能已利用使用「安全性聲明標記語言」(SAML) 判斷提示授與的 Windows 整合式驗證。 有了此授與之後，同盟 Azure AD 租用戶的使用者便可向其內部部署 Active Directory 執行個體以無訊息方式進行驗證，而不需輸入認證。 目前 v2.0 端點不支援 SAML 判斷提示授與。
