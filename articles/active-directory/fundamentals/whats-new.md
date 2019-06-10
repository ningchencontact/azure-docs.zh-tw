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
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a66aefec69a0551f85b11a380c90d1915bd776
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474199"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>獲知時返回此頁面的更新複製並貼上此 URL:`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`到您![RSS 摘要讀取器圖示](./media/whats-new/feed-icon-16x16.png)摘要讀取器。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服務的變更：只在應用程式 Proxy 服務的 TLS 1.2 通訊協定的未來支援

**類型：** 針對變更做規劃  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

為了提供客戶的同級產品中加密，我們要限制只在應用程式 Proxy 服務的 TLS 1.2 通訊協定存取。 這項變更會逐漸即將推出以供客戶已經只使用 TLS 1.2 通訊協定，因此您不應該看到的任何變更。

已被取代的 TLS 1.0 與 TLS 1.1 會於 2019 年 8 月 31 日，但我們將提供其他進階的通知，因此您必須做好這項變更的時間。 若要準備這項變更請確定您的用戶端伺服器和瀏覽器 / 伺服器組合，包括任何使用者用來存取已發佈的應用程式透過應用程式 Proxy 的用戶端，已更新為使用 TLS 1.2 通訊協定來維護應用程式的連線Proxy 服務。 如需詳細資訊，請參閱 < [Azure Active Directory 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用使用量和深入解析報告來檢視您應用程式相關登入資料

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

您現在可以使用 [使用量和 insights] 報告，位於**企業應用程式**區域的 Azure 入口網站中，以取得應用程式為中心的登入資料，包括相關的資訊檢視：

- 頂端會用於您的組織中的應用程式

- 使用最失敗的登入應用程式

- 每個應用程式的主要登入錯誤

如需這項功能的詳細資訊，請參閱[在 Azure Active Directory 入口網站中報告的使用量和深入解析](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>自動化使用者佈建雲端使用 Azure AD 的應用程式

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視與報告

請遵循這些新的教學課程，以自動化建立、 刪除、 使用 Azure AD 佈建服務和更新的下列雲端式應用程式的使用者帳戶：

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

您也可以遵循這個新[Dropbox 教學課程](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)，其中提供如何佈建群組物件的相關資訊。

如需如何更妥善地保護您的組織，透過自動化的使用者帳戶佈建的詳細資訊，請參閱[自動化使用者佈建到 SaaS 應用程式與 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>身分識別安全分數現已推出 Azure AD （正式上市）

**類型：** 新功能  
**服務類別：** N/A  
**產品功能：** 身分識別安全性與保護

您現在可以監視並改善您的身分識別所使用的身分識別的安全性狀態安全分數功能在 Azure AD 中。 身分識別安全分數 」 功能會使用單一的儀表板，可協助您：

- 客觀地測量您的身分識別安全性狀態，根據 1 與 223 之間的分數。

- 規劃您的身分識別的安全性增強功能

- 檢閱您的安全性增強功能的成功

如需身分識別安全性分數功能的詳細資訊，請參閱[什麼是 Azure Active Directory 中的身分識別安全分數？](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>新的應用程式註冊體驗現已可用 （正式上市）

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 開發人員體驗

新[應用程式註冊](https://aka.ms/appregistrations)體驗已現在正式運作。 這個新體驗包含您已熟悉 Azure 入口網站和應用程式註冊入口網站的所有重要功能，並透過為基礎加以改進：

- **更好的應用程式管理。** 而不是跨不同的入口網站中看到您的應用程式，您現在可以看到所有放在一個位置的應用程式。

- **簡化應用程式註冊。** 從上全新面貌的權限選取項目體驗改善的瀏覽體驗，現在正是您更輕鬆地註冊及管理您的應用程式。

- **更詳細的資訊。** 您可以找到更多詳細資料，您的應用程式，包括快速入門指南，以及更多有關。

如需詳細資訊，請參閱 < [Microsoft 身分識別平台](https://docs.microsoft.com/azure/active-directory/develop/)而[應用程式註冊體驗現在已正式推出 ！](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 部落格公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Identity protection 有風險的使用者 API 中新功能

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

我們很高興地宣布，您現在可以使用具風險的使用者 API 擷取使用者的風險歷程記錄，請關閉具風險的使用者，並確認使用者遭到入侵。 這項變更可協助您更有效率地更新您的使用者風險狀態，並了解其風險歷程記錄。

如需詳細資訊，請參閱 <<c0> [ 有風險的使用者 API 參考文件](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>新同盟應用程式可以使用 Azure AD app 資源庫-2019 年中

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在 2019 年，我們新增了這些 21 的新應用程式具有同盟支援新增至應用程式庫：

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)，[實際連結](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)， [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)，[簡單登](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)， [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)， [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)，[Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)， [Marketo 銷售吸引](https://toutapp.com/login)， [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)， [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)， [Meta4 全域 HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)，[配量 Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)，[鈷](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial)， [webMethods API 雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)， [RedFlag](https://pocketstop.com/redflag/)， [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial)，[控制](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial)， [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial)， [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial)， [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)， [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中的改善的群組建立和管理體驗

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們已改進的群組相關的體驗在 Azure AD 入口網站中。 這些增強功能允許系統管理員更有效地管理群組清單中，成員的清單，並提供其他的建立選項。

改善項目包括：

- 基本篩選依成員資格類型和群組類型。

- 新增新的資料行，例如來源和電子郵件地址。

- 多重選取的群組、 成員和擁有者的能力，列出以方便刪除。

- 選擇 電子郵件地址，並在群組建立期間新增擁有者的能力。

如需詳細資訊，請參閱 <<c0> [ 建立基本的群組，並加入使用 Azure Active Directory 的成員](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD 入口網站 （正式上市） 中設定 Office 365 群組命名原則

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

系統管理員現在可以設定為使用 Azure AD 入口網站的 Office 365 群組命名原則。 這項變更有助於強制執行一致的命名慣例，對您組織中使用者所建立或編輯的 Office 365 群組。

您可以在兩個不同的方式設定 Office 365 群組命名原則：

- 定義前置詞或後置詞，會自動新增至群組名稱。

- 上傳一組自訂封鎖字組為您的組織不允許在 群組名稱 (例如，「 CEO，Payroll，HR 」)。

如需詳細資訊，請參閱 <<c0> [ 強制執行的 Office 365 群組命名原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 端點現已提供 Azure AD 活動記錄 （一般可用性）

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視與報告

我們很高興宣布正式運作的 Microsoft Graph API 端點支援的 Azure AD 活動記錄檔。 此版本中，您現在可以的使用 1.0 版的 Azure AD 稽核記錄，以及登入記錄檔的 Api。

如需詳細資訊，請參閱 < [Azure AD 稽核記錄 API 概觀](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>系統管理員現在可以使用條件式存取在合併的註冊程序 （公開預覽）

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護  

系統管理員現在可以建立合併的註冊頁面使用的條件式存取原則。 這包括套用原則以允許註冊，如果：

- 使用者位於受信任的網路。

- 使用者會有較低的登入風險。

- 使用者是受管理的裝置上。

- 使用者同意的組織使用規定 (TOU)。

如需有關條件式存取和密碼重設的詳細資訊，您可以看到[的 Azure AD 條件式存取結合 MFA 和密碼重設註冊體驗部落格文章](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 如需有關在合併的註冊程序的條件式存取原則的詳細資訊，請參閱[條件式存取原則結合註冊](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)。 使用功能的 Azure AD 詞彙的相關資訊，請參閱[Azure Active Directory 使用規定特性](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>新 Azure AD 威脅智慧偵測現已在重新整理的 Azure AD Identity Protection

**類型：** 新功能  
**服務類別：** Azure AD Identity Protection  
**產品功能：** 身分識別安全性與保護

現在可在重新整理的 Azure AD Identity Protection 中使用 azure AD 的威脅情報偵測。 這項新功能，可協助指出特定使用者的不尋常或這是已知的攻擊模式，根據 Microsoft 內部和外部威脅情報與一致的使用者活動。

如需 Azure AD Identity Protection 的重新整理版本的詳細資訊，請參閱[四個主要的 Azure AD Identity Protection 增強功能現為公開預覽版](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)部落格和[什麼是 Azure Active Directory（重新整理） 的身分識別保護嗎？](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 發行項。 如需有關 Azure AD 威脅智慧偵測的詳細資訊，請參閱[Azure Active Directory Identity Protection 風險事件](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)文章。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 權限管理目前可用的 （公開預覽）

**類型：** 新功能  
**服務類別：** Identity Governance  
**產品功能：** Identity Governance

Azure AD 權限管理，現處於公開預覽狀態，可協助客戶委派管理存取套件，其中定義員工或商務合作夥伴如何要求存取、 人員，必須核准，以及他們可以存取的時間長度。 在 Azure AD 中的成員資格和 Office 365 群組、 企業應用程式中的角色指派和 SharePoint Online 網站的角色指派，可以管理存取套件。 深入了解如何管理權利[的 Azure AD 權限管理概觀](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要深入了解 Azure AD 身分識別控管功能，包括特殊權限的身分識別管理和存取權檢閱使用條款，請參閱[什麼是 Azure AD 身分識別控管？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD 入口網站 （公開預覽） 中設定 Office 365 群組命名原則

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

系統管理員現在可以設定為使用 Azure AD 入口網站的 Office 365 群組命名原則。 這項變更有助於強制執行一致的命名慣例，對您組織中使用者所建立或編輯的 Office 365 群組。

您可以在兩個不同的方式設定 Office 365 群組命名原則：

- 定義前置詞或後置詞，會自動新增至群組名稱。

- 上傳一組自訂封鎖字組為您的組織不允許在 群組名稱 (例如，「 CEO，Payroll，HR 」)。

如需詳細資訊，請參閱 <<c0> [ 強制執行的 Office 365 群組命名原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活動記錄檔現已提供 Azure 監視器 （正式上市）

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視與報告

為了協助解決您的 Azure AD 活動記錄檔的視覺效果的相關的意見反應，我們導入 Log Analytics 中的新深入解析功能。 這項功能可協助您深入了解您的 Azure AD 資源使用我們互動式的範本，稱為活頁簿。 這些預先建置的活頁簿可以提供詳細資料的應用程式或使用者，並將包含：

- **登入。** 提供應用程式和使用者，包括登入位置、 使用中作業系統或瀏覽器用戶端和版本，以及成功或失敗的登入數目的詳細資料。

- **舊有的驗證和條件式存取。** 提供的應用程式和使用者使用舊有的驗證，包括 Multi-factor Authentication 條件式存取原則，使用條件式存取原則，應用程式所觸發的使用方式詳細資料，依此類推。

- **登入失敗分析。** 可協助您判斷是否您登入時的錯誤會發生因為使用者動作、 原則的問題或您的基礎結構。

- **自訂報表。** 您可以建立新的或編輯現有的活頁簿，以幫助您自訂您的組織深入解析的功能。

如需詳細資訊，請參閱 <<c0> [ 如何使用 Azure 監視器的活頁簿，針對 Azure Active Directory 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>新同盟應用程式可在 Azure AD 應用程式庫 2019 年 4 月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在 4 月 2019年中，我們已新增這些 21 的新應用程式具有同盟支援新增至應用程式庫：

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)， [HRworks 單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)，[套用](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)， [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)， [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)， [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)， [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)， [MileIQ](https://mileiq.onelink.me/991934284/7e980085)， [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)， [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)， [RStudio連接](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)， [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)， [Mitel 連接](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)， [Alibaba 雲端 (以角色為基礎的 SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)， [Certent 權益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial)， [Sectigo 憑證管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)， [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial)， [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)， [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)， [SurveyMonkey 企業](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)， [Indiggo](https://indiggolead.com/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新的存取權檢閱頻率選項和多個角色選取

**類型：** 新功能  
**服務類別：** 存取權檢閱  
**產品功能：** Identity Governance

在 Azure AD 中的新更新存取權檢閱可讓您：

- 您可存取的頻率檢閱到的變更**半 annually**，除了每週、 每月、 每季，和每年的先前已存在的選項。

- 選取多個 Azure AD 和 Azure 資源角色時建立單一的存取權檢閱。 在此情況下，所有角色都設有相同的設定，並在同一時間的所有檢閱者會收到通知。

如需如何建立存取權檢閱的詳細資訊，請參閱[建立群組的存取權檢閱，或在 Azure AD 中的應用程式存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 的電子郵件警示系統會轉換成，傳送新的電子郵件寄件者資訊，對某些客戶

**類型：** 已變更的功能  
**服務類別：** AD 同步  
**產品功能：** 平台

Azure AD Connect 正在轉換我們的電子郵件警示系統，可能會顯示某些客戶新的電子郵件寄件者。 若要解決此問題，您必須新增`azure-noreply@microsoft.com`到您的組織允許清單，或您將無法繼續收到來自您 Office 365、 Azure 或您的同步處理服務的重要的警示。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN 尾碼變更現在已成功在 Azure AD Connect 的同盟網域之間

**類型：** 已修正  
**服務類別：** AD 同步  
**產品功能：** 平台

您現在可以成功變更使用者的 UPN 尾碼從一個同盟網域在 Azure AD Connect 中的另一個同盟網域。 此修正就表示您應該不會再同步處理週期遇到 FederatedDomainChangeError 錯誤訊息或收到通知電子郵件，「 無法更新此物件在 Azure Active Directory，因為屬性 [FederatedUser.UserPrincipalName] 不是有效的。 更新您的本機目錄服務中的值 」。

如需詳細資訊，請參閱 <<c0> [ 同步處理期間錯誤進行疑難排解](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>更高的安全性，Azure AD （公開預覽） 中使用應用程式保護型條件式存取原則

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

應用程式保護為基礎的條件式存取是現在使用**需要應用程式保護**原則。 這個新的原則，可協助增加您組織的安全性，協助預防：

- 取得應用程式，而不需要 Microsoft Intune 授權的存取權的使用者。

- 無法取得 Microsoft Intune 應用程式保護原則的使用者。

- 使用者沒有已設定的 Microsoft Intune 應用程式保護原則的應用程式的存取。

如需詳細資訊，請參閱 <<c0> [ 如何使用條件式存取的雲端應用程式存取要求應用程式保護原則](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>新的支援，Azure ad 單一登入和條件式存取在 Microsoft Edge （公開預覽）

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

我們已增強我們的 Azure AD 支援 Microsoft edge 的適用性，包括 Azure AD 單一登入和條件式存取提供新的支援。 如果您先前使用過 Microsoft Intune Managed Browser，您現在可以使用 Microsoft Edge 改。

如需有關設定和管理您的裝置和應用程式使用條件式存取的詳細資訊，請參閱 <<c0> [ 需要受管理的裝置存取雲端應用程式使用條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)和[需要核准的用戶端應用程式，適用於雲端使用條件式存取應用程式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。 如需如何使用 Microsoft Edge 與 Microsoft Intune 原則管理存取權的詳細資訊，請參閱[使用 Microsoft Intune 原則保護瀏覽器管理網際網路存取](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>身分識別體驗架構和自訂原則中 Azure Active Directory B2C 的支援現已推出 (GA)

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您現在可以建立在 Azure AD B2C 中，自訂原則，包括下列工作，這是支援大規模在我們的 Azure SLA:

- 建立及使用自訂原則來上傳自訂驗證使用者旅程圖。

- 逐步將使用者旅程描述為宣告提供者之間的交換。

- 定義使用者旅程中的條件式分支。

- 轉換，並將即時決策和通訊中使用的宣告對應。

- 在您的自訂驗證使用者旅程圖中使用 REST API 啟用的服務。 例如，電子郵件提供者、 與 Crm，專屬的授權系統。

- 身為符合 OpenIDConnect 通訊協定的身分識別提供者與同盟。 例如，使用多租用戶 Azure AD、 社交帳戶提供者或雙因素驗證提供者。

如需建立自訂原則的詳細資訊，請參閱 <<c0> [ 在 Azure Active Directory B2C 自訂原則的開發人員注意事項](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom)讀[Alex Simon 部落格文章，包括個案研究](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>新同盟應用程式可在 Azure AD 應用程式庫 2019 年 3 月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在 3 月 2019年中，我們已新增這些 14 的新應用程式具有同盟支援新增至應用程式庫：

[ISEC7 Mobile Exchange 委派](https://www.isec7.com/english/)， [MediusFlow](https://office365.cloudapp.mediusflow.com/)， [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial)， [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial)， [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial)， [說明為基礎的稽核系統](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial)，[精實](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial)， [Powerschool 效能重要](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial)， [Cinode](https://cinode.com/)，[鳶尾花內部網路](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial)， [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial)， [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)， [Confirmit 視野](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial)，[工作](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>新的 Zscaler 和 Atlassian 佈建連接器，在 Azure AD 資源庫-2019 年 3 月

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 協力廠商整合

自動化建立、 更新和刪除下列應用程式的使用者帳戶：

[Zscaler](https://aka.ms/ZscalerProvisioning)， [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning)， [Zscaler One](https://aka.ms/ZscalerOneProvisioning)， [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning)， [Zscaler 三](https://aka.ms/ZscalerThreeProvisioning)， [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning)， [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

如需如何更妥善地保護您的組織，透過自動化的使用者帳戶佈建的詳細資訊，請參閱[自動化使用者佈建到 SaaS 應用程式與 Azure AD](https://aka.ms/ProvisioningDocumentation)。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>還原和管理 Azure AD 入口網站中已刪除的 Office 365 群組

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

您現在可以檢視和管理您已刪除的 Office 365 群組從 Azure AD 入口網站。 這項變更可協助您查看哪些群組可用於還原，以及可讓您永久刪除您的組織不需要任何群組。

如需詳細資訊，請參閱 <<c0> [ 還原已過期或已刪除的群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>單一登入現在已可透過應用程式 Proxy （公開預覽） 的 Azure AD SAML 保護內部部署應用程式

**類型：** 新功能  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

您現在可以提供單一登入 (SSO) 體驗，以便在內部，SAML 驗證的應用程式，以及對這些應用程式，透過應用程式 Proxy 遠端存取。 如需如何設定您的內部部署應用程式的 SAML SSO 的詳細資訊，請參閱[SAML 單一登入內部部署應用程式使用應用程式 Proxy （預覽）](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>在迴圈中要求的用戶端應用程式將會中斷以改善可靠性和使用者體驗

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

用戶端應用程式的時間在短時間內，可以不正確地發出數以百計的相同的登入要求。 這些要求，不論它們是成功與否，全都會造成不佳的使用者體驗，以及增強的工作負載的增加所有使用者的延遲和降低可用性的 IDP 的 IDP。

此更新會傳送`invalid_grant`錯誤：`AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`發出的時間，超出一般作業的範圍在短時間內多次的重複要求的用戶端應用程式。 發生這個問題的用戶端應用程式應該會顯示互動式提示，要求使用者再次登入。 如需有關這項變更，以及如何修正您的應用程式，如果遇到此錯誤的詳細資訊，請參閱[驗證最新消息？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)。

---

### <a name="new-audit-logs-user-experience-now-available"></a>新稽核記錄檔的使用者體驗現已開放下載

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視與報告

我們建立了新的 Azure AD**稽核記錄檔**頁面上，若要改善可讀性和搜尋您的資訊。 若要查看新**稽核記錄檔**頁面上，選取**稽核記錄檔**中**活動**的 Azure AD 的區段。

![新的稽核記錄 頁面上，使用範例的資訊](media/whats-new/audit-logs-page.png)

如需有關新**稽核記錄檔**頁面上，請參閱[的稽核活動報告，在 Azure Active Directory 入口網站中的](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>新的警告和指引來協助防止意外的系統管理員鎖定，從 設定錯誤的條件式存取原則

**類型：** 已變更的功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

為了協助防止系統管理員不小心自己鎖定自己的租用戶，透過設定不正確的條件式存取原則，我們建立了新的警告和最新的指引在 Azure 入口網站中。 如需有關新的指導方針的詳細資訊，請參閱[什麼是 Azure Active Directory 條件式存取中的服務相依性](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>中的行動裝置上的使用經驗的改良的使用者條款

**類型：** 已變更的功能  
**服務類別：** 使用規定  
**產品功能：** 控管

我們已更新的使用體驗，以協助改善您在檢閱並同意在行動裝置上的使用規定我們現有的條款。 此外，您現在可以放大和縮小，返回、 下載的詳細資訊，並選取超連結。 如需已更新使用規定的詳細資訊，請參閱[Azure Active Directory 使用規定特性](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>新的 Azure AD 活動記錄檔下載可用的體驗

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視與報告

您現在可以直接從 Azure 入口網站下載大量的活動記錄檔。 此更新可讓您：

- 下載最多 250,000 個資料列。

- 下載完成之後，收到通知。

- 自訂您的檔案名稱。

- 判斷您的輸出格式，JSON 或 CSV。

如需進一步瞭解這項功能的詳細資訊，請參閱[快速入門：下載使用 Azure 入口網站的稽核報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大變更：條件評估的 Exchange ActiveSync (EAS) 的更新

**類型：** 針對變更做規劃  
**服務類別：** 條件式存取  
**產品功能：** 存取控制

我們正在更新 Exchange ActiveSync (EAS) 的下列條件的評估方式：

- 使用者位置，根據國家/地區、 區域或 IP 位址

- 登入風險

- 裝置平台

如果您先前已在您的條件式存取原則使用這些條件，請注意條件行為可能會變更。 例如，如果您先前在原則中使用使用者位置條件，您可能會發現現在略過的原則會根據您的使用者的位置。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>可設定的 Azure AD SAML 權杖加密 (公開預覽) 

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** SSO

您現在可以設定任何支援的 SAML 應用程式來接收加密的 SAML 權杖。 當設定並與應用程式，Azure AD 會加密使用公開金鑰取自的憑證儲存在 Azure AD 發出的 SAML 判斷提示。

如需有關如何設定您的 SAML 權杖加密的詳細資訊，請參閱 <<c0> [ 設定 Azure AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>使用 Azure AD 存取權檢閱建立群組或應用程式的存取權檢閱

**類型：** 新功能  
**服務類別：** 存取權檢閱  
**產品功能：** 控管

您現在可以包含多個群組，或在單一 Azure AD 中的應用程式存取權的群組成員資格或應用程式指派的檢閱。 存取權檢閱與多個群組或應用程式設定使用相同的設定和所有包含的檢閱者會收到通知，在相同的時間。

如需有關如何建立使用 Azure AD 存取權檢閱存取權檢閱，請參閱[建立在 Azure AD 存取權檢閱的群組或應用程式的存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 應用程式庫推出新的同盟應用程式 - 2019 年 2 月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
在 2 月 2019年中，我們已新增這些 27 的新應用程式具有同盟支援新增至應用程式庫：

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial)， [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial)，[意外](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)， [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)， [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial)， [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial)， [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial)， [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial)， [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial)， [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)，權限按一下[Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial)， [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial)， [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial)， [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial)，[地震](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial)，[共用夢想](https://www.shareadream.org/how-it-works)， [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial)， [webMethods 整合雲端](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)，[知識隨處 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial)，[OU 校園](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial)， [Periscope 資料](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)， [Netop 入口網站](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial)， [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial)， [PureCloud 由 Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)， [ClickUp 產能平台](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="enhanced-combined-mfasspr-registration"></a>增強的合併 MFA/SSPR 註冊

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 使用者驗證

為了回應客戶的意見反應，我們已增強結合的 MFA/SSPR 註冊預覽版體驗，協助使用者更快速地 MFA 和 SSPR 註冊他們的安全性資訊。 

**若要開啟上增強的體驗，為您的使用者現在，請遵循下列步驟：**

1. 身為全域管理員或使用者系統管理員，登入 Azure 入口網站並移至**Azure Active Directory > 使用者設定 > 管理的存取面板的預覽功能設定**。 

2. 在**使用者可以使用預覽功能的註冊和管理的安全性資訊 – 重新整理**選項，請開啟 功能選擇**選取的使用者的群組**或**所有使用者**.

在接下來的幾週，我們將會移除開啟舊結合 MFA/SSPR 註冊預覽版體驗還沒有開啟它的租用戶的能力。

**若要查看您的租用戶，將會移除控制項，請遵循下列步驟：**

1. 身為全域管理員或使用者系統管理員，登入 Azure 入口網站並移至**Azure Active Directory > 使用者設定 > 管理的存取面板的預覽功能設定**。  

2. 如果**可以使用預覽功能註冊和管理的安全性資訊的使用者**選項設定為**無**，選項會移除您的租用戶。

不論是否您先前已開啟舊的結合 MFA/SSPR 註冊預覽與否的使用者體驗，舊的體驗將會關閉在未來日期。 因此，我們強烈建議您儘速移至新的增強體驗。

如需有關增強的註冊體驗的詳細資訊，請參閱[至 Azure AD 的超炫增強功能結合 MFA 和密碼重設註冊體驗](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>更新的使用者流程原則管理體驗

**類型：** 已變更的功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

我們已更新使用者流程 （先前稱為、 內建原則） 更輕鬆的原則建立及管理程序。 這個新體驗現在是您的 Azure AD 租用戶的所有預設值。

您可以提供其他意見反應和建議使用笑臉或苦臉中的圖示**傳送意見反應**入口網站畫面上方區域。

如需以新的原則管理體驗的詳細資訊，請參閱[Azure AD B2C 現在有 JavaScript 自訂以及許多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)部落格。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>選擇 Azure AD B2C 所提供的特定網頁元素版本

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您現在可以選擇特定版本的 Azure AD B2C 所提供的頁面元素。 藉由選取特定版本，您可以測試您的更新，再它們出現在頁面上，而且您可以取得可預期的行為。 此外，您可以立即選擇強制執行特定的頁面版本，以允許 JavaScript 的自訂項目。 若要開啟此功能，請前往**屬性**在使用者流程中的頁面。

如需有關選擇特定版本的頁面元素的詳細資訊，請參閱[Azure AD B2C 現在有 JavaScript 自訂以及許多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)部落格。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>可設定的終端使用者密碼需求 B2C (GA)

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您現在可以設定貴組織的密碼複雜性為您的使用者，而不需使用您的原生 Azure AD 密碼原則。 從**屬性**刀鋒視窗中的使用者 （先前稱為內建的原則） 的流程，您可以選擇的密碼複雜性**簡單**或是**強式**，或者您也可以建立**自訂**組的需求。

如需密碼複雜性需求設定的詳細資訊，請參閱[設定 Azure Active Directory B2C 中的密碼複雜度需求](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>自訂品牌化驗證體驗的新預設範本

**類型：** 新功能  
**服務類別：** B2C - 消費者身分識別管理  
**產品功能：** B2B/B2C

您可以使用我們新的預設範本，位於**頁面上配置**刀鋒視窗中，您的使用者流程 （先前稱為內建原則），來建立自訂品牌的使用者的驗證體驗。

如需使用範本的詳細資訊，請參閱[Azure AD B2C 現在有 JavaScript 自訂以及許多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)。

---

## <a name="january-2019"></a>2019 年 1 月

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>使用單次密碼驗證 (公開預覽) 的 Active Directory B2B 共同作業

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C

對於無法透過如 Azure AD、Microsoft 帳戶 (MSA) 或 Google 同盟等方式驗證的 B2B 來賓使用者，我們已加入單次密碼驗證 (OTP)。 這個新的驗證方法表示來賓使用者不需要建立新的 Microsoft 帳戶。 另一方便，兌換邀請或存取共用的資源時，來賓使用者可以要求臨時代碼來傳送電子郵件地址。 使用此組臨時代碼時，來賓使用者可以繼續登入。

如需詳細資訊，請參閱[電子郵件單次密碼驗證 (預覽版)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 和部落格 [Azure AD 可讓任何帳戶的任何使用者順利共用和共同作業](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)。

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>新增 Azure AD 應用程式 Proxy Cookie 設定

**類型：** 新功能  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

我們引進了三項新的 cookie 設定，以供透過應用程式 Proxy 發佈的應用程式使用：

- **使用僅限 HTTP Cookie。** 在您的應用程式 Proxy 存取和工作階段 Cookie 上設定 **HTTPOnly** 旗標。 開啟此設定可提供額外的安全性優點，例如協助防止透過用戶端指令碼複製或修改 Cookie。 我們建議您開啟這個旗標 (選擇 [是]  )，以獲得附加好處。

- **使用安全的 Cookie。** 在您的應用程式 Proxy 存取和工作階段 Cookie 上設定 [安全]  旗標。 開啟此設定可提供額外的安全性優點，例如確定 Cookie 只會透過 TLS 安全通道 (例如 HTTPS) 傳輸。 我們建議您開啟這個旗標 (選擇 [是]  )，以獲得附加好處。

- **使用永續性 Cookie。** 防止存取 Cookie 在關閉 Web 瀏覽器關閉時過期。 這些 Cookie 會在存取權杖的存留期中持續存在。 不過，如果已達到期時間，或是使用者手動刪除 Cookie，便會重設 Cookie。 我們建議您保留預設設定 [否]  ，僅針對程序間不共用 Cookie 的舊版應用程式開啟此設定。

如需新 Cookie 的詳細資訊，請參閱 [Azure Active Directory 中用來存取內部部署應用程式的 Cookie 設定](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD 應用程式庫推出新的同盟應用程式 - 2019 年 1 月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
我們已在 2019 年 1 月將下列這 35 個提供同盟支援的全新應用程式新增至應用程式庫：

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial)， [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial)，[人才調色盤](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial)， [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)， [Cisco 傘](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial)， [Zscaler網際網路存取 」 系統管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial)，[到期提醒](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial)， [InstaVR 檢視器](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial)， [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial)，[動詞](https://app.verb.net/login)， [OpenLattice](https://openlattice.com/agora)， [TheOrgWiki](https://www.theorgwiki.com/signup)，[關閉 Pavaso 數位](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial)， [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial)， [雲端服務 PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial)， [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial)， [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial)， [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)， [CallPlease](https://webapp.callplease.com/create-account/create-account.html)， [GTNexus SSO 系統](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial)， [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial)， [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial)， [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial)， [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial)，[阿瑞斯 for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial)，[適用於 Office 365 的 K2](https://www.k2.com/O365)， [Xledger](https://www.xledger.net/)， [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial)， [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial)， [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)， [Korn 兩者之間互相傳遞 ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial)， [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial)， [Adoddle cSaas 平台](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>新增 Azure AD Identity Protection 增強功能 (公開預覽)

**類型：** 已變更的功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

我們很興奮地宣布，我們在 Azure AD Identity Protection 公開預覽供應項目中新增了下列增強功能，包括：

- 已更新且整合性更高的使用者介面

- 其他 API

- 透過機器學習服務改良風險評估

- 有風險的使用者與有風險的登入間的全產品對照

如需增強功能的詳細資訊，請參閱[什麼是 Azure Active Directory Identity Protection (已重新整理)？](https://aka.ms/IdentityProtectionDocs) 以深入了解相關資訊並透過產品內的提示分享您的想法。

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>為 iOS 及 Android 裝置上的 Microsoft Authenticator 應用程式新增應用程式鎖定功能

**類型：** 新功能  
**服務類別：** Microsoft Authenticator 應用程式  
**產品功能：** 身分識別安全性與保護

若要讓單次密碼、應用程式資訊和應用程式設定更加安全，您可以在 Microsoft Authenticator 應用程式中開啟應用程式鎖定功能。 開啟 [應用程式鎖定] 表示系統會在您每次開啟 Microsoft Authenticator 應用程式時，要求您使用 PIN 碼或生物特徵辨識進行驗證。

如需詳細資訊，請參閱 [Microsoft Authenticator 應用程式常見問題集](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)。

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>增強 Azure AD Privileged Identity Management (PIM) 的匯出功能

**類型：** 新功能  
**服務類別：** Privileged Identity Management  
**產品功能：** Privileged Identity Management

Privileged Identity Management (PIM) 系統管理員現在可以針對特定資源匯出所有作用中且合格的角色指派，其中包含所有子資源的角色指派。 之前系統管理員很難取得訂用帳戶的角色指派完整清單，而且他們必須針對每個特定資源匯出角色指派。

如需詳細資訊，請參閱[在 PIM 中檢視 Azure 資源角色的活動和稽核記錄](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。

---

## <a name="novemberdecember-2018"></a>2018 年 11 月/12月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>從同步處理範圍移除的使用者不會再切換為僅雲端帳戶

**類型：** 已修正  
**服務類別：** 使用者管理  
**產品功能：** 目錄

>[!Important]
>我們已收到訊息，且了解您對此修正並不滿意。 因此，我們已將這項變更還原到可讓您較易於在組織中實作修正的時間點。

我們已修正在 Azure AD 中將 Active Directory Domain Services (AD DS) 物件從同步處理範圍排除，並接著在後續的同步週期移至資源回收桶時，使用者的 DirSyncEnabled 旗標會錯誤地切換至 **False**。 由於此修正的結果，如果從同步處理範圍排除使用者，並在之後從 Azure AD 資源回收桶還原，使用者帳戶會如預期地保留與內部部署 AD 的同步，且無法從雲端管理，因為其授權來源 (SoA) 仍然是內部部署 AD。

在此修正之前，於 DirSyncEnabled 旗標切換為 False 時會發生問題。 這會給人錯誤的印象，認為這些帳戶已被轉換為僅限雲端物件，且該帳戶可在雲端中進行管理。 不過，帳戶仍然保留其 SoA 為內部部署，且所有已同步的屬性 (陰影屬性) 皆來自內部部署 AD。 這種情況會在 Azure AD 中造成多個問題，且其他雲端工作負載 (例如 Exchange Online) 預期將這些帳戶視為從 AD 進行同步處理，但現在行為卻類似僅雲端帳戶。

這時，將 AD 帳戶同步處理真正轉換為僅雲端帳戶的唯一方式，是停用租用戶層級的 DirSync，這會觸發後端作業以傳輸 SoA。 這類 SoA 變更必須 (但不限於) 清除所有與內部部署相關的屬性 (例如 LastDirSyncTime 和陰影屬性)，並且傳送信號給其他雲端工作負載，以使其各自的物件也轉換為僅雲端帳戶。

因此，此修正防止直接更新從 AD 同步處理之使用者的 ImmutableID 屬性 (這在過去的某些案例中是必要的)。 根據設計，Azure AD 中物件的 ImmutableID 顧名思義就是不可變更的。 Azure Active Directory Connect Health 和 Azure AD Connect 同步用戶端實作的新功能，可用來解決這類案例：

- **以分段方式完成大規模更新許多使用者的 ImmutableID**
  
  例如，您需要執行冗長的 AD DS 內部樹系移轉。 解決方案：使用 Azure AD Connect **設定 Source Anchor**，然後隨著使用者移轉，從 Azure AD 將現有的 ImmutableID 值複製到新樹系本機 AD DS 使用者的 DS-Consistency-Guid 屬性。 如需詳細資訊，請參閱[使用 ms-DS-ConsistencyGuid 作為 sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)。

- **一次完成大規模更新許多使用者的 ImmutableID**

  例如，您在實作 Azure AD Connect 時發生錯誤，而現在您需要變更 SourceAnchor 屬性。 解決方案：停用租用戶層級的 DirSync，並清除所有無效的 ImmutableID 值。 如需詳細資訊，請參閱[關閉 Office 365 的目錄同步處理](/office365/enterprise/turn-off-directory-synchronization)。

- **重新比對內部部署使用者與 Azure AD 中的現有使用者** 例如，在 AD DS 中重新建立了某個使用者，在 Azure AD 帳戶中產生重複，而不是重新比對現有的 Azure AD 帳戶 (孤立的物件)。 解決方案：在 Azure 入口網站中 使用 Azure AD Connect Health 以重新對應來源錨點/ImmutableID。 如需詳細資訊，請參閱[孤立物件案例](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>中斷性變更：透過 Azure 監視器更新稽核和登入記錄結構描述

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視與報告

我們目前透過 Azure 監視器發佈稽核和登入記錄資料流，因此您可以將記錄檔與 SIEM 工具或 Log Analytics完美整合。 根據您的意見反應，並準備此功能正式發行的宣佈，我們對結構描述進行下列變更。 這些結構描述變更及其相關文件更新將在 1 月的第一週進行。

#### <a name="new-fields-in-the-audit-schema"></a>稽核結構描述中的新欄位
我們正在新增新的 [操作類型]  欄位，以提供對資源執行的操作類型。 例如，**新增**、**更新**或**刪除**。

#### <a name="changed-fields-in-the-audit-schema"></a>稽核結構描述中變更的欄位
稽核結構描述中的下列欄位會變更：

|欄位名稱|變更內容|舊值|新值|
|----------|------------|----------|----------|
|類別|這以前是 [服務名稱]  欄位。 現在是 [稽核類別]  欄位。 [服務名稱]  已重新命名為 [loggedByService]  欄位。|<ul><li>帳戶佈建</li><li>核心目錄</li><li>自助式密碼重設</li></ul>|<ul><li>使用者管理</li><li>群組管理</li><li>應用程式管理</li></ul>|
|targetResources|包含最高層級的 **TargetResourceType**。|&nbsp;|<ul><li>原則</li><li>應用程式</li><li>使用者</li><li>群組</li></ul>|
|loggedByService|提供產生稽核記錄之服務的名稱。|Null|<ul><li>帳戶佈建</li><li>核心目錄</li><li>自助式密碼重設</li></ul>|
|結果|提供稽核記錄的結果。 在過去這是列舉的，但我們現在會顯示實際值。|<ul><li>0</li><li>1</li></ul>|<ul><li>成功</li><li>失敗</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>登入結構描述中變更的欄位
登入結構描述中的下列欄位會變更：

|欄位名稱|變更內容|舊值|新值|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|這以前是 [conditionalaccessPolicies]  欄位。 現在是 [appliedConditionalAccessPolicies]  欄位。|無變更|無變更|
|conditionalAccessStatus|在登入時提供條件式存取原則狀態的結果。 在過去這是列舉的，但我們現在會顯示實際值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>成功</li><li>失敗</li><li>未套用</li><li>已停用</li></ul>|
|appliedConditionalAccessPolicies: result|在登入時提供個別條件式存取原則狀態的結果。 在過去這是列舉的，但我們現在會顯示實際值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>成功</li><li>失敗</li><li>未套用</li><li>已停用</li></ul>|

如需結構描述的相關詳細資訊，請參閱[解譯 Azure 監視器中的 Azure AD 稽核記錄結構描述 (預覽)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>受監督的機器學習模型和風險分數引擎的身分識別保護增強功能

**類型：** 已變更的功能  
**服務類別：** 身分識別保護  
**產品功能：** 風險分數

對身分識別保護相關使用者和登入風險評估引擎的增強功能，有助於改善使用者風險精確度和涵蓋範圍。 系統管理員可能會注意到，使用者風險層級不再直接與特定偵測的風險層級相關聯，並且有風險的登入事件的數量和等級也會增加。

風險偵測現在由受監督的機器學習服務模型評估，該模型透過使用使用者登入的其他功能和偵測模式來計算使用者風險。 根據此模型，系統管理員可能會發現具有高風險分數的使用者，即使與該使用者相關聯的偵測屬於低或中層級的風險。 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>系統管理員可以使用 Microsoft Authenticator 應用程式重設其密碼 (公開預覽)

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 使用者驗證

Azure AD 系統管理員現在可以使用 Microsoft Authenticator 應用程式通知，或來自任何行動驗證器應用程式或硬體權杖的程式碼重設其密碼。 若要重設其密碼，系統管理員現在可以使用下列兩個方法：

- Microsoft Authenticator 應用程式通知

- 其他行動驗證器應用程式 / 硬體權杖

- 電子郵件

- 撥打電話

- 簡訊

如需使用 Microsoft Authenticator 應用程式重設密碼的相關詳細資訊，請參閱 [Azure AD 自助式密碼重設 - 行動裝置應用程式和 SSPR (預覽)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新的 Azure AD 雲端裝置系統管理員角色 (公開預覽)

**類型：** 新功能  
**服務類別：** 裝置註冊與管理  
**產品功能：** 存取控制

系統管理員可以將使用者指派至新的雲端裝置系統管理員角色，以執行雲端裝置系統管理員工作。 使用者指派的雲端裝置系統管理員角色者可以啟用、停用和刪除 Azure AD 中的裝置，且能在 Azure 入口網站中讀取 Windows 10 BitLocker 金鑰 (如果有的話)。

如需有關角色和權限的詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>使用 Azure AD 中的新活動時間戳記管理裝置 (公開預覽)

**類型：** 新功能  
**服務類別：** 裝置註冊與管理  
**產品功能：** 裝置生命週期管理

我們了解一段時間您必須重新整理，並在 Azure AD 中，以避免過時的裝置在您的環境中淘汰您組織的裝置。 為了協助進行此程序，Azure AD 現在會使用新的活動時間戳記更新您的裝置，協助您管理您的裝置生命週期。

如需如何取得及使用此時間戳記的詳細資訊，請參閱[作法：管理 Azure AD 中的過時裝置](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>系統管理員可以要求使用者接受每個裝置上的使用規定

**類型：** 新功能  
**服務類別：** 使用規定  
**產品功能：** 控管
 
系統管理員現在可以開啟**使用者必須同意每個裝置上**選項會要求使用者接受他們使用您的租用戶每個裝置上的使用規定。

如需詳細資訊，請參閱 <<c0> [ 每一裝置的 Azure Active Directory 使用規定特性使用 > 一節的規定](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>系統管理員可以設定為根據的週期性排程過期的使用規定

**類型：** 新功能  
**服務類別：** 使用規定  
**產品功能：** 控管
 

系統管理員現在可以開啟**到期同意**到期的所有使用者根據您指定的週期性排程的選項，以便將使用規定。 排程可以是每年、每兩年、每季，或每個月。 使用規定到期之後，必須接受使用者。

如需詳細資訊，請參閱 <<c0> [ 新增的 Azure Active Directory 使用規定特性使用 > 一節的詞彙](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>系統管理員可以設定為過期 根據排程每位使用者的使用規定

**類型：** 新功能  
**服務類別：** 使用規定  
**產品功能：** 控管

系統管理員現在可以指定持續時間，該使用者必須接受使用規定。 比方說，系統管理員可以指定使用者必須接受使用規定每隔 90 天。

如需詳細資訊，請參閱 <<c0> [ 新增的 Azure Active Directory 使用規定特性使用 > 一節的詞彙](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)。
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 角色的新 Azure AD Privileged Identity Management (PIM) 電子郵件

**類型：** 新功能  
**服務類別：** Privileged Identity Management  
**產品功能：** Privileged Identity Management
 
使用 Azure AD Privileged Identity Management (PIM) 的客戶現在可以收到每週摘要電子郵件，其中包括過去七天的下列資訊：

- 最高合格和永久角色指派概觀

- 啟用角色的使用者數目

- 指派給 PIM 中角色的使用者數目

- 指派給 PIM 外部角色的使用者數目

- PIM 中「永久的」使用者數目

如需 PIM 和可用電子郵件通知的相關詳細資訊，請參閱 [PIM 中的電子郵件通知](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)。

---

### <a name="group-based-licensing-is-now-generally-available"></a>群組型授權現已正式推出

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

群組型授權不在公開預覽範圍內，而且現在已正式推出。 作為此正式發行的一部分，我們使這項功能更具擴充性，並新增了為單一使用者重新處理群組型授權指派的功能，以及搭配 Office 365 E3/A3 授權使用群組型授權的功能。

如需群組型授權的相關詳細資訊，請參閱[什麼是Azure Active Directory 中的群組型授權？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD 應用程式資源庫中有新的同盟應用程式可用 - 2018 年 11 月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合
 
我們已在 2018 年 11 月將下列這 26 個提供同盟支援的全新應用程式新增至應用程式資源庫：

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial)、[GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)、[Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial)、[eHour](https://getehour.com/try-now)、[Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial)、[Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial)、[DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview)、[Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial)、[Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial)、[Alaya](https://alayagood.com/en/demo/)、[HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial)、[Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial)、[Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge 成員入口網站](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial)、[Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial)、[Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial)、[Plex 應用程式 - 傳統測試](https://test.plexonline.com/signon)、[Plex 應用程式 – 傳統 ](https://www.plexonline.com/signon)、[Plex 應用程式 - UX 測試](https://test.cloud.plex.com/sso)、[Plex 應用程式 – UX](https://cloud.plex.com/sso)、[Plex 應用程式 – IAM](https://accounts.plex.com/)、[CRAFTS - 育兒記錄、出席與財務追蹤系統](https://getcrafts.ca/craftsregistration) 

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---