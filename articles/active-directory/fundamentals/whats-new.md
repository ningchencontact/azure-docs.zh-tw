---
title: 新增功能？ 版本資訊 - Azure Active Directory | Microsoft Docs
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
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c475bfc6179f3604ed6b83cafc75d34f4359513f
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532845"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？

>藉由複製並貼上此 URL，取得何時要重新流覽此頁面以取得更新的通知： `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 放入 ![RSS 摘要讀取器圖示 ](./media/whats-new/feed-icon-16x16.png) 摘要讀取器。

Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已被取代的功能
- 方案變更

此頁面會每月更新，因此請定期瀏覽。 如果想要尋找超過 6 個月的項目，請至 [Azure Active Directory 的新增功能封存](whats-new-archive.md) (英文)。

---

## <a name="october-2019"></a>2019年10月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD Identity Protection 風險偵測的 identityRiskEvent API 已淘汰  

**類型：** 方案變更  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

為回應開發人員的意見反應，Azure AD Premium P2 訂閱者現在可以使用適用于 Microsoft Graph 的新 riskDetection API，對 Azure AD Identity Protection 的風險偵測資料執行複雜的查詢。 現有的[identityRiskEvent](https://docs.microsoft.com/en-us/graph/api/resources/identityriskevent?view=graph-rest-beta) API 搶鮮版（Beta）版本將會停止傳回**2020 年1月10日**附近的資料。 如果您的組織使用 identityRiskEvent API，您應該轉換至新的 riskDetection API。

如需有關新 riskDetection API 的詳細資訊，請參閱[風險偵測 API 參考檔](https://aka.ms/RiskDetectionsAPI)。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="deprecation-of-the-power-bi-content-packs"></a>取代 Power BI 內容套件

**類型：** 已被取代  
**服務類別：** 報告  
**產品功能：** 監視和報告

Power BI 是從其存放區淘汰內容套件，而從**2019 年10月 1**日起，我們不再支援 Azure AD Power BI 內容套件。 目前使用內容套件的客戶可以繼續使用它，即使基礎 Api 是較舊的版本也一樣。 除了此內容套件之外，您還可以使用 Azure AD 活頁簿（建基於 Log Analytics）來深入瞭解您的 Azure AD 相關服務。

如需活頁簿的詳細資訊，請參閱[如何使用 Azure Active Directory 報表的 Azure 監視器活頁簿](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。 如需有關取代內容套件的詳細資訊，請參閱[宣佈 Power BI 範本應用程式正式](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)運作的 blog 文章。

---

### <a name="my-profile-is-re-naming-and-integrating-with-the-microsoft-office-account-page"></a>我的設定檔正在重新命名，並與 Microsoft Office 帳戶頁面整合

**類型：** 方案變更  
**服務類別：** 我的設定檔/帳戶  
**產品功能：** 共同作業

從10月開始，我的設定檔體驗將會變成我的帳戶。 做為這項變更的一部分，目前顯示的所有**內容都會變更**為 [**我的帳戶**]。 在命名變更和一些設計改進方面，更新的體驗會與 Microsoft Office 帳戶 頁面提供額外的整合。 具體而言，您將能夠從 [**總覽帳戶**] 頁面存取 office 安裝和訂閱，以及 [**隱私權**] 頁面中的 office 相關連絡人喜好設定。

如需有關 [我的設定檔（預覽）] 體驗的詳細資訊，請參閱[我的設定檔（預覽）入口網站總覽](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 入口網站中使用 CSV 檔案大量管理群組和成員（公開預覽）

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們很高興能在 Azure AD 入口網站中宣佈大量群組管理體驗的公開預覽可用性。 您現在可以使用 CSV 檔案和 Azure AD 入口網站來管理群組和成員清單，包括：

- 新增或移除群組中的成員。

- 從目錄下載群組清單。

- 正在下載特定群組的群組成員清單。

如需詳細資訊，請參閱[大量新增成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[大量移除成員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、[大量下載成員清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)和[大量下載群組清單](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>透過新的系統管理員同意端點，現已支援動態同意

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

我們已建立新的系統管理員同意端點來支援動態同意，這對於想要在 Microsoft 身分識別平臺上使用動態同意模型的應用程式很有説明。

如需如何使用這個新端點的詳細資訊，請參閱[使用系統管理員同意端點](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)。

---

### <a name="new-azure-ad-global-reader-role"></a>新增 Azure AD 全域讀取者角色

**類型：** 新功能  
**服務類別：** RBAC  
**產品功能：** 存取控制

從2019年9月24日開始，我們將開始推出名為全域讀取者的新 Azure Active Directory （AD）角色。 此首度發行將從生產和全球雲端客戶（GCC）開始，在10月的全球完成。

全域讀取者角色是全域管理員的唯讀對應。 此角色中的使用者可以跨 Microsoft 365 服務讀取設定和系統管理資訊，但無法採取管理動作。 我們已建立全域讀取者角色，以協助減少組織中全域管理員的數目。 由於全域管理員帳戶的功能強大且容易受到攻擊，因此建議您擁有少於五個全域管理員。 我們建議使用全域讀取者角色進行規劃、審核或調查。 我們也建議將全域讀取者角色與其他有限的系統管理員角色（例如 Exchange 系統管理員）搭配使用，以協助完成工作而不需要全域管理員角色。

「全域讀取者」角色適用于新的 Microsoft 365 系統管理中心、Exchange 系統管理中心、小組系統管理中心、資訊安全中心、合規性中心、Azure AD 系統管理中心，以及裝置管理系統管理中心。

>[!NOTE]
> 在公開預覽開始時，全域讀者角色將無法使用： SharePoint、特殊許可權的存取管理、客戶加密箱、敏感度標籤、小組生命週期、報告 & 通話分析的小組、IP 電話裝置管理和小組應用程式目錄. 這些服務全都適用于未來的角色。

如需詳細資訊，請參閱[Azure Active Directory 中的系統管理員角色許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 應用程式 Proxy 從您的 Power BI 行動版應用程式存取內部部署報表伺服器

**類型：** 新功能  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

Power BI 行動應用程式與 Azure AD 應用程式 Proxy 之間的新整合，可讓您安全地登入 Power BI 行動應用程式，並查看裝載于內部部署 Power BI 報表伺服器上的任何組織報表。

如需 Power BI 行動版應用程式的相關資訊，包括下載應用程式的位置，請參閱[Power BI 網站](https://powerbi.microsoft.com/mobile/)。 如需如何使用 Azure AD 應用程式 Proxy 設定 Power BI 行動應用程式的詳細資訊，請參閱[使用 Azure AD 應用程式 Proxy 啟用 Power BI 行動版的遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell 模組的新版本可供使用

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

新的 Cmdlet 已新增至 AzureADPreview 模組，以協助在 Azure AD 中定義和指派自訂角色，包括：

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect 的新版本

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

我們已發行自動升級客戶的更新版本 Azure AD Connect。 這個新版本包含數個新功能、改進和 bug 修正。 如需這個新版本的詳細資訊，請參閱[Azure AD Connect：版本發行歷程記錄](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure 多重要素驗證（MFA）伺服器（版本8.0.2）現已推出

**類型：** 固定  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

如果您是在2019年7月1日前啟用 MFA Server 的現有客戶，您現在可以下載最新版本的 MFA Server （版本8.0.2）。 在這個新版本中，我們：

- 已修正問題：當 Azure AD 同步處理將使用者從停用變更為啟用時，系統會傳送電子郵件給使用者。

- 已修正問題，讓客戶可以成功升級，同時繼續使用標記功能。

- 已新增科索沃（+ 383）國家/地區代碼。

- 已將一次性略過審核記錄新增至 MultiFactorAuthSvc。

- 改善 Web 服務 SDK 的效能。

- 已修正其他次要錯誤。

自2019年7月1日起，Microsoft 已停止為新的部署提供 MFA 伺服器。 需要多重要素驗證的新客戶應該使用以雲端為基礎的 Azure 多因素驗證。 如需詳細資訊，請參閱[規劃以雲端為基礎的 Azure 多因素驗證部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>規劃變更：已淘汰 Power BI 內容套件

**類型：** 方案變更  
**服務類別：** 報告  
**產品功能：** 監視和報告

從2019年10月1日開始，Power BI 將開始取代所有內容套件，包括 Azure AD Power BI 內容套件。 除了此內容套件之外，您還可以使用 Azure AD 活頁簿，深入瞭解您的 Azure AD 相關服務。 其他活頁簿即將推出，包括有關僅限報表模式中的條件式存取原則、以應用程式同意為基礎的深入解析等等的活頁簿。

如需活頁簿的詳細資訊，請參閱[如何使用 Azure Active Directory 報表的 Azure 監視器活頁簿](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。 如需有關取代內容套件的詳細資訊，請參閱[宣佈 Power BI 範本應用程式正式](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)運作的 blog 文章。

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>群組的增強搜尋、篩選和排序功能可在 Azure AD 入口網站中取得（公開預覽）

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們很高興能在 Azure AD 入口網站中宣佈增強群組相關體驗的公開預覽可用性。 這些增強功能可協助您更有效地管理群組和成員清單，方法是提供：

- 先進的搜尋功能，例如 [群組] 清單上的 [子字串搜尋]。
- 成員和擁有者清單上的先進篩選和排序選項。
- 成員和擁有者清單的新搜尋功能。
- 較精確的群組計數適用于大型群組。

如需詳細資訊，請參閱[管理 Azure 入口網站中的群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>新的自訂角色適用于應用程式註冊管理（公開預覽）

**類型：** 新功能  
**服務類別：** RBAC  
**產品功能：** 存取控制

自訂角色（可透過 Azure AD P1 或 P2 訂用帳戶取得）現在可以協助您提供更細緻的存取權，方法是讓您以特定許可權建立角色定義，然後將這些角色指派給特定的資源。 目前，您可以使用管理應用程式註冊的許可權，然後將角色指派給特定的應用程式，來建立自訂角色。 如需自訂角色的詳細資訊，請參閱[Azure Active Directory （預覽）中的自訂系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)。

如果您需要支援的其他許可權或資源（目前並未看到），您可以將意見反應傳送給我們的[Azure 意見反應網站](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)，我們會將您的要求新增至我們的更新藍圖。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新的布建記錄可協助您監視和疑難排解您的應用程式布建部署（公開預覽）

**類型：** 新功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理

新的布建記錄檔可協助您監視和疑難排解使用者和群組布建部署。 這些新的記錄檔包含下列資訊：

- 已成功在[ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)中建立哪些群組
- 哪些角色是從 Amazon Web Services 匯入[（AWS）](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- 哪些員工未從[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)匯入

如需詳細資訊，請參閱在[Azure Active Directory 入口網站中布建報表（預覽）](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>所有 Azure AD 系統管理員的新安全性報告（公開上市）

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

根據預設，所有 Azure AD 系統管理員很快就能存取 Azure AD 內的新式安全性報告。 在9月底之前，您將能夠使用新式安全性報告頂端的橫幅來回到舊的報告。

新式安全性報告將提供較舊版本的其他功能，包括：

- 先進的篩選和排序
- 大量動作，例如關閉使用者風險
- 確認遭盜用或安全的實體
- 風險狀態，涵蓋：風險、已解除、已補救且已確認遭到入侵
- 新的風險相關偵測（可供 Azure AD Premium 訂閱者使用）

如需詳細資訊，請參閱有[風險的使用者](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report)、有風險的登[入](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)，以及[風險](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report)偵測。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>使用者指派的受控識別適用于虛擬機器和虛擬機器擴展集（公開上市）

**類型：** 新功能  
**服務類別：** 適用于 Azure 資源的受控識別  
**產品功能：** 開發人員體驗

使用者指派的受控識別現在已正式提供虛擬機器和虛擬機器擴展集。 在此過程中，Azure 可以在使用中的訂用帳戶所信任的 Azure AD 租使用者中建立身分識別，並可將其指派給一或多個 Azure 服務實例。 如需使用者指派受控識別的詳細資訊，請參閱[什麼是適用于 Azure 資源的受控識別？](https://aka.ms/azuremanagedidentity)。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>使用者可以使用行動應用程式或硬體權杖（一般可用性）重設其密碼

**類型：** 已變更的功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 使用者驗證

已向您的組織註冊行動應用程式的使用者，現在可以藉由核准來自 Microsoft Authenticator 應用程式的通知，或從行動應用程式或硬體權杖輸入程式碼，來重設自己的密碼。

如需詳細資訊，請參閱[運作方式： Azure AD 自助式密碼重設](https://aka.ms/authappsspr)。 如需使用者體驗的詳細資訊，請參閱[重設您自己的工作或學校密碼總覽](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 會針對代理者案例忽略 MSAL.NET 共用快取

**類型：** 固定  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

從 Azure AD authentication library （ADAL.NET）第5.0.0 版-preview 開始，應用程式開發人員必須[針對 web 應用程式和 Web api，將每個帳戶的一個](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)快取序列化。 否則，某些使用代理者[流程](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)的案例，以及某些 `UserAssertion` 的特定使用案例，可能會導致權限提高。 為避免此弱點，ADAL.NET 現在會忽略適用于代理者案例的 Microsoft authentication library for dotnet （MSAL.NET）共用快取。

如需有關此問題的詳細資訊，請參閱[Azure Active Directory 驗證程式庫權限提高弱點](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年8月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在2019年8月，我們已將下列26個具有同盟支援的新應用程式新增至應用程式庫：

[市政平臺](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial)， [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial)， [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)， [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial)， [Viareport 的 Inativ Portal （歐洲）](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial)， [Azure Databricks](https://azure.microsoft.com/services/databricks)，[資源](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)管理員，學術[出席](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial)，[優先順序對照表](https://sync.appfluence.com/pmwebng/)，[Cousto MySpace](https://cousto.platformers.be/account/login)， [Uploadcare](https://uploadcare.com/accounts/signup/)， [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial)， [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial)， [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial)，[交付媒體™入口網站](https://portal.deliver.media)，[第一線教育](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial)， [F5](https://www.f5.com/products/security/access-policy-manager)， [stashcat ADconnect](https://www.stashcat.com)、[閃爍](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)、 [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial)、 [ProNovos 分析](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)、 [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial)、 [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial)、[依色彩監看](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [、控管、](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [EAB 流覽策略護理](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>有新版本的 AzureAD PowerShell 和 AzureADPreview PowerShell 模組可供使用

**類型：** 已變更的功能  
**服務類別：** 其他  
**產品功能：** 目錄

AzureAD 和 AzureAD Preview PowerShell 模組的新更新可供使用：

- 已將新的 `-Filter` 參數新增至 AzureAD 模組中的 `Get-AzureADDirectoryRole` 參數。 此參數可協助您篩選 Cmdlet 所傳回的目錄角色。
- 新的 Cmdlet 已新增至 AzureADPreview 模組，以協助在 Azure AD 中定義和指派自訂角色，包括：

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure 入口網站中動態群組規則產生器的 UI 改善

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們對動態群組規則產生器提供了一些 UI 改良功能，可在 Azure 入口網站中使用，以協助您更輕鬆地設定新規則，或變更現有的規則。 這項設計改進可讓您建立最多五個運算式的規則，而不只是一個。 我們也已更新裝置屬性清單，以移除已淘汰的裝置屬性。

如需詳細資訊，請參閱[管理動態成員資格規則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule)。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>新的 Microsoft Graph 應用程式許可權可與存取審查搭配使用

**類型：** 已變更的功能  
**服務類別：** 存取權檢閱  
**產品功能：** 身分識別治理

我們引進了新的 Microsoft Graph 應用程式許可權，`AccessReview.ReadWrite.Membership`，可讓應用程式自動建立和抓取群組成員資格和應用程式指派的存取權審查。 此許可權可供您的排程工作或自動化的一部分使用，而不需要登入的使用者內容。

如需詳細資訊，請參閱[如何使用 PowerShell 的 Microsoft Graph 應用程式許可權建立 Azure AD 存取評論的範例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD 活動記錄現在可供中的政府雲端實例使用 Azure 監視器

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告

我們很高興宣佈，Azure 監視器的政府雲端實例現已提供 Azure AD 活動記錄。 您現在可以將 Azure AD 記錄傳送到您的儲存體帳戶或事件中樞，以與 SIEM 工具（例如[Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)、 [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)和[ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)）整合。 

如需設定 Azure 監視器的詳細資訊，請參閱[Azure 監視器中的 Azure AD 活動記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>將您的使用者更新為新的增強式安全性資訊體驗

**類型：** 已變更的功能  
**服務類別：** 驗證（登入）   
**產品功能：** 使用者驗證

在2019年9月25日，我們將關閉舊的非增強式安全性資訊體驗，以註冊和管理使用者安全性資訊，並只開啟新的[增強版](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。 這表示您的使用者將無法再使用舊體驗。

如需增強式安全性資訊體驗的詳細資訊，請參閱我們的系統[管理員檔](https://aka.ms/securityinfodocs) and 我們的[使用者檔](https://aka.ms/securityinfoguide)。

#### <a name="to-turn-on-this-new-experience-you-must"></a>若要開啟此新體驗，您必須：

1. 以全域管理員或使用者系統管理員身分登入 Azure 入口網站。

2. 移至 **Azure Active Directory > 使用者設定 > 管理存取面板預覽功能的設定**。

3. 在 [**使用者可以使用預覽功能來登錄及管理安全性資訊-增強**] 區域中，選取 [已**選取**]，然後選擇一組使用者，或選擇 [**全部**] 以針對租使用者中的所有使用者開啟此功能。

4. 在 [使用者可以使用預覽功能來註冊及管理安全性 * * info * * *] 區域中，選取 [**無**]。

5. 儲存您的設定。

    儲存設定之後，您將無法再存取舊的安全性資訊體驗。

>[!Important]
>如果您未在2019年9月25日之前完成這些步驟，將會自動啟用您的 Azure Active Directory 租使用者以獲得增強的體驗。 如有任何疑問，請在 registrationpreview@microsoft.com 與我們聯繫。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>使用 POST 登入的驗證要求將會更嚴格地進行驗證

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 標準化

從2019年9月2日開始，使用 POST 方法的驗證要求將會更嚴格地針對 HTTP 標準進行驗證。 具體而言，不會再從要求表單值中移除空格和雙引號（"）。 這些變更不應該中斷任何現有的用戶端，而且將有助於確保傳送至 Azure AD 的要求會每次可靠地處理。

如需詳細資訊，請參閱[Azure AD 重大變更通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>規劃變更：應用程式 Proxy 服務更新僅支援 TLS 1。2

**類型：** 方案變更  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

為了協助提供您最強的加密，我們將開始限制只有 TLS 1.2 通訊協定的應用程式 Proxy 服務存取。 這項限制一開始會向已使用 TLS 1.2 通訊協定的客戶推出，因此您不會看到影響。 TLS 1.0 和 TLS 1.1 通訊協定的完整淘汰將于2019年8月31日完成。 仍在使用 TLS 1.0 和 TLS 1.1 的客戶，將會收到針對此變更進行準備的 advanced 通知。

若要在此變更期間維持與應用程式 Proxy 服務的連線，建議您確定您的用戶端-伺服器和瀏覽器伺服器組合已更新為使用 TLS 1.2。 我們也建議您務必包含員工所使用的任何用戶端系統，以存取透過應用程式 Proxy 服務發佈的應用程式。

如需詳細資訊，請參閱在[Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>規劃變更：應用程式庫即將推出設計更新

**類型：** 方案變更  
**服務類別：** 企業應用程式  
**產品功能：** SSO

新的使用者介面變更即將進入 [**加入應用程式**] 分頁之 [元件**庫**] 區域的設計。 這些變更可協助您更輕鬆地找到支援自動布建、OpenID Connect、安全性聲明標記語言（SAML）和密碼單一登入（SSO）的應用程式。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>規劃變更：從 Office 365 IP 位址移除 MFA server IP 位址

**類型：** 方案變更  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

我們會從[Office 365 Ip 位址和 URL Web 服務](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)移除 MFA 伺服器 ip 位址。 如果您目前依賴這些頁面來更新您的防火牆設定，您必須確定也包含使用者入門的**Azure 多因素驗證服務器防火牆需求**一節中記載的 IP 位址清單。 [使用 Azure 多因素驗證服務器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)一文。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>僅限應用程式權杖現在需要用戶端應用程式存在於資源租使用者中

**類型：** 固定  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

在2019年7月26日，我們變更了透過[用戶端認證授](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)與提供僅限應用程式權杖的方式。 先前，應用程式可以取得權杖來呼叫其他應用程式，而不論用戶端應用程式是否在租使用者中。 我們已更新此行為，因此只有存在於資源租使用者中的用戶端應用程式，才能呼叫單一租使用者資源（有時稱為 Web Api）。

如果您的應用程式不在資源租使用者中，您將會收到錯誤訊息，指出，`The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` 若要修正此問題，您必須使用系統[管理員同意端點](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)或[透過 PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)，在租使用者中建立用戶端應用程式服務主體。這可確保您的租使用者已提供應用程式許可權，以在租使用者內操作。

如需詳細資訊，請參閱[驗證的新功能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)。

> [!NOTE]
> 用戶端與 API 之間的現有同意仍不需要。 應用程式仍應執行自己的授權檢查。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>新的無密碼使用 FIDO2 安全性金鑰登入 Azure AD

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

Azure AD 客戶現在可以設定原則來管理其組織之使用者和群組的 FIDO2 安全性金鑰。 使用者也可以自行註冊其安全性金鑰、使用金鑰在網站上登入其 Microsoft 帳戶，同時在具備 FIDO 功能的裝置上，以及登入其已加入 Azure AD 的 Windows 10 裝置。

如需詳細資訊，請參閱為[Azure AD （預覽）啟用無密碼登入](/azure/active-directory/authentication/concept-authentication-passwordless)以取得系統管理員相關資訊，以及[將安全性資訊設定為使用安全性金鑰（預覽）](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key)來取得使用者相關資訊。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD App 資源庫中提供的新同盟應用程式-2019 年7月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年7月，將具有同盟支援的18個新應用程式新增至應用程式庫：

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial)，[鮮圖樣 Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial)，[聰明的 Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial)， [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial)， [Looop](https://www.looop.co/schedule-a-demo/)， [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial)， [MS Azure SSO Access for Ethidex 合規性 Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso)， [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial)， [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)、 [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)、 [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、 [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)、 [TwineSocial](https://twinesocial.com/)、 [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial)、 [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)、 [PharmID WasteWitness](https://www.pharmid.com/)、 [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、 [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視和報告

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶：

- [撥號盤](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [同盟目錄](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>網路安全性群組的新 Azure AD Domain Services 服務標記

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

如果您不想管理長清單的 IP 位址和範圍，可以使用 Azure 網路安全性群組中的新**AzureActiveDirectoryDomainServices**網路服務標籤，以協助保護您 Azure AD Domain Services 虛擬的輸入流量網路子網。

如需這個新服務標記的詳細資訊，請參閱[Azure AD Domain Services 的網路安全性群組](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 的新安全性審查（公開預覽）

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

我們很高興宣佈發行 Azure AD 網域服務安全性審核到公開預覽。 安全性審核透過將安全性審核事件串流至目標資源（包括 Azure 儲存體、Azure Log Analytics 工作區，以及使用 Azure AD 網域服務的 Azure 事件中樞），協助您對驗證服務提供重要的深入解析。入口.

如需詳細資訊，請參閱[啟用 Azure AD Domain Services 的安全性審核（預覽）](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新的驗證方法使用 & insights （公開預覽）

**類型：** 新功能  
**服務類別：** 自助式密碼重設  
**產品功能：** 監視和報告

新的驗證方法使用 & insights 報告可協助您瞭解 Azure 多因素驗證和自助式密碼重設等功能在您的組織中註冊和使用的方式，包括已註冊的數目每項功能的使用者、用來重設密碼的自助式密碼重設頻率，以及重設發生的方法。

如需詳細資訊，請參閱[驗證方法使用 & insights （預覽）](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>所有 Azure AD 系統管理員都可以使用新的安全性報告（公開預覽）

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

所有 Azure AD 系統管理員現在都可以選取現有安全性報告頂端的橫幅，例如**標示為有風險的使用者**報告，以開始使用有風險的**使用者**和有風險的登**入**報告中所示的新安全性體驗. 經過一段時間之後，所有的安全性報告都會從較舊的版本移至新版本，而新的報表會提供您下列額外的功能：

- 先進的篩選和排序

- 大量動作，例如關閉使用者風險

- 確認遭盜用或安全的實體

- 風險狀態，涵蓋：風險、已解除、已補救且已確認遭到入侵

如需詳細資訊，請參閱有[風險的使用者報告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report)和有[風險的登入報告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 的新安全性審查（公開預覽）

**類型：** 新功能  
**服務類別：** Azure AD Domain Services  
**產品功能：** Azure AD Domain Services

我們很高興宣佈發行 Azure AD 網域服務安全性審核到公開預覽。 安全性審核透過將安全性審核事件串流至目標資源（包括 Azure 儲存體、Azure Log Analytics 工作區，以及使用 Azure AD 網域服務的 Azure 事件中樞），協助您對驗證服務提供重要的深入解析。入口.

如需詳細資訊，請參閱[啟用 Azure AD Domain Services 的安全性審核（預覽）](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>使用 SAML/WS-送出新的 B2B 直接同盟（公開預覽）

**類型：** 新功能  
**服務類別：** B2B  
**產品功能：** B2B/B2C

直接同盟可協助您更輕鬆地使用其 IT 受控識別解決方案不 Azure AD 的合作夥伴，方法是使用支援 SAML 或 WS-ADDRESSING 標準的身分識別系統。 在您設定與夥伴的直接同盟關聯性之後，您從該網域邀請的任何新來賓使用者都可以使用其現有的組織帳戶與您共同作業，讓您的來賓使用者體驗更順暢。

如需詳細資訊，請參閱[來賓使用者的直接與 AD FS 和協力廠商提供者的同盟（預覽）](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視和報告

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶：

- [撥號盤](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [同盟目錄](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中新檢查重複的組名

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

現在，當您從 Azure AD 入口網站建立或更新組名時，我們會執行檢查以查看您是否在資源中複製現有的組名。 如果我們判斷該名稱已被另一個群組使用，系統會要求您修改您的名稱。

如需詳細資訊，請參閱在[Azure AD 入口網站中管理群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD 現在支援回復（重新導向） Uri 中的靜態查詢參數

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

Azure AD 的應用程式現在可以針對 OAuth 2.0 要求，使用靜態查詢參數（例如，`https://contoso.com/oauth2?idp=microsoft`）註冊並使用回復（重新導向） Uri。 靜態查詢參數受限於回復 uri 的字串比對，就像回復 URI 的任何其他部分一樣。 如果沒有任何已註冊的字串符合 URL 解碼的重新導向 uri，則會拒絕要求。 如果找到回復 URI，則會使用整個字串來重新導向使用者，包括靜態查詢參數。

