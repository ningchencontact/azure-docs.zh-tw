---
title: 比較 Microsoft 身分識別平台 (v2.0) 端點與 Azure AD v1.0 端點 |Microsoft Docs
description: 了解 Microsoft 身分識別平台 (v2.0) 端點與 Azure Active Directory (Azure AD) v1.0 端點之間的差異。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd443c95e8cf6dbddd66e5531b182469a118e4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59260726"
---
# <a name="comparing-the-microsoft-identity-platform-endpoint-and-azure-ad-v10-endpoint"></a>比較 Microsoft 身分識別平台端點與 Azure AD v1.0 端點

在開發新的應用程式時，務必知道 Microsoft 身分識別平台 (v2.0) 與 Azure Active Directory (v1.0) 端點之間的差異。 本文章涵蓋的端點，以及一些現有的限制，Microsoft 身分識別平台的主要差異。

> [!NOTE]
> Microsoft 身分識別平台端點不支援所有的 Azure AD 案例和功能。 若要判斷是否應該使用 Microsoft 身分識別平台的端點，請參閱[Microsoft 身分識別平台限制](#limitations)。

## <a name="who-can-sign-in"></a>誰可以登入

![誰可以登入 v1.0 與 v2.0 端點](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* v1.0 端點只允許使用公司和學校帳戶登入您的應用程式 (Azure AD)
* Microsoft 身分識別平台端點可讓工作和學校帳戶從 Azure AD 和個人 Microsoft 帳戶 (MSA)，例如 hotmail.com、 outlook.com 及 msn.com，登入。
* 這兩個端點也會接受登入*[來賓使用者](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* 的 Azure AD 目錄的應用程式設定為*[單一租用戶](single-and-multi-tenant-apps.md)* 若是*多租用戶*設定為指向租用戶專屬端點的應用程式 (`https://login.microsoftonline.com/{TenantId_or_Name}`)。

Microsoft 身分識別平台端點可讓您撰寫接受來自個人 Microsoft 帳戶，以及公司和學校帳戶登入的應用程式。 這可讓您撰寫完全無從驗證帳戶的應用程式。 例如，如果您的應用程式呼叫 [Microsoft Graph](https://graph.microsoft.io) \(英文\)，則公司帳戶可取得一些額外的功能和資料，例如他們的 SharePoint 網站或目錄資料。 但在許多動作 (例如[讀取使用者的郵件](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages)) 中，相同的程式碼可以存取個人及公司和學校帳戶的電子郵件。

Microsoft 身分識別平台的端點，您可以使用來取得存取消費者、 教育及企業優點的 Microsoft Authentication Library (MSAL)。 Azure AD v1.0 端點只接受來自公司和學校帳戶的登入。

## <a name="incremental-and-dynamic-consent"></a>增量和動態同意

使用 Azure AD v1.0 端點的應用程式必須事先指定其所需的 OAuth 2.0 權限，例如：

![權限註冊 UI](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

直接在應用程式註冊上設定的權限是**靜態的**。 雖然在 Azure 入口網站中定義應用程式的靜態使用權限，可讓程式碼保持簡潔，但它可能為開發人員帶來一些問題：

* 應用程式必須在使用者第一次登入時要求可能會需要的所有使用權限。 這可能會是一長串的使用權限，因此讓使用者不願意在初始登入時核准應用程式的存取。

* 應用程式必須事先知道可能會存取的所有資源。 很難建立能夠存取任意數目的資源的應用程式。

與 Microsoft 身分識別平台的端點，您可以忽略靜態定義的權限在 Azure 入口網站並要求權限的應用程式註冊資訊中以累加方式相反地，這表示裸機最小一組權限預先要求並產生更一段時間的客戶使用額外的應用程式功能。 若要這樣做，您隨時可以在要求存取權杖時於 `scope` 參數中納入新的範圍，以指定您應用程式所需的範圍，而不需要在應用程式註冊資訊中預先定義它們。 如果使用者尚未同意新增至要求的新範圍，則系統只會提示他們同意新的使用權限。 若要深入了解，請參閱[使用權限、同意和範圍](v2-permissions-and-consent.md)。

允許應用程式透過 `scope` 參數動態要求權限，讓開發人員可以完全掌控使用者的體驗。 您也可以選擇將您的同意體驗提前，並在一個初始授權要求中要求所有使用權限。 如果您的應用程式需要大量的使用權限，您可以在他們嘗試使用應用程式的某些功能時，以累加的方式逐漸向使用者收集這些使用權限。

代表組織進行的管理員同意，仍然需要為應用程式註冊靜態使用權限，因此如果您需要系統管理員代表整個組織提供同意，您應在應用程式註冊入口網站中，設定應用程式的那些使用權限。 這可減少組織系統管理員設定應用程式所需的週期。

## <a name="scopes-not-resources"></a>範圍，而非資源

若為使用 v1.0 端點的應用程式，應用程式能以**資源**或權杖收件者的方式運作。 資源可以定義它所了解的許多**範圍**或 **oAuth2Permissions**，讓用戶端應用程式得以針對特定一組範圍向該資源要求權杖。 請考慮以 Azure AD Graph API 做為資源的範例：

* 資源識別碼，或 `AppID URI`：`https://graph.windows.net/`
* 範圍，或 `oAuth2Permissions`：`Directory.Read`、`Directory.Write` 等。

這適用於 Microsoft 身分識別平台的端點。 應用程式仍可作為資源、定義範圍並依據 URI 識別。 用戶端應用程式仍可要求存取這些範圍。 不過，用戶端要求那些權限的方式已改變。

對於 v1.0 端點，Azure AD 的 OAuth 2.0 授權要求可能如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

其中 **resource** 參數指出用戶端應用程式要求授權的資源。 Azure AD 根據 Azure 入口網站中的靜態設定計算應用程式所需的權限，並據以發出權杖。

對於使用 Microsoft 身分識別平台端點的應用程式，在相同的 OAuth 2.0 授權要求如下：

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

其中 **scope** 參數指出用戶端應用程式要求授權的資源和使用權限。 所需的資源仍存在於要求中，它包含在 scope 參數的每個值中。 以這種方式使用 scope 參數可讓 Microsoft 身分識別平台端點更符合 OAuth 2.0 規格，並更貼近常見的業界作法。 它也可以讓應用程式執行[累加式同意](#incremental-and-dynamic-consent) - 只在應用程式需要使用權限才要求，而不是事先要求。

## <a name="well-known-scopes"></a>知名的範圍

### <a name="offline-access"></a>離線存取

使用 Microsoft 身分識別平台端點的應用程式可能需要使用新的已知權限的應用程式-`offline_access`範圍。 如果應用程式需要長期代表使用者存取資源，則所有應用程式都需要要求此權限，即使使用者可能不會主動使用此應用程式亦然。 在同意對話方塊中， `offline_access` 範圍會對使用者顯示為 [隨時存取您的資料]，而使用者必須加以同意。 要求`offline_access`權限可讓您的 web 應用程式，以從 Microsoft 身分識別平台端點獲取 OAuth 2.0 refresh_token。 重新整理權杖屬於長效權杖，可用來交換新的 OAuth 2.0 存取權杖以延長存取期間。

如果您的應用程式未要求 `offline_access` 範圍，則不會收到重新整理權杖。 這表示當您在 OAuth 2.0 授權碼流程中兌換授權碼時，只會從 `/token` 端點接收一個存取權杖。 該存取權杖會短時間維持有效 (通常是一小時)，但最後終將過期。 屆時，您的應用程式必須將使用者重新導向回到 `/authorize` 端點以擷取新的授權碼。 在此重新導向期間，視應用程式的類型而定，使用者或許不需要再次輸入其認證或重新同意使用權限。

若要深入了解 OAuth 2.0 `refresh_tokens`，並`access_tokens`，請參閱[Microsoft 身分識別平台通訊協定參考](active-directory-v2-protocols.md)。

### <a name="openid-profile-and-email"></a>OpenID、設定檔和電子郵件

在過去，最基本 OpenID Connect 登入流程與 Microsoft 身分識別平台會提供大量使用者在產生的相關資訊*id_token*。 id_token 中的宣告可以包含使用者的名稱、慣用使用者名稱、電子郵件地址、物件識別碼等等。

現在會限制 `openid` 範圍可提供應用程式存取權的資訊。 `openid` 範圍只允許您的應用程式將使用者登入，並接收使用者的應用程式特定識別碼。 如果您想要取得有關應用程式中使用者的個人資料，您的應用程式便需要向使用者要求其他使用權限。 兩個新範圍 (`email` 和 `profile`) 可讓您要求其他使用權限。

* `email` 範圍可讓您的應用程式透過 id_token 中的 `email` 宣告，存取使用者的主要電子郵件地址 (假設使用者有可定址的電子郵件地址)。 
* `profile` 範圍可以讓您的應用程式存取使用者的所有其他基本資訊，例如其 id_token 中的名稱、慣用的使用者名稱、物件識別碼等等。

這可讓您以最低洩漏的方式撰寫應用程式，所以您只可以向使用者要求應用程式執行其作業所需的資訊集。 如需有關這些範圍的詳細資訊，請參閱 < [Microsoft 身分識別平台範圍參考](v2-permissions-and-consent.md)。

## <a name="token-claims"></a>權杖宣告

Microsoft 身分識別平台的端點預設承載小問題較小的其權杖中的宣告集。 如果您有應用程式和具有相依性在 v1.0 權杖中不再提供的 Microsoft 身分識別平台權杖中的預設值的特定宣告的服務，請考慮使用[選擇性宣告](active-directory-optional-claims.md)包含該宣告的功能。

## <a name="limitations"></a>限制

有一些限制需要注意的時使用 Microsoft 身分識別平台。

當您建置與 Microsoft 身分識別平台整合的應用程式時，您必須決定的 Microsoft 身分識別平台端點和驗證通訊協定是否符合您的需求。 平台與 v1.0 端點仍受到完整支援，並在某些方面，比 Microsoft 身分識別平台功能更豐富。 不過，Microsoft 身分識別平台[帶來顯著的好處](azure-ad-endpoint-comparison.md)適用於開發人員。

以下是我們在這個時間點針對開發人員所提供的簡化建議：

* 如果您想要或需要在應用程式中支援個人 Microsoft 帳戶或您正在撰寫新的應用程式，使用 Microsoft 身分識別平台。 但在這樣做之前，請務必了解此文章中討論的限制。
* 如果您正在移轉，或更新相依於 SAML 應用程式，您無法使用 Microsoft 身分識別平台。 相反地，請參閱[Azure AD v1.0 指南](v1-overview.md)。

Microsoft 身分識別平台端點將會進化到排除此處所列的限制，讓您將只會需要使用 Microsoft 身分識別平台的端點。 在此同時，使用本文來判斷是否最適合您的 Microsoft 身分識別平台的端點。 我們將持續更新以反映目前的 Microsoft 身分識別平台端點的狀態這篇文章。 請檢查回到重新評估您對 Microsoft 身分識別平台功能的需求。

### <a name="restrictions-on-app-registrations"></a>應用程式註冊的限制

您想要整合與 Microsoft 身分識別平台端點每個應用程式中，您可以在新建立的應用程式註冊[**應用程式註冊**體驗](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)在 Azure 入口網站中。 現有的 Microsoft 帳戶應用程式不相容預覽入口網站中，但所有的 Azure AD 應用程式，不論其位置及時間註冊。

支援公司和學校帳戶及個人帳戶的應用程式註冊有下列注意事項：

* 每個應用程式識別碼只允許兩個應用程式密碼。
* 沒有在租用戶中註冊的應用程式，只能由註冊該應用程式的帳戶管理。 不能與其他開發人員共用該應用程式。 在應用程式註冊入口網中使用個人 Microsoft 帳戶註冊的大部分應用程式都是此情況。 如果您想要與多個開發人員分享您的應用程式註冊，請使用新的租用戶中註冊應用程式**應用程式註冊**Azure 入口網站區段。
* 所允許的重新導向 URL 的格式有幾項限制。 如需有關重新導向 URL 的詳細資訊，請參閱下一節。

### <a name="restrictions-on-redirect-urls"></a>重新導向 URL 的限制

Microsoft 身分識別平台已註冊的應用程式僅限於一組有限的重新導向 URL 值。 Web 應用程式和服務的重新導向 URL 必須以配置 `https` 開頭，而且所有重新導向 URL 值必須共用單一 DNS 網域。  註冊系統會將現有重新導向 URL 的整個 DNS 名稱與您要新增之重新導向 URL 的 DNS 名稱做比較。 系統也支援將 `http://localhost` 作為重新導向 URL。  

如果下列任一條件成立，新增 DNS 名稱的要求就會失敗︰  

* 新重新導向 URL 的整個 DNS 名稱與現有重新導向 URL 的 DNS 名稱不相符。
* 新重新導向 URL 的整個 DNS 名稱不是現有重新導向 URL 的子網域。

#### <a name="example-1"></a>範例 1

如果應用程式的重新導向 URL 是 `https://login.contoso.com`，您可以新增 DNS 名稱完全相符的重新導向 URL，如下列範例所示：

`https://login.contoso.com/new`

或者，您可以參照 login.contoso.com 的 DNS 子網域，如下列範例所示：

`https://new.login.contoso.com`

#### <a name="example-2"></a>範例 2

如果您希望應用程式以 `login-east.contoso.com` 和 `login-west.contoso.com` 作為重新導向 URL，您必須依下列順序新增這些重新導向 URL︰

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

您可以新增後面兩個，因為它們是第一個重新導向 URL (contoso.com) 的子網域。 即將推出的版本將會移除這項限制。

另請注意，針對特定應用程式您只能有 20 個回覆 URL - 此限制會套用到註冊所支援的所有應用程式類型 (SPA、原生用戶端、Web 應用程式和服務)。  

若要了解如何向 Microsoft 身分識別平台使用的應用程式，請參閱[註冊應用程式使用新的應用程式註冊體驗](quickstart-register-app.md)。

### <a name="restrictions-on-libraries-and-sdks"></a>程式庫和 SDK 的限制

目前，Microsoft 身分識別平台端點的程式庫支援是有限的。 如果您想要在生產環境應用程式中使用 Microsoft 身分識別平台的端點，您會有下列選項：

* 如果您要建置 Web 應用程式，您可以安心地使用公開推出的伺服器端中介軟體來執行登入和權杖驗證。 這些包括了適用於 ASP.NET 的 OWIN OpenID Connect 中介軟體和 Node.js Passport 外掛程式。 如需使用 Microsoft 中介軟體的程式碼範例，請參閱[開始使用 Microsoft 身分識別平台](v2-overview.md#getting-started)一節。
* 如果您正在建置傳統型或行動應用程式，可以使用其中一個預覽 Microsoft 驗證程式庫 (MSAL)。 這些程式庫處於支援生產環境的預覽階段，因此可以在生產應用程式中安全地使用。 您可以在[驗證程式庫參考](reference-v2-libraries.md)中，閱讀更多和預覽條款與可用程式庫相關的詳細資訊。
* 對於未涵蓋的 Microsoft 程式庫的平台，您可以透過直接傳送和接收通訊協定訊息在應用程式程式碼中整合與 Microsoft 身分識別平台的端點。 OpenID Connect 和 OAuth 通訊協定[明確地記載](active-directory-v2-protocols.md)可協助您執行這種整合。
* 最後，您可以使用開放原始碼 OpenID Connect 和 OAuth 程式庫來與 Microsoft 身分識別平台端點整合。 Microsoft 身分識別平台端點應該與許多開放原始碼通訊協定程式庫，而不需要變更相容。 這類的程式庫的可用性會依語言和平台而有所不同。 [OpenID Connect](https://openid.net/connect/) \(英文\) 和 [OAuth 2.0](https://oauth.net/2/) \(英文\) 網站保有一份常用的實作清單。 如需詳細資訊，請參閱 < [Microsoft 身分識別平台和驗證程式庫](reference-v2-libraries.md)，以及開放原始碼用戶端程式庫和經過 Microsoft 身分識別平台端點的範例的清單。
* 如需參考， `.well-known` Microsoft 身分識別平台的通用端點的端點是`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`。 使用您的租用戶識別碼來取代 `common`，以取得您租用戶特有的資料。  

### <a name="protocol-changes"></a>通訊協定變更

Microsoft 身分識別平台端點不支援 SAML 或 WS-同盟;此外，它只會支援 OpenID Connect 和 OAuth 2.0。  對於 1.0 版端點 OAuth 2.0 通訊協定的重大變更為： 

* 如果設定選擇性宣告**或**在要求中指定 scope=email，則會傳回 `email` 宣告。 
* 現已支援代替 `resource` 參數的 `scope` 參數。  
* 已修改許多回應，使它們更符合 OAuth 2.0 的規範，例如正確地將 `expires_in` 傳回為 Int 而不是字串。  

若要進一步了解的 Microsoft 身分識別平台端點中支援的通訊協定功能範圍，請參閱[OpenID Connect 和 OAuth 2.0 通訊協定參考](active-directory-v2-protocols.md)。

#### <a name="saml-restrictions"></a>SAML 限制

如果您已在 Windows 應用程式中使用 Active Directory Authentication Library (ADAL)，您可能已利用使用「安全性聲明標記語言」(SAML) 判斷提示授與的 Windows 整合式驗證。 有了此授與之後，同盟 Azure AD 租用戶的使用者便可向其內部部署 Active Directory 執行個體以無訊息方式進行驗證，而不需輸入認證。 不支援 SAML 判斷提示授與端點上的 Microsoft 身分識別平台。
