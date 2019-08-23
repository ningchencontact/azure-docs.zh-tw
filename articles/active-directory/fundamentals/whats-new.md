---
title: 新功能 版本資訊 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93327091d0065a67d441dd56cadd83f4f7d2d1f2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970388"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>藉由複製並貼上此 URL, 取得何時要重新流覽此頁面以`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`進行更新![的通知: 在](./media/whats-new/feed-icon-16x16.png)您的 RSS 摘要讀取器圖示摘要讀取器中。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 會針對代理者案例忽略 MSAL.NET 共用快取

**類型：** 已修正  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

從 Azure AD authentication library (ADAL.NET) 第5.0.0 版-preview 開始, 應用程式開發人員必須[針對 web 應用程式和 Web api, 將每個帳戶的一個](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)快取序列化。 否則, 某些使用代理者[流程](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)的案例以及某些特定的`UserAssertion`使用案例, 可能會導致權限提高。 為避免此弱點, ADAL.NET 現在會忽略適用于代理者案例的 Microsoft authentication library for dotnet (MSAL.NET) 共用快取。

如需有關此問題的詳細資訊, 請參閱[Azure Active Directory 驗證程式庫權限提高弱點](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

## <a name="july-2019"></a>2019年7月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>規劃變更:僅支援 TLS 1.2 的應用程式 Proxy 服務更新

**類型：** 針對變更做規劃  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

為了協助提供您最強的加密, 我們將開始限制只有 TLS 1.2 通訊協定的應用程式 Proxy 服務存取。 這項限制一開始會向已使用 TLS 1.2 通訊協定的客戶推出, 因此您不會看到影響。 TLS 1.0 和 TLS 1.1 通訊協定的完整淘汰將于2019年8月31日完成。 仍在使用 TLS 1.0 和 TLS 1.1 的客戶, 將會收到針對此變更進行準備的 advanced 通知。

若要在此變更期間維持與應用程式 Proxy 服務的連線, 建議您確定您的用戶端-伺服器和瀏覽器伺服器組合已更新為使用 TLS 1.2。 我們也建議您務必包含員工所使用的任何用戶端系統, 以存取透過應用程式 Proxy 服務發佈的應用程式。

如需詳細資訊, 請參閱在[Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>規劃變更:應用程式資源庫的設計更新即將推出

**類型：** 針對變更做規劃  
**服務類別：** 企業應用程式  
**產品功能：** SSO

新的使用者介面變更即將進入 [**加入應用程式**] 分頁之 [元件**庫**] 區域的設計。 這些變更可協助您更輕鬆地找到支援自動布建、OpenID Connect、安全性聲明標記語言 (SAML) 和密碼單一登入 (SSO) 的應用程式。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>規劃變更:從 Office 365 IP 位址移除 MFA server IP 位址

**類型：** 針對變更做規劃  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

我們會從[Office 365 Ip 位址和 URL Web 服務](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)移除 MFA 伺服器 ip 位址。 如果您目前依賴這些頁面來更新您的防火牆設定, 您必須確定也包含使用者入門的**Azure 多因素驗證服務器防火牆需求**一節中記載的 IP 位址清單。 [使用 Azure 多因素驗證服務器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)一文。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>僅限應用程式權杖現在需要用戶端應用程式存在於資源租使用者中

**類型：** 已修正  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

在2019年7月26日, 我們變更了透過[用戶端認證授](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)與提供僅限應用程式權杖的方式。 先前, 應用程式可以取得權杖來呼叫其他應用程式, 而不論用戶端應用程式是否在租使用者中。 我們已更新此行為, 因此只有存在於資源租使用者中的用戶端應用程式, 才能呼叫單一租使用者資源 (有時稱為 Web Api)。

如果您的應用程式不在資源租使用者中, 您會收到錯誤訊息, 指出`The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`若要修正此問題, 您必須使用系統[管理員同意端點](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)或[透過 PowerShell, 在租使用者中建立用戶端應用程式服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), 可確保您的租使用者已提供應用程式許可權, 以在租使用者內操作。

如需詳細資訊, 請參閱[驗證的新功能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)。

> [!NOTE]
> 用戶端與 API 之間的現有同意仍不需要。 應用程式仍應執行自己的授權檢查。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>新的無密碼使用 FIDO2 安全性金鑰登入 Azure AD

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

Azure AD 客戶現在可以設定原則來管理其組織之使用者和群組的 FIDO2 安全性金鑰。 使用者也可以自行註冊其安全性金鑰、使用金鑰在網站上登入其 Microsoft 帳戶, 同時在具備 FIDO 功能的裝置上, 以及登入其已加入 Azure AD 的 Windows 10 裝置。

如需詳細資訊, 請參閱為[Azure AD (預覽) 啟用無密碼登入](/azure/active-directory/authentication/concept-authentication-passwordless)以取得系統管理員相關資訊, 以及[將安全性資訊設定為使用安全性金鑰 (預覽)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key)來取得使用者相關資訊。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年7月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年7月, 將具有同盟支援的18個新應用程式新增至應用程式庫:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial),[鮮圖樣 Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial),[聰明的 Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex 合規性 Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)、 [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)、 [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、 [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)、 [TwineSocial](https://twinesocial.com/)、 [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial)、 [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)、 [PharmID WasteWitness](https://www.pharmid.com/)、 [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、 [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶:

- [撥號盤](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [同盟目錄](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊, 請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>網路安全性群組的新 Azure AD Domain Services 服務標記

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

如果您不想管理長清單的 IP 位址和範圍, 可以使用 Azure 網路安全性群組中的新**AzureActiveDirectoryDomainServices**網路服務標籤, 以協助保護您 Azure AD Domain Services 虛擬的輸入流量網路子網。

如需這個新服務標記的詳細資訊, 請參閱[Azure AD Domain Services 的網路安全性群組](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 的新安全性審查 (公開預覽)

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

我們很高興宣佈發行 Azure AD 網域服務安全性審核到公開預覽。 安全性審核透過將安全性審核事件串流至目標資源 (包括 Azure 儲存體、Azure Log Analytics 工作區, 以及使用 Azure AD 網域服務的 Azure 事件中樞), 協助您對驗證服務提供重要的深入解析。入口.

如需詳細資訊, 請參閱[啟用 Azure AD Domain Services 的安全性審核 (預覽)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新的驗證方法使用 & insights (公開預覽)

**類型：** 新功能  
**服務類別：** 自助密碼重設  
**產品功能：** 監視與報告

新的驗證方法使用 & insights 報告可協助您瞭解 Azure 多因素驗證和自助式密碼重設等功能在您的組織中註冊和使用的方式, 包括已註冊的數目每項功能的使用者、用來重設密碼的自助式密碼重設頻率, 以及重設發生的方法。

如需詳細資訊, 請參閱[驗證方法使用 & insights (預覽)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>所有 Azure AD 系統管理員都可以使用新的安全性報告 (公開預覽)

**類型：** 新功能  
**服務類別：** Identity Protection  
**產品功能：** 身分識別安全性與保護

所有 Azure AD 系統管理員現在都可以選取現有安全性報告頂端的橫幅, 例如**標示為有風險的使用者**報告, 以開始使用有風險的**使用者**和有風險的登**入**報告中所示的新安全性體驗. 經過一段時間之後, 所有的安全性報告都會從較舊的版本移至新版本, 而新的報表會提供您下列額外的功能:

- 先進的篩選和排序

- 大量動作, 例如關閉使用者風險

- 確認遭盜用或安全的實體

- 風險狀態, 涵蓋:有風險、已解除、補救和確認遭到入侵

如需詳細資訊, 請參閱有[風險的使用者報告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report)和有[風險的登入報告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 的新安全性審查 (公開預覽)

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

我們很高興宣佈發行 Azure AD 網域服務安全性審核到公開預覽。 安全性審核透過將安全性審核事件串流至目標資源 (包括 Azure 儲存體、Azure Log Analytics 工作區, 以及使用 Azure AD 網域服務的 Azure 事件中樞), 協助您對驗證服務提供重要的深入解析。入口.

如需詳細資訊, 請參閱[啟用 Azure AD Domain Services 的安全性審核 (預覽)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>使用 SAML/WS-送出新的 B2B 直接同盟 (公開預覽)

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C

直接同盟可協助您更輕鬆地使用其 IT 受控識別解決方案不 Azure AD 的合作夥伴, 方法是使用支援 SAML 或 WS-ADDRESSING 標準的身分識別系統。 在您設定與夥伴的直接同盟關聯性之後, 您從該網域邀請的任何新來賓使用者都可以使用其現有的組織帳戶與您共同作業, 讓您的來賓使用者體驗更順暢。

如需詳細資訊, 請參閱[來賓使用者的直接與 AD FS 和協力廠商提供者的同盟 (預覽)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶:

- [撥號盤](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [同盟目錄](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊, 請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中新檢查重複的組名

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 協同合作

現在, 當您從 Azure AD 入口網站建立或更新組名時, 我們會執行檢查以查看您是否在資源中複製現有的組名。 如果我們判斷該名稱已被另一個群組使用, 系統會要求您修改您的名稱。

如需詳細資訊, 請參閱在[Azure AD 入口網站中管理群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD 現在支援回復 (重新導向) Uri 中的靜態查詢參數

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

Azure AD 應用程式現在可以針對 OAuth 2.0 要求, 使用靜態查詢參數 (例如, `https://contoso.com/oauth2?idp=microsoft`) 來註冊並使用回復 (重新導向) uri。 靜態查詢參數受限於回復 uri 的字串比對, 就像回復 URI 的任何其他部分一樣。 如果沒有任何已註冊的字串符合 URL 解碼的重新導向 uri, 則會拒絕要求。 如果找到回復 URI, 則會使用整個字串來重新導向使用者, 包括靜態查詢參數。

動態回復 Uri 仍然是禁止的, 因為它們代表安全性風險, 而且無法用來在驗證要求中保留狀態資訊。 基於此目的, 請使用`state`參數。

目前, Azure 入口網站的應用程式註冊畫面仍會封鎖查詢參數。 不過, 您可以手動編輯應用程式資訊清單, 以在您的應用程式中新增和測試查詢參數。 如需詳細資訊, 請參閱[驗證的新功能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Azure AD 的活動記錄 (MS Graph Api) 現在可透過 PowerShell Cmdlet 取得

**類型：** 新功能  
**服務類別：** 報表  
**產品功能：** 監視與報告

我們很高興宣佈, Azure AD 活動記錄 (Audit 和登入報告) 現在可以透過 Azure AD PowerShell 模組取得。 之前, 您可以使用 MS 圖形 API 端點來建立自己的腳本, 現在我們已將該功能擴充至 PowerShell Cmdlet。

如需有關如何使用這些 Cmdlet 的詳細資訊, 請參閱[適用于報告的 Azure AD PowerShell Cmdlet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>已更新 Azure AD 中 Audit 和登入記錄的篩選器控制項

**類型：** 已變更的功能  
**服務類別：** 報表  
**產品功能：** 監視與報告

我們已更新 Audit 和登入記錄報告, 因此您現在可以套用各種篩選, 而不需要將它們新增為報表畫面上的資料行。 此外, 您現在可以決定要在螢幕上顯示的篩選數目。 這些更新會共同合作, 讓您的報表更容易閱讀, 而且更有範圍滿足您的需求。

如需這些更新的詳細資訊, 請參閱[篩選 audit 記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)和[篩選登入活動](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>適用于 Microsoft Graph 的新 riskDetections API (公開預覽)

**類型：** 新功能  
**服務類別：** Identity Protection  
**產品功能：** 身分識別安全性與保護

我們很高興宣佈適用于 Microsoft Graph 的新 riskDetections API 現已開放公開預覽。 您可以使用這個新的 API 來查看您組織的身分識別保護相關使用者和登入風險偵測的清單。 您也可以使用此 API 更有效率地查詢您的風險偵測, 包括偵測類型、狀態、層級等等的詳細資料。

如需詳細資訊, 請參閱[風險偵測 API 參考檔](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2019 年6月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年6月, 將具有同盟支援的22個新應用程式新增至應用程式庫:

[AZURE AD SAML 工具](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)組、 [Otsuka Shokai (大塚商會)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、 [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、 [Azure VPN 用戶端](https://portal.azure.com/)、 [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、 [helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、 [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)、 [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)、 [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/)、 [Skore](https://app.justskore.it/)、[Oracle 雲端基礎結構主控台](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML 驗證](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), oracle Access Manager for oracle零售商品、oracle Access Manager for Oracle 電子商務套件、適用于電子商務套件的 Oracle IDCS、Oracle IDCS for PeopleSoft、Oracle IDCS for JD Edwards

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊, 請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>查看 Azure AD 布建服務的即時進度

**類型：** 已變更的功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理

我們已更新 Azure AD 布建體驗, 以包含新的進度列, 顯示您在使用者布建程式中的程度。 這項更新的體驗也會提供在目前週期期間布建的使用者數目, 以及已布建至日期的使用者人數的相關資訊。

如需詳細資訊, 請參閱[檢查使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)布建的狀態。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>公司商標現在會出現在登出和錯誤畫面上

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

我們已更新 Azure AD, 因此您的公司商標現在會出現在 [登出] 和 [錯誤] 畫面上, 以及 [登入] 頁面。 您不需要執行任何動作來開啟此功能, Azure AD 只會使用您在 Azure 入口網站**公司商標**區域中已設定的資產。

如需設定公司商標的詳細資訊, 請參閱[將商標新增至貴組織的 Azure Active Directory 頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure 多重要素驗證 (MFA) 伺服器已不再適用于新的部署

**類型：** 即將淘汰  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

從2019年7月1日起, Microsoft 將不再為新的部署提供 MFA 伺服器。 新客戶若想要在其組織中要求多重要素驗證, 現在必須使用雲端式 Azure 多重要素驗證。 在7月1日前啟動 MFA Server 的客戶不會看到變更。 您仍然可以下載最新版本、取得未來的更新, 以及產生啟用認證。

如需詳細資訊, 請參閱[開始使用 Azure 多因素驗證服務器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)。 如需以雲端為基礎的 Azure 多因素驗證的詳細資訊, 請參閱[規劃雲端式 Azure 多因素驗證部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服務變更:未來僅支援應用程式 Proxy 服務上的 TLS 1.2 通訊協定

**類型：** 針對變更做規劃  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

為了協助為我們的客戶提供最高等級的加密, 我們會限制只有應用程式 Proxy 服務上的 TLS 1.2 通訊協定才能存取。 這種變更會逐漸推出給已經只使用 TLS 1.2 通訊協定的客戶, 因此您不應該看到任何變更。

TLS 1.0 和 TLS 1.1 的淘汰會在2019年8月31日發生, 但我們會提供額外的 advanced 通知, 讓您有時間準備進行這項變更。 若要為此變更做準備, 請確定您的用戶端伺服器和瀏覽器伺服器組合 (包括您的使用者用來存取透過應用程式 Proxy 發佈之應用程式的任何用戶端) 都會更新為使用 TLS 1.2 通訊協定來維護應用程式的連接Proxy 服務。 如需詳細資訊, 請參閱在[Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用 [使用量和深入解析] 報告來查看應用程式相關的登入資料

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

您現在可以使用 Azure 入口網站的 **企業應用程式** 區域中的 使用量和深入解析 報表, 以應用程式為主的登入資料檢視, 包括下列資訊:

- 貴組織最常用的應用程式

- 具有最多失敗登入的應用程式

- 每個應用程式的最上層登入錯誤

如需這項功能的詳細資訊, 請參閱[Azure Active Directory 入口網站中的使用量和深入解析報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>使用 Azure AD 將使用者布建自動布建至雲端應用程式

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

請遵循這些新的教學課程, 使用 Azure AD 布建服務, 自動建立、刪除和更新下列雲端式應用程式的使用者帳戶:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

您也可以遵循這個新的[Dropbox 教學](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)課程, 其中提供如何布建群組物件的相關資訊。

如需如何透過自動化的使用者帳戶布建更進一步保護組織安全的詳細資訊, 請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>身分識別安全分數現已于 Azure AD 提供 (正式運作)

**類型：** 新功能  
**服務類別：** N/A  
**產品功能：** 身分識別安全性與保護

您現在可以使用 Azure AD 中的身分識別安全分數功能來監視及改善您的身分識別安全性狀態。 身分識別安全分數功能會使用單一儀表板來協助您:

- 客觀地根據1到223之間的分數, 測量您的身分識別安全性狀態。

- 規劃您的身分識別安全性改進

- 回顧您的安全性改進成果

如需身分識別安全性分數功能的詳細資訊, 請參閱[Azure Active Directory 中的身分識別安全分數為何？](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>新的應用程式註冊體驗現已推出 (正式運作)

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 開發人員體驗

新的[應用程式註冊](https://aka.ms/appregistrations)體驗現已正式推出。 這項新體驗包括您在 Azure 入口網站和應用程式註冊入口網站中熟悉的所有重要功能, 並透過下列方式加以改善:

- **更好的應用程式管理。** 您現在可以在同一個位置看到您的所有應用程式, 而不是在不同的入口網站中看到您的應用程式。

- **簡化的應用程式註冊。** 從改良的流覽體驗到改頭換面許可權選擇體驗, 現在可以更輕鬆地註冊及管理您的應用程式。

- **詳細資訊。** 您可以找到更多有關應用程式的詳細資料, 包括快速入門手冊等等。

如需詳細資訊, 請參閱[Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/)和[應用程式註冊體驗現已正式推出!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog 公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>適用于身分識別保護的具風險使用者 API 中可用的新功能

**類型：** 新功能  
**服務類別：** Identity Protection  
**產品功能：** 身分識別安全性與保護

我們很高興宣佈您現在可以使用具風險的使用者 API 來抓取使用者的風險歷程記錄、解除有風險的使用者, 以及確認使用者是否遭到入侵。 這項變更可協助您更有效率地更新使用者的風險狀態, 並瞭解其風險歷程記錄。

如需詳細資訊, 請參閱有[風險的使用者 API 參考檔](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-5 月2019

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在5月2019日, 我們已將下列21個新的應用程式與同盟支援新增至應用程式庫:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)、 [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)、 [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)、 [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)、 [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)、 [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)、 [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)、 [Marketo Sales 參與](https://toutapp.com/login)、 [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)、 [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)、 [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)、[量子Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial),[鈷](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>改善 Azure AD 入口網站中的群組建立和管理體驗

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 協同合作

我們已改善 Azure AD 入口網站中的群組相關體驗。 這些改良功能可讓系統管理員更有效地管理群組清單、成員清單, 以及提供其他建立選項。

改善項目包括：

- 依成員資格類型和群組類型的基本篩選。

- 加入新的資料行, 例如來源和電子郵件地址。

- 可以多重選取群組、成員和擁有者清單以方便刪除。

- 在群組建立期間選擇電子郵件地址及新增擁有者的能力。

如需詳細資訊, 請參閱[建立基本群組和使用 Azure Active Directory 新增成員](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>在 Azure AD 入口網站中設定 Office 365 群組的命名原則 (公開上市)

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能：** 協同合作

系統管理員現在可以使用 Azure AD 入口網站, 為 Office 365 群組設定命名原則。 這項變更有助於針對組織中的使用者所建立或編輯的 Office 365 群組強制執行一致的命名慣例。

您可以透過兩種不同的方式來設定 Office 365 群組的命名原則:

- 定義會自動新增至組名的首碼或尾碼。

- 針對您的組織上傳自訂的封鎖字組, 不允許用於組名 (例如, 「CEO, 薪資, HR」)。

如需詳細資訊, 請參閱對[Office 365 群組強制執行命名原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 端點現在可供 Azure AD 活動記錄 (正式運作)

**類型：** 已變更的功能  
**服務類別：** 報表  
**產品功能：** 監視與報告

我們很高興宣佈 Azure AD 活動記錄的 Microsoft Graph API 端點支援正式運作。 在此版本中, 您現在可以使用1.0 版的 Azure AD audit 記錄, 以及登入記錄 Api。

如需詳細資訊, 請參閱[Azure AD audit LOG API 總覽](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>系統管理員現在可以將條件式存取用於合併的註冊程式 (公開預覽)

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護  

系統管理員現在可以建立條件式存取原則, 以供結合的註冊頁面使用。 這包括套用原則以允許註冊, 如果:

- 使用者位於受信任的網路上。

- 使用者是很低的登入風險。

- 使用者位於受管理的裝置上。

- 使用者同意組織的使用規定 (TOU)。

如需有關條件式存取和密碼重設的詳細資訊, 您可以查看[Azure AD 結合的 MFA 和密碼重設註冊體驗 blog 文章的條件式存取](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 如需合併註冊程式之條件式存取原則的詳細資訊, 請參閱[結合註冊的條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)。 如需 Azure AD 使用規定功能的詳細資訊, 請參閱[Azure Active Directory 使用規定功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新的 Azure AD 威脅情報偵測現在可作為 Azure AD Identity Protection 的一部分

**類型：** 新功能  
**服務類別：** Azure AD Identity Protection  
**產品功能：** 身分識別安全性與保護

Azure AD 的威脅情報偵測現在已在更新的 Azure AD Identity Protection 功能中提供。 這種新功能有助於指出特定使用者或活動的異常使用者活動, 其與根據 Microsoft 內部和外部威脅情報來源的已知攻擊模式一致。

如需 Azure AD Identity Protection 重新整理版本的詳細資訊, 請參閱[現已公開預覽的四個主要 Azure AD Identity Protection 增強功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935), 以及[Azure Active Directory Identity Protection 的內容 (重新整理)。](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 篇. 如需有關 Azure AD 威脅情報偵測的詳細資訊, 請參閱[Azure Active Directory Identity Protection 風險事件](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)一文。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 的權利管理現已推出 (公開預覽)

**類型：** 新功能  
**服務類別：** Identity Governance  
**產品功能：** Identity Governance

Azure AD 權利管理 (現為公開預覽狀態) 可協助客戶委派存取套件的管理, 以定義員工和商務合作夥伴如何要求存取權、誰必須核准, 以及他們有權存取的時間長度。 存取套件可以管理 Azure AD 和 Office 365 群組中的成員資格、企業應用程式中的角色指派, 以及 SharePoint Online 網站的角色指派。 如需有關權利管理的詳細資訊, 請參閱[Azure AD 權利管理總覽](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要深入瞭解 Azure AD Identity Governance 功能的廣度, 包括 Privileged Identity Management、存取權審查和使用規定, 請參閱[什麼是 Azure AD Identity Governance？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 入口網站中設定 Office 365 群組的命名原則 (公開預覽)

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 協同合作

系統管理員現在可以使用 Azure AD 入口網站, 為 Office 365 群組設定命名原則。 這項變更有助於針對組織中的使用者所建立或編輯的 Office 365 群組強制執行一致的命名慣例。

您可以透過兩種不同的方式來設定 Office 365 群組的命名原則:

- 定義會自動新增至組名的首碼或尾碼。

- 針對您的組織上傳自訂的封鎖字組, 不允許用於組名 (例如, 「CEO, 薪資, HR」)。

如需詳細資訊, 請參閱對[Office 365 群組強制執行命名原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活動記錄現已于 Azure 監視器提供 (正式運作)

**類型：** 新功能  
**服務類別：** 報表  
**產品功能：** 監視與報告

為了協助您使用 Azure AD 活動記錄來處理視覺效果的意見反應, 我們在 Log Analytics 中引進了新的深入解析功能。 這項功能可協助您使用我們的互動式範本 (稱為活頁簿), 深入瞭解您的 Azure AD 資源。 這些預先建立的活頁簿可以提供應用程式或使用者的詳細資料, 包括:

- **登入。** 提供應用程式和使用者的詳細資料, 包括登入位置、使用中作業系統或瀏覽器用戶端和版本, 以及成功或失敗的登入次數。

- **舊版驗證和條件式存取。** 提供使用舊版驗證之應用程式和使用者的詳細資料, 包括條件式存取原則所觸發的多重要素驗證使用方式、使用條件式存取原則的應用程式等等。

- **登入失敗分析。** 協助您判斷是否因為使用者動作、原則問題或基礎結構而發生登入錯誤。

- **自訂報表。** 您可以建立新的或編輯現有的活頁簿, 以協助自訂您組織的深入解析功能。

如需詳細資訊, 請參閱[如何使用 Azure Active Directory 報表的 Azure 監視器活頁簿](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2019 年4月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年4月將下列21個具有同盟支援的新應用程式新增至應用程式庫:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)、 [hrworks single sign-on 單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)、 [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)、 [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)、 [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)、 [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)、 [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)、 [MileIQ](https://mileiq.onelink.me/991934284/7e980085)、 [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)、 [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)、 [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (以角色為基礎的 SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [certent equity 權益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [sectigo certificate 憑證管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial),[SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)、 [Indiggo](https://indiggolead.com/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新的存取審查頻率選項和多個角色選取

**類型：** 新功能  
**服務類別：** 存取權檢閱  
**產品功能：** Identity Governance

Azure AD 存取審查的新更新可讓您:

- 除了先前現有的每週、每月、每季和每年的選項以外, 請將存取權評論的頻率變更為**半年**。

- 建立單一存取權審查時, 請選取多個 Azure AD 和 Azure 資源角色。 在這種情況下, 所有角色都是以相同的設定進行設定, 而且所有的審核者都會同時收到通知。

如需有關如何建立存取權審查的詳細資訊, 請參閱[在 Azure AD 存取審查中建立群組或應用程式的存取權審查](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 電子郵件警示系統正在轉換, 傳送部分客戶的新電子郵件寄件者資訊

**類型：** 已變更的功能  
**服務類別：** AD 同步  
**產品功能：** 平台

Azure AD Connect 正在轉換我們的電子郵件警示系統, 可能會向一些客戶顯示新的電子郵件寄件者。 若要解決此情況, 您`azure-noreply@microsoft.com`必須將新增至您組織的允許清單, 否則將無法繼續接收來自 Office 365、Azure 或同步服務的重要警示。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Azure AD Connect 中的同盟網域之間的 UPN 尾碼變更現在成功

**類型：** 已修正  
**服務類別：** AD 同步  
**產品功能：** 平台

您現在可以在 Azure AD Connect 中, 成功將使用者的 UPN 尾碼從一個同盟網域變更為另一個同盟網域。 這項修正表示您應該不會在同步處理迴圈期間遇到導致發生 federateddomainchangeerror 錯誤訊息, 也不會收到通知電子郵件, 指出「無法在 Azure Active Directory 中更新此物件, 因為屬性 [FederatedUser. UserPrincipalName] 無效。 更新本機目錄服務中的值」。

如需詳細資訊, 請參閱針對[同步處理期間的錯誤進行疑難排解](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>在 Azure AD 中使用以應用程式保護為基礎的條件式存取原則來提高安全性 (公開預覽)

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

以應用程式保護為基礎的條件式存取現在可使用「**需要應用程式保護**原則」來取得。 這項新原則有助於避免下列情況來增加貴組織的安全性:

- 使用者在沒有 Microsoft Intune 授權的情況下, 取得應用程式的存取權。

- 使用者無法取得 Microsoft Intune 的應用程式保護原則。

- 使用者在沒有設定 Microsoft Intune 應用程式保護原則的情況下, 取得應用程式的存取權。

如需詳細資訊, 請參閱[如何要求應用程式保護原則以使用條件式存取進行雲端應用程式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 中 Azure AD 單一登入和條件式存取的新支援 (公開預覽)

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

我們已增強 Microsoft Edge 的 Azure AD 支援, 包括提供 Azure AD 單一登入和條件式存取的新支援。 如果您先前已使用 Microsoft Intune Managed Browser, 您現在可以改用 Microsoft Edge。

如需使用條件式存取來設定及管理裝置和應用程式的詳細資訊, 請參閱要求使用條件式存取的[雲端應用程式存取受管理的裝置](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), 並[要求透過條件式存取進行雲端應用程式存取的核准用戶端應用程式](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 如需有關如何使用 Microsoft Edge 與 Microsoft Intune 原則來管理存取權的詳細資訊, 請參閱[使用 Microsoft Intune 受原則保護的瀏覽器來管理網際網路存取](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C 中的 Identity Experience Framework 和自訂原則支援現已推出 (GA)

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您現在可以在 Azure AD B2C 中建立自訂原則, 包括下列可大規模支援的工作, 以及我們的 Azure SLA:

- 使用自訂原則來建立和上傳自訂驗證使用者旅程。

- 逐步將使用者旅程描述為宣告提供者之間的交換。

- 定義使用者旅程中的條件式分支。

- 轉換和對應宣告, 以供即時決策和通訊使用。

- 在您的自訂驗證使用者旅程中使用已啟用 REST API 的服務。 例如, 透過電子郵件提供者、Crm 和專屬授權系統。

- 與相容于 OpenIDConnect 通訊協定的身分識別提供者同盟。 例如, 使用多租使用者 Azure AD、社交帳戶提供者或雙因素驗證提供者。

如需建立自訂原則的詳細資訊, 請參閱[Azure Active Directory B2C 中的自訂原則的開發人員注意事項](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom)和閱讀[Alex Simon 的 blog 文章, 包括個案研究](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2019 年3月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2019年3月, 我們已將這14個具有同盟支援的新應用程式新增至應用程式庫:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), 以[說明為基礎的審核系統](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial),[精簡](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [powerschool performance matters 效能重要](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/)、[鳶尾花內部](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial)網路、 [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial)、 [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)、 [confirmit horizons 視野](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial)、 [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD 資源庫中的新 Zscaler 和 Atlassian 布建連接器-2019 年3月

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合

自動建立、更新和刪除下列應用程式的使用者帳戶:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 二](https://aka.ms/ZscalerTwoProvisioning), [Zscaler 三](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

如需如何透過自動化的使用者帳戶布建更進一步保護組織安全的詳細資訊, 請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://aka.ms/ProvisioningDocumentation)布建。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中還原和管理已刪除的 Office 365 群組

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 協同合作

您現在可以從 Azure AD 入口網站查看和管理已刪除的 Office 365 群組。 這項變更可協助您查看哪些群組可供還原, 以及讓您永久刪除組織不需要的任何群組。

如需詳細資訊, 請參閱[還原已過期或已刪除的群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>單一登入現在可供透過應用程式 Proxy Azure AD 受 SAML 保護的內部部署應用程式 (公開預覽)

**類型：** 新功能  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

您現在可以為內部部署、SAML 驗證的應用程式提供單一登入 (SSO) 體驗, 以及透過應用程式 Proxy 遠端存取這些應用程式。 如需如何使用內部部署應用程式設定 SAML SSO 的詳細資訊, 請參閱[使用應用程式 Proxy (預覽) 進行內部部署應用程式的 saml 單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>要求迴圈中的用戶端應用程式將會中斷, 以改善可靠性和使用者體驗

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

用戶端應用程式可能會在短時間內不正確地發出數百個相同的登入要求。 這些要求 (不論是否成功) 都會導致不佳的使用者體驗, 以及提升 IDP 的工作負載、增加所有使用者的延遲, 以及降低 IDP 的可用性。

此更新會將`invalid_grant` `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`錯誤傳送給在短時間內多次發出重複要求的用戶端應用程式, 超出正常操作的範圍。 遇到此問題的用戶端應用程式應該會顯示互動式提示, 要求使用者重新登入。 如需有關此變更的詳細資訊, 以及如何在遇到此錯誤時修正您的應用程式, 請參閱[驗證的新功能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)。

---

### <a name="new-audit-logs-user-experience-now-available"></a>新的審核記錄使用者體驗現已推出

**類型：** 已變更的功能  
**服務類別：** 報表  
**產品功能：** 監視與報告

我們已建立新的 Azure AD **Audit logs**  頁面, 以協助改善可讀性及搜尋資訊的方式。 若要查看 [新增**審核記錄**] 頁面, 請在 Azure AD 的 [**活動**] 區段中選取 [ **Audit logs** ]。

![[新增審核記錄] 頁面, 包含範例資訊](media/whats-new/audit-logs-page.png)

如需有關 [新增**審核記錄**] 頁面的詳細資訊, 請參閱[Azure Active Directory 入口網站中的 [審核活動報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)]。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>新的警告和指導方針, 可協助防止意外的系統管理員從設定不正確的條件式存取原則

**類型：** 已變更的功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

為了協助防止系統管理員透過設定錯誤的條件式存取原則, 不小心將自己鎖在自己的租使用者中, 我們在 Azure 入口網站中建立了新的警告和更新的指引。 如需新指引的詳細資訊, 請參閱[什麼是 Azure Active Directory 條件式存取中的服務](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)相依性。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>改善行動裝置上的使用者使用經驗

**類型：** 已變更的功能  
**服務類別：** 使用規定  
**產品功能：** 管理

我們已更新現有的使用體驗, 以協助改善您在行動裝置上審查和同意使用規定的方式。 您現在可以放大和縮小、返回、下載資訊, 然後選取 [超連結]。 如需有關更新使用規定的詳細資訊, 請參閱[Azure Active Directory 使用規定功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>有新的 Azure AD 活動記錄下載體驗

**類型：** 已變更的功能  
**服務類別：** 報表  
**產品功能：** 監視與報告

您現在可以直接從 Azure 入口網站下載大量的活動記錄。 此更新可讓您:

- 下載最多250000個數據列。

- 在下載完成後收到通知。

- 自訂您的檔案名。

- 判斷您的輸出格式, JSON 或 CSV。

如需這項功能的詳細資訊[, 請參閱快速入門:使用 Azure 入口網站下載 audit 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大變更：Exchange ActiveSync (EAS) 的條件評估更新

**類型：** 針對變更做規劃  
**服務類別：** 條件式存取  
**產品功能：** 存取控制

我們正在更新 Exchange ActiveSync (EAS) 評估下列條件的方式:

- 根據國家/地區、區域或 IP 位址的使用者位置

- 登入風險

- 裝置平台

如果您先前已在條件式存取原則中使用這些條件, 請注意條件行為可能會變更。 例如, 如果您先前在原則中使用了使用者位置條件, 您可能會發現原則現在會根據使用者的位置略過。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>可設定的 Azure AD SAML 權杖加密 (公開預覽) 

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO

您現在可以設定任何支援的 SAML 應用程式來接收加密的 SAML 權杖。 當設定並與應用程式搭配使用時, Azure AD 會使用從儲存在 Azure AD 中的憑證取得的公開金鑰來加密發出的 SAML 判斷提示。

如需有關設定 SAML 權杖加密的詳細資訊, 請參閱[Configure AZURE AD saml token encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>使用 Azure AD 存取權審查來建立群組或應用程式的存取權審查

**類型：** 新功能  
**服務類別：** 存取權檢閱  
**產品功能：** 管理

您現在可以在群組成員資格或應用程式指派的單一 Azure AD 存取權審查中, 包含多個群組或應用程式。 具有多個群組或應用程式的存取權會使用相同設定進行設定, 而且所有包含的審核者都會同時收到通知。

如需有關如何使用 Azure AD 存取審查來建立存取權審查的詳細資訊, 請參閱[Azure AD 存取審查中的建立群組或應用程式的存取權審核](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2019 年2月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在2019年2月, 我們已將這27個具有同盟支援的新應用程式新增至應用程式庫:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial)、 [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial)、 [FAT 手指](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)、 [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)、 [Oracle 融合 ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial)、 [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial)、 [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial)、 [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial)、 [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial)、 [soloinsight-cloudgate-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)、許可權按一下, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial),[地震](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial),[分享夢想](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods 整合雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial),[知識Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial)、 [OU 校園](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial)、 [Periscope 資料](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)、 [netop portal Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial)、 [Smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial)、 [purecloud by by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)、 [clickup productivity 生產力平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="enhanced-combined-mfasspr-registration"></a>增強的結合 MFA/SSPR 註冊

**類型：** 已變更的功能  
**服務類別：** 自助密碼重設  
**產品功能：** 使用者驗證

為了回應客戶的意見反應, 我們已增強結合的 MFA/SSPR 註冊預覽體驗, 協助您的使用者更快速地為 MFA 和 SSPR 註冊安全性資訊。 

**若要為您的使用者立即開啟增強的體驗, 請遵循下列步驟:**

1. 身為全域系統管理員或使用者系統管理員, 請登入 Azure 入口網站並移至**Azure Active Directory > 使用者設定 > 管理存取面板預覽功能的設定**。 

2. 在 [**可以使用預覽功能來註冊及管理安全性資訊-** 重新整理] 選項的 [使用者] 中, 選擇開啟**所選使用者群組**或**所有使用者**的功能。

在接下來的幾周, 我們將移除開啟尚未開啟的租使用者之舊結合 MFA/SSPR 註冊預覽體驗的功能。

**若要查看您的租使用者是否將移除控制項, 請遵循下列步驟:**

1. 身為全域系統管理員或使用者系統管理員, 請登入 Azure 入口網站並移至**Azure Active Directory > 使用者設定 > 管理存取面板預覽功能的設定**。  

2. 如果**可以使用 [註冊及管理安全性資訊**] 選項的 [預覽功能] 選項設定為 [**無**], 則會從您的租使用者中移除此選項。

無論您先前是否已為使用者開啟舊的結合 MFA/SSPR 註冊預覽體驗, 舊的體驗將會在未來的日期關閉。 因此, 我們強烈建議您儘快移至新的增強式體驗。

如需增強型註冊體驗的詳細資訊, 請參閱[Azure AD 結合的 MFA 和密碼重設註冊體驗的酷炫增強功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>使用者流程的更新原則管理體驗

**類型：** 已變更的功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

我們已更新使用者流程的原則建立和管理程式 (先前稱為內建原則)。 這項新體驗現在是您所有 Azure AD 租使用者的預設值。

您可以在入口網站畫面頂端的 [**傳送意見**反應] 區域中使用笑臉或苦臉圖示, 以提供其他意見反應和建議。

如需新原則管理體驗的詳細資訊, 請參閱[Azure AD B2C 現在已有 JavaScript 自訂和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)的 blog。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>選擇 Azure AD B2C 所提供的特定頁面元素版本

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您現在可以選擇 Azure AD B2C 所提供的特定版本頁面元素。 藉由選取特定版本, 您可以先測試更新, 再將其顯示在頁面上, 而且您可以取得可預測的行為。 此外, 您現在可以選擇強制執行特定頁面版本, 以允許進行 JavaScript 自訂。 若要開啟這項功能, 請移至使用者流程中的 [**屬性**] 頁面。

如需有關選擇特定版本頁面元素的詳細資訊, 請參閱[Azure AD B2C 現在有 JavaScript 自訂和更多新功能的](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)blog。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C (GA) 的可設定使用者密碼需求

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您現在可以為您的終端使用者設定組織的密碼複雜性, 而不需要使用您的原生 Azure AD 密碼原則。 從使用者流程的 [**屬性**] 分頁 (先前稱為您的內建原則), 您可以選擇 [**簡單**] 或 [**強**式] 的密碼複雜性, 或建立一組**自訂**的需求。

如需密碼複雜性需求設定的詳細資訊, 請參閱[在 Azure Active Directory B2C 中設定密碼的複雜性需求](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>自訂品牌驗證體驗的新預設範本

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您可以使用新的預設範本, 位於使用者流程的 [**頁面配置] 分頁**(先前稱為內建原則), 為您的使用者建立自訂的品牌驗證體驗。

如需使用範本的詳細資訊, 請參閱[Azure AD B2C 現在已有 JavaScript 自訂及更多的新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)。

---