動態回復 Uri 仍然是禁止的，因為它們代表安全性風險，而且無法用來在驗證要求中保留狀態資訊。 基於此目的，請使用 `state` 參數。

目前，Azure 入口網站的應用程式註冊畫面仍會封鎖查詢參數。 不過，您可以手動編輯應用程式資訊清單，以在您的應用程式中新增和測試查詢參數。 如需詳細資訊，請參閱[驗證的新功能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Azure AD 的活動記錄（MS Graph Api）現在可透過 PowerShell Cmdlet 取得

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視和報告

我們很高興宣佈，Azure AD 活動記錄（Audit 和登入報告）現在可以透過 Azure AD PowerShell 模組取得。 之前，您可以使用 MS 圖形 API 端點來建立自己的腳本，現在我們已將該功能擴充至 PowerShell Cmdlet。

如需有關如何使用這些 Cmdlet 的詳細資訊，請參閱[適用于報告的 Azure AD PowerShell Cmdlet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>已更新 Azure AD 中 Audit 和登入記錄的篩選器控制項

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告

我們已更新 Audit 和登入記錄報告，因此您現在可以套用各種篩選，而不需要將它們新增為報表畫面上的資料行。 此外，您現在可以決定要在螢幕上顯示的篩選數目。 這些更新會共同合作，讓您的報表更容易閱讀，而且更有範圍滿足您的需求。

如需這些更新的詳細資訊，請參閱[篩選 audit 記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)和[篩選登入活動](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>適用于 Microsoft Graph 的新 riskDetections API （公開預覽）

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

我們很高興宣佈適用于 Microsoft Graph 的新 riskDetections API 現已開放公開預覽。 您可以使用這個新的 API 來查看您組織的身分識別保護相關使用者和登入風險偵測的清單。 您也可以使用此 API 更有效率地查詢您的風險偵測，包括偵測類型、狀態、層級等等的詳細資料。

如需詳細資訊，請參閱[風險偵測 API 參考檔](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2019 年6月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年6月，將具有同盟支援的22個新應用程式新增至應用程式庫：

[AZURE AD SAML 工具](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)組、 [Otsuka Shokai （大塚商會）](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、 [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、 [Azure VPN 用戶端](https://portal.azure.com/)、 [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、 [helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、 [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)、 [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)、 [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/)、 [Skore](https://app.justskore.it/)、[Oracle 雲端基礎結構主控台](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial)， [CyberArk SAML 驗證](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial)， [Scrible Edu](https://www.scrible.com/sign-in/#/create-account)， [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial)， [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial)， [Proptimise OS](https://proptimise.co.uk/software/)， [Vtiger CRM （SAML）](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial)，oracle Access Manager for oracle零售商品、oracle Access Manager for Oracle 電子商務套件、適用于電子商務套件的 Oracle IDCS、Oracle IDCS for PeopleSoft、Oracle IDCS for JD Edwards

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>針對這些新支援的 SaaS 應用程式自動布建使用者帳戶

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視和報告

您現在可以自動為這些新整合的應用程式建立、更新和刪除使用者帳戶：

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

如需如何使用自動使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>查看 Azure AD 布建服務的即時進度

**類型：** 已變更的功能  
**服務類別：** 應用程式佈建  
**產品功能：** 身分識別生命週期管理

我們已更新 Azure AD 布建體驗，以包含新的進度列，顯示您在使用者布建程式中的程度。 這項更新的體驗也會提供在目前週期期間布建的使用者數目，以及已布建至日期的使用者人數的相關資訊。

如需詳細資訊，請參閱[檢查使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)布建的狀態。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>公司商標現在會出現在登出和錯誤畫面上

**類型：** 已變更的功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 使用者驗證

我們已更新 Azure AD，因此您的公司商標現在會出現在 [登出] 和 [錯誤] 畫面上，以及 [登入] 頁面。 您不需要執行任何動作來開啟此功能，Azure AD 只會使用您在 Azure 入口網站**公司商標**區域中已設定的資產。

如需設定公司商標的詳細資訊，請參閱[將商標新增至貴組織的 Azure Active Directory 頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure 多重要素驗證（MFA）伺服器已不再適用于新的部署

**類型：** 已被取代  
**服務類別：** MFA  
**產品功能：** 身分識別安全性與保護

從2019年7月1日起，Microsoft 將不再為新的部署提供 MFA 伺服器。 新客戶若想要在其組織中要求多重要素驗證，現在必須使用雲端式 Azure 多重要素驗證。 在7月1日前啟動 MFA Server 的客戶不會看到變更。 您仍然可以下載最新版本、取得未來的更新，以及產生啟用認證。

如需詳細資訊，請參閱[開始使用 Azure 多因素驗證服務器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)。 如需以雲端為基礎的 Azure 多因素驗證的詳細資訊，請參閱[規劃雲端式 Azure 多因素驗證部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服務變更：未來僅支援應用程式 Proxy 服務上的 TLS 1.2 通訊協定

**類型：** 方案變更  
**服務類別：** 應用程式 Proxy  
**產品功能：** 存取控制

為了協助為我們的客戶提供最高等級的加密，我們會限制只有應用程式 Proxy 服務上的 TLS 1.2 通訊協定才能存取。 這種變更會逐漸推出給已經只使用 TLS 1.2 通訊協定的客戶，因此您不應該看到任何變更。

TLS 1.0 和 TLS 1.1 的淘汰會在2019年8月31日發生，但我們會提供額外的 advanced 通知，讓您有時間準備進行這項變更。 若要為此變更做準備，請確定您的用戶端伺服器和瀏覽器伺服器組合（包括您的使用者用來存取透過應用程式 Proxy 發佈之應用程式的任何用戶端）都會更新為使用 TLS 1.2 通訊協定來維護應用程式的連接Proxy 服務。 如需詳細資訊，請參閱在[Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用 [使用量和深入解析] 報告來查看應用程式相關的登入資料

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視和報告

您現在可以使用 Azure 入口網站的 **企業應用程式** 區域中的 使用量和深入解析 報表，以應用程式為主的登入資料檢視，包括下列資訊：

- 貴組織最常用的應用程式

- 具有最多失敗登入的應用程式

- 每個應用程式的最上層登入錯誤

如需這項功能的詳細資訊，請參閱[Azure Active Directory 入口網站中的使用量和深入解析報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>使用 Azure AD 將使用者布建自動布建至雲端應用程式

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 監視和報告

請遵循這些新的教學課程，使用 Azure AD 布建服務，自動建立、刪除和更新下列雲端式應用程式的使用者帳戶：

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

您也可以遵循這個新的[Dropbox 教學](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)課程，其中提供如何布建群組物件的相關資訊。

如需如何透過自動化的使用者帳戶布建更進一步保護組織安全的詳細資訊，請參閱[使用 Azure AD 自動化 SaaS 應用程式的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>身分識別安全分數現已于 Azure AD 提供（正式運作）

**類型：** 新功能  
**服務類別：** N/A  
**產品功能：** 身分識別安全性與保護

您現在可以使用 Azure AD 中的身分識別安全分數功能來監視及改善您的身分識別安全性狀態。 身分識別安全分數功能會使用單一儀表板來協助您：

- 客觀地根據1到223之間的分數，測量您的身分識別安全性狀態。

- 規劃您的身分識別安全性改進

- 回顧您的安全性改進成果

如需身分識別安全性分數功能的詳細資訊，請參閱[Azure Active Directory 中的身分識別安全分數為何？](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>新的應用程式註冊體驗現已推出（正式運作）

**類型：** 新功能  
**服務類別：** 驗證 (登入)  
**產品功能：** 開發人員體驗

新的[應用程式註冊](https://aka.ms/appregistrations)體驗現已正式推出。 這項新體驗包括您在 Azure 入口網站和應用程式註冊入口網站中熟悉的所有重要功能，並透過下列方式加以改善：

- **更好的應用程式管理。** 您現在可以在同一個位置看到您的所有應用程式，而不是在不同的入口網站中看到您的應用程式。

- **簡化的應用程式註冊。** 從改良的流覽體驗到改頭換面許可權選擇體驗，現在可以更輕鬆地註冊及管理您的應用程式。

- **詳細資訊。** 您可以找到更多有關應用程式的詳細資料，包括快速入門手冊等等。

如需詳細資訊，請參閱[Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/)和[應用程式註冊體驗現已正式推出！](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog 公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>適用于身分識別保護的具風險使用者 API 中可用的新功能

**類型：** 新功能  
**服務類別：** 身分識別保護  
**產品功能：** 身分識別安全性與保護

我們很高興宣佈您現在可以使用具風險的使用者 API 來抓取使用者的風險歷程記錄、解除有風險的使用者，以及確認使用者是否遭到入侵。 這項變更可協助您更有效率地更新使用者的風險狀態，並瞭解其風險歷程記錄。

如需詳細資訊，請參閱有[風險的使用者 API 參考檔](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-5 月2019

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

在5月2019日，我們已將下列21個新的應用程式與同盟支援新增至應用程式庫：

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)、 [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)、 [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)、 [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)、 [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)、 [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)、 [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)、 [Marketo Sales 參與](https://toutapp.com/login)、 [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)、 [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)、 [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)、[量子Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)，[鈷](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial)， [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)， [RedFlag](https://pocketstop.com/redflag/)， [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial)， [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial)， [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial)， [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial)， [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)， [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>改善 Azure AD 入口網站中的群組建立和管理體驗

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

我們已改善 Azure AD 入口網站中的群組相關體驗。 這些改良功能可讓系統管理員更有效地管理群組清單、成員清單，以及提供其他建立選項。

改善項目包括：

- 依成員資格類型和群組類型的基本篩選。

- 加入新的資料行，例如來源和電子郵件地址。

- 可以多重選取群組、成員和擁有者清單以方便刪除。

- 在群組建立期間選擇電子郵件地址及新增擁有者的能力。

如需詳細資訊，請參閱[建立基本群組和使用 Azure Active Directory 新增成員](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>在 Azure AD 入口網站中設定 Office 365 群組的命名原則（公開上市）

**類型：** 已變更的功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

系統管理員現在可以使用 Azure AD 入口網站，為 Office 365 群組設定命名原則。 這項變更有助於針對組織中的使用者所建立或編輯的 Office 365 群組強制執行一致的命名慣例。

您可以透過兩種不同的方式來設定 Office 365 群組的命名原則：

- 定義會自動新增至組名的首碼或尾碼。

- 針對您的組織上傳自訂的封鎖字組，不允許用於組名（例如，「CEO，薪資，HR」）。

如需詳細資訊，請參閱對[Office 365 群組強制執行命名原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 端點現在可供 Azure AD 活動記錄（正式運作）

**類型：** 已變更的功能  
**服務類別：** 報告  
**產品功能：** 監視和報告

我們很高興宣佈 Azure AD 活動記錄的 Microsoft Graph API 端點支援正式運作。 在此版本中，您現在可以使用1.0 版的 Azure AD audit 記錄，以及登入記錄 Api。

如需詳細資訊，請參閱[Azure AD audit LOG API 總覽](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>系統管理員現在可以將條件式存取用於合併的註冊程式（公開預覽）

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護  

系統管理員現在可以建立條件式存取原則，以供結合的註冊頁面使用。 這包括套用原則以允許註冊，如果：

- 使用者位於受信任的網路上。

- 使用者是很低的登入風險。

- 使用者位於受管理的裝置上。

- 使用者同意組織的使用規定（TOU）。

如需有關條件式存取和密碼重設的詳細資訊，您可以查看[Azure AD 結合的 MFA 和密碼重設註冊體驗 blog 文章的條件式存取](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 如需合併註冊程式之條件式存取原則的詳細資訊，請參閱[結合註冊的條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)。 如需 Azure AD 使用規定功能的詳細資訊，請參閱[Azure Active Directory 使用規定功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新的 Azure AD 威脅情報偵測現在可作為 Azure AD Identity Protection 的一部分

**類型：** 新功能  
**服務類別：** Azure AD Identity Protection  
**產品功能：** 身分識別安全性與保護

Azure AD 的威脅情報偵測現在已在更新的 Azure AD Identity Protection 功能中提供。 這種新功能有助於指出特定使用者或活動的異常使用者活動，其與根據 Microsoft 內部和外部威脅情報來源的已知攻擊模式一致。

如需 Azure AD Identity Protection 重新整理版本的詳細資訊，請參閱[現已公開預覽的四個主要 Azure AD Identity Protection 增強功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)，以及[Azure Active Directory Identity Protection 的內容（重新整理）。](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 篇. 如需有關 Azure AD 威脅情報偵測的詳細資訊，請參閱[Azure Active Directory Identity Protection 風險](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)偵測一文。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 的權利管理現已推出（公開預覽）

**類型：** 新功能  
**服務類別：** 身分識別治理  
**產品功能：** 身分識別治理

Azure AD 權利管理（現為公開預覽狀態）可協助客戶委派存取套件的管理，以定義員工和商務合作夥伴如何要求存取權、誰必須核准，以及他們有權存取的時間長度。 存取套件可以管理 Azure AD 和 Office 365 群組中的成員資格、企業應用程式中的角色指派，以及 SharePoint Online 網站的角色指派。 如需有關權利管理的詳細資訊，請參閱[Azure AD 權利管理總覽](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要深入瞭解 Azure AD Identity Governance 功能的廣度，包括 Privileged Identity Management、存取權審查和使用規定，請參閱[什麼是 Azure AD Identity Governance？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 入口網站中設定 Office 365 群組的命名原則（公開預覽）

**類型：** 新功能  
**服務類別：** 群組管理  
**產品功能：** 共同作業

系統管理員現在可以使用 Azure AD 入口網站，為 Office 365 群組設定命名原則。 這項變更有助於針對組織中的使用者所建立或編輯的 Office 365 群組強制執行一致的命名慣例。

您可以透過兩種不同的方式來設定 Office 365 群組的命名原則：

- 定義會自動新增至組名的首碼或尾碼。

- 針對您的組織上傳自訂的封鎖字組，不允許用於組名（例如，「CEO，薪資，HR」）。

如需詳細資訊，請參閱對[Office 365 群組強制執行命名原則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活動記錄現已于 Azure 監視器提供（正式運作）

**類型：** 新功能  
**服務類別：** 報告  
**產品功能：** 監視和報告

為了協助您使用 Azure AD 活動記錄來處理視覺效果的意見反應，我們在 Log Analytics 中引進了新的深入解析功能。 這項功能可協助您使用我們的互動式範本（稱為活頁簿），深入瞭解您的 Azure AD 資源。 這些預先建立的活頁簿可以提供應用程式或使用者的詳細資料，包括：

- 登**入。** 提供應用程式和使用者的詳細資料，包括登入位置、使用中作業系統或瀏覽器用戶端和版本，以及成功或失敗的登入次數。

- **舊版驗證和條件式存取。** 提供使用舊版驗證之應用程式和使用者的詳細資料，包括條件式存取原則所觸發的多重要素驗證使用方式、使用條件式存取原則的應用程式等等。

- **登入失敗分析。** 協助您判斷是否因為使用者動作、原則問題或基礎結構而發生登入錯誤。

- **自訂報表。** 您可以建立新的或編輯現有的活頁簿，以協助自訂您組織的深入解析功能。

如需詳細資訊，請參閱[如何使用 Azure Active Directory 報表的 Azure 監視器活頁簿](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 應用程式庫中可用的新同盟應用程式-2019 年4月

**類型：** 新功能  
**服務類別：** 企業應用程式  
**產品功能：** 協力廠商整合

我們已在2019年4月將下列21個具有同盟支援的新應用程式新增至應用程式庫：

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)、 [hrworks single sign-on 單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)、 [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)、 [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)、 [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)、 [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)、 [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)、 [MileIQ](https://mileiq.onelink.me/991934284/7e980085)、 [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)、 [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)、 [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)， [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)， [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)， [Alibaba Cloud （以角色為基礎的 SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)， [certent equity 權益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial)， [sectigo certificate 憑證管理員](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)， [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial)， [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)， [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)，[SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)、 [Indiggo](https://indiggolead.com/)

如需應用程式的詳細資訊，請參閱[與 Azure Active Directory 整合的 SaaS 應用程式](https://aka.ms/appstutorial)。 如需在 Azure AD 應用程式庫中列出應用程式的詳細資訊，請參閱[在 Azure Active Directory 應用程式庫中列出您的應用程式](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新的存取審查頻率選項和多個角色選取

**類型：** 新功能  
**服務類別：** 存取權檢閱  
**產品功能：** 身分識別治理

Azure AD 存取審查的新更新可讓您：

- 除了先前現有的每週、每月、每季和每年的選項以外，請將存取權評論的頻率變更為**半年**。

- 建立單一存取權審查時，請選取多個 Azure AD 和 Azure 資源角色。 在這種情況下，所有角色都是以相同的設定進行設定，而且所有的審核者都會同時收到通知。

如需有關如何建立存取權審查的詳細資訊，請參閱[在 Azure AD 存取審查中建立群組或應用程式的存取權審查](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 電子郵件警示系統正在轉換，傳送部分客戶的新電子郵件寄件者資訊

**類型：** 已變更的功能  
**服務類別：** AD 同步  
**產品功能：** 平台

Azure AD Connect 正在轉換我們的電子郵件警示系統，可能會向一些客戶顯示新的電子郵件寄件者。 若要解決此情況，您必須將 `azure-noreply@microsoft.com` 新增至您組織的允許清單，否則您將無法繼續接收來自 Office 365、Azure 或同步服務的重要警示。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Azure AD Connect 中的同盟網域之間的 UPN 尾碼變更現在成功

**類型：** 固定  
**服務類別：** AD 同步  
**產品功能：** 平台

您現在可以在 Azure AD Connect 中，成功將使用者的 UPN 尾碼從一個同盟網域變更為另一個同盟網域。 這項修正表示您應該不會在同步處理迴圈期間遇到導致發生 federateddomainchangeerror 錯誤訊息，也不會收到通知電子郵件，指出「無法在 Azure Active Directory 中更新此物件，因為屬性 [FederatedUser. UserPrincipalName] 無效。 更新本機目錄服務中的值」。

如需詳細資訊，請參閱針對[同步處理期間的錯誤進行疑難排解](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>在 Azure AD 中使用以應用程式保護為基礎的條件式存取原則來提高安全性（公開預覽）

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

以應用程式保護為基礎的條件式存取現在可使用「**需要應用程式保護**原則」來取得。 這項新原則有助於避免下列情況來增加貴組織的安全性：

- 使用者在沒有 Microsoft Intune 授權的情況下，取得應用程式的存取權。

- 使用者無法取得 Microsoft Intune 的應用程式保護原則。

- 使用者在沒有設定 Microsoft Intune 應用程式保護原則的情況下，取得應用程式的存取權。

如需詳細資訊，請參閱[如何要求應用程式保護原則以使用條件式存取進行雲端應用程式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 中 Azure AD 單一登入和條件式存取的新支援（公開預覽）

**類型：** 新功能  
**服務類別：** 條件式存取  
**產品功能：** 身分識別安全性與保護

我們已增強 Microsoft Edge 的 Azure AD 支援，包括提供 Azure AD 單一登入和條件式存取的新支援。 如果您先前已使用 Microsoft Intune Managed Browser，您現在可以改用 Microsoft Edge。

如需使用條件式存取來設定及管理裝置和應用程式的詳細資訊，請參閱要求使用條件式存取的[雲端應用程式存取受管理的裝置](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)，並[要求透過條件式存取進行雲端應用程式存取的核准用戶端應用程式](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 如需有關如何使用 Microsoft Edge 與 Microsoft Intune 原則來管理存取權的詳細資訊，請參閱[使用 Microsoft Intune 受原則保護的瀏覽器來管理網際網路存取](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---
