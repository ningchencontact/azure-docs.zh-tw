---
title: "新功能 Azure Active Directory 版本資訊 | Microsoft Docs"
description: "了解 Azure Active Directory (Azure AD) 的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a30b5d08377594b8ad7e10b63a23e2a9d168af9c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？


> 請訂閱 [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [摘要](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)，以便隨時收到 Azure Active Directory (Azure AD) 的最新訊息。



Azure AD 會持續不斷進行改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

-   最新版本
-   已知問題
-   錯誤修正
-   已被取代的功能
-   方案變更

此頁面會每月更新，因此請定期瀏覽。



## <a name="february-2018"></a>2018 年 2 月
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>改進使用者和群組的管理瀏覽體驗

**類型：**方案變更  
**服務類別：**目錄管理  
**產品功能：**目錄
 

使用者和群組的管理瀏覽體驗已經簡化。 您現在可以從目錄概觀直接瀏覽至所有使用者清單，且更容易存取已刪除的使用者清單。 您也可以從目錄概觀直接瀏覽至所有群組清單，且更容易存取群組管理設定。 此外，從目錄概觀頁面，您還可搜尋使用者、群組、企業應用程式或應用程式註冊。
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>21Vianet (Azure 中國 21Vianet) 所營運的 Microsoft Azure 提供登入和稽核報告

**類型：**新功能  
**服務類別：**主權雲端  
**產品功能：**監視和報告
 

21Vianet (Azure 中國 21Vianet) 執行個體所營運的 Microsoft Azure 現已提供 Azure AD 活動記錄報告。 以下是包含的記錄：

- **登入活動記錄** - 包括與您租用戶相關的所有登入記錄。

- **自助密碼稽核記錄** - 包括所有 SSPR 稽核記錄。

- **目錄管理稽核記錄** - 包括所有目錄管理相關的稽核記錄，例如使用者管理、應用程式管理等。

藉由這些記錄，您便可深入了解您環境的運作情況。 提供的資料可讓您：

- 判斷使用者如何利用您的應用程式和服務。

- 針對會阻止使用者完成其工作的問題進行疑難排解。

如需有關如何使用這些報告的詳細資訊，請參閱 [Azure Active Directory 報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)。
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>使用「報告讀取者」角色 (非管理員角色) 來檢視 Azure AD 活動報告

**類型：**新功能  
**服務類別：**報告  
**產品功能：**監視和報告
 

在對於讓非管理員角色能夠存取 Azure AD 活動記錄的客戶意見反應中，我們已讓具備「報告讀取者」角色的使用者除了能夠使用我們的「圖形 API」之外，也能夠存取 Azure 入口網站內的「登入」和「稽核」活動。 

如需有關如何使用這些報告的詳細資訊，請參閱 [Azure Active Directory 報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)。 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>提供 EmployeeID 宣告作為使用者屬性和使用者識別碼

**類型：**新功能  
**服務類別：**企業應用程式  
**產品功能：**SSO
 

您可以從企業應用程式 UI 設定 **EmployeeID**，作為 SAML 登入應用程式中成員使用者和 B2B 來賓的使用者識別碼和使用者屬性。

如需詳細資訊，請參閱[針對 Azure Active Directory 中的企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>在 Azure AD 應用程式 Proxy 中使用萬用字元來簡化應用程式管理

**類型：**新功能  
**服務類別：**應用程式 Proxy  
**產品功能：**使用者驗證
 

為了讓應用程式部署更簡單並減輕您的系統管理額外負荷，我們現已支援使用萬用字元來發佈應用程式的功能。 若要發佈萬用字元應用程式，您可以依照標準的應用程式發佈流程，但在內部和外部 URL 中使用萬用字元。

如需詳細資訊，請參閱 [Azure Active Directory 應用程式 Proxy 中的萬用字元應用程式](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-wildcard)

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>可支援設定應用程式 Proxy 的新 Cmdlet

**類型：**新功能  
**服務類別：**應用程式 Proxy  
**產品功能：**平台
 

最新版的「AzureAD PowerShell 預覽」模組包含可讓客戶使用 PowerShell 來設定「應用程式 Proxy 應用程式」的新 Cmdlet。

這些新 Cmdlet 包括： 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>可支援設定群組的新 Cmdlet

**類型：**新功能  
**服務類別：**應用程式 Proxy  
**產品功能：**平台
 

最新版的 AzureAD PowerShell 模組包含可管理 Azure AD 中群組的 Cmdlet。 這些 Cmdlet 先前是在 AzureADPreview 模組中提供的，現已新增到 AzureAD 模組中

現已發行而可正式運作的 Group Cmdlet 包括： 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>提供新版 Azure AD Connect

**類型：**新功能  
**服務類別：**AD 同步  
**產品功能：**平台
 

Azure AD Connect 是在 Azure AD 與內部部署資料來源 (包括 Windows Server Active Directory 和 LDAP) 之間同步處理資料時，建議使用的工具。

**重要**
 
這個組建導入了結構描述和同步處理規則變更。 「Azure AD Connect 同步處理服務」會在升級後觸發「完整匯入」和「完整同步處理」步驟。 如需有關如何變更此行為的資訊，請參閱[如何延遲升級之後的完整同步處理](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)。

此版本包含下列更新和變更：

**已修正的問題**

- 修正 [分割區篩選] 頁面切換至下一頁時背景工作上的時間間隔問題。
- 修正在 ConfigDB 自訂動作期間造成存取違規的錯誤 (bug)。
- 修正從 SQL 復原時連線逾時的錯誤 (bug)。
- 修正含 SAN 萬用字元的憑證在進行必要條件檢查時失敗的錯誤 (bug)。
- 修正造成 miiserver.exe 在 AAD 連接器匯出期間當機的錯誤 (bug)。
- 修正當執行 AAD Connect 精靈來變更設定時，錯誤密碼嘗試被記錄在 DC 上的錯誤 (bug)

**新功能和改進**

- 基於 GDPR，我們必須指出客戶與 Microsoft 共用的資料種類 (遙測、健康情況等)、提供詳細線上文件的連結，以及提供讓您變更其喜好設定的方式。  這項新措施加入下列各項：
    - 全新安裝 EULA 頁面上的資料共用和隱私權通知。

    - 升級頁面上的資料共用和隱私權通知。

    - 新的額外工作**隱私權設定**，讓使用者可以變更其喜好設定。
 
- 應用程式遙測 - 系統管理員可以開啟/關閉此類別的資料。

- Azure AD 健康情況資料 - 系統管理員必須瀏覽健康情況入口網站，才能控制其健康情況設定。 一旦變更服務原則，代理程式會讀取並強制執行它。

- 新增裝置回寫設定動作和頁面初始化的進度列。

- 藉由 HTML 報告改善一般診斷並以 ZIP 文字/HTML 報告提供完整資料收集。

- 改善自動升級的可靠性，並新增其他遙測，以確保您可以判斷伺服器的健康情況。

- 限制具特殊權限帳戶對 AD 連接器帳戶的可用權限。 針對新的安裝，精靈會在建立 MSOL 帳戶之後，限制具特殊權限帳戶對 MSOL 帳戶的權限。 這些變更會影響「自動建立」帳戶的相關快速安裝和自訂安裝。

- 將安裝程式變更為在進行 AADConnect 全新安裝時不需要 SA 權限。

- 可針對特定物件的同步處理問題進行疑難排解的新公用程式。 目前，此公用程式會檢查下列各項：

    - 同步處理的使用者物件與 Azure AD 租用戶中使用者帳戶的 UserPrincipalName 不符。
  
    - 是否因為網域篩選而從同步處理篩選物件
  
    - 是否因為組織單位 (OU) 篩選而從同步處理篩選物件

- 可針對特定使用者帳戶同步處理儲存在內部部署 Active Directory 中之目前密碼雜湊的新公用程式。 此公用程式不需要變更密碼。 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>新增支援 Intune 應用程式防護原則的應用程式，以與 Azure AD 應用程式型條件式存取搭配使用

**類型：**已變更的功能  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護
 

我們已新增更多支援應用程式型條件式存取的應用程式。 現在，您可以使用這些已核准的用戶端應用程式，來存取 Office 365 及其他已連線至 Azure AD 的雲端應用程式。

以下是會在二月底前新增的應用程式 

- Microsoft PowerBI

- Microsoft Launcher

- Microsoft Invoicing

如需詳細資訊，請參閱

- [已核准的用戶端應用程式需求](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 應用程式型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>更新行動體驗的使用規定 

**類型：**已變更的功能  
**服務類別：**使用規定  
**產品功能：**治理
 

顯示使用規定時，您現在可以按一下 [檢視時發生問題嗎?請按一下這裡]。 按一下此連結會在您裝置上以原生方式開啟使用規定。 不論文件中的字型大小或裝置的螢幕大小為何，您都可以視需要縮放並閱讀文件。 
 

---
 
## <a name="january-2018"></a>2018 年 1 月
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD App 資源庫中可用的新同盟應用程式 

**類型：**新功能  
**服務類別：**企業應用程式  
**產品功能：**協力廠商整合
 

在 2018 年 1 月，於應用程式庫中新增了下列支援同盟的新應用程式：

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698) \(機器翻譯\)、[OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660) \(機器翻譯\)、[Dealpath](https://go.microsoft.com/fwlink/?linkid=863526) \(機器翻譯\)、[IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699) \(機器翻譯\) 及 [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701)。

如需所有可用教學課程的完整概觀，請參閱 [SaaS 應用程式與 Azure Active Directory 的整合](https://aka.ms/appstutorial)。
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>偵測到有額外風險的登入

**類型：**新功能  
**服務類別：**身分識別保護  
**產品功能：**身分識別安全性與保護
 

您針對偵測到風險事件所獲得的深入解析會與您的 Azure AD 訂用帳戶息息相關。 使用 Azure AD Premium P2 版本時，您會獲得有關所有基礎偵測的最詳細資訊。

使用 Azure AD Premium P1 版本時，您授權未涵蓋的偵測會顯示為「偵測到有額外風險的登入」風險事件。

如需詳細資訊，請參閱 [Azure Active Directory 風險事件](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)。
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>從使用者存取面板中隱藏 Office 365 應用程式

**類型：**新功能  
**服務類別：**我的應用程式  
**產品功能：**SSO
 

您現在可以透過新的使用者設定，以更好的方式管理 Office 365 應用程式在使用者存取面板上的顯示方式。 如果您偏好只在 Office 入口網站中顯示 Office 應用程式，此選項將有助於減少使用者存取面板中的應用程式數量。 此設定位於 [使用者設定] 中，並且標示為 [使用者只能在 Office 365 入口網站中看見 Office 365 應用程式]。
 

如需詳細資訊，請參閱[從 Azure Active Directory 的使用者體驗中隱藏應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>啟用無縫應用程式登入功能，可直接從應用程式的 URL 進行密碼 SSO 

**類型：**新功能  
**服務類別：**我的應用程式  
**產品功能：**SSO
 

現在透過便利的工具即可使用 [我的應用程式] 瀏覽器擴充功能，此工具會以捷徑的形式在您的瀏覽器中提供 [我的應用程式] 單一登入功能。 安裝之後，使用者將會在其瀏覽器中看到一個鬆餅圖示，可讓使用者快速存取應用程式。 使用者現在可以利用：

- 此功能從應用程式的登入頁面直接登入以密碼 SSO 為基礎的應用程式
- 使用快速搜尋功能來啟動任何應用程式
- 擴充功能所提供的最近使用過的應用程式捷徑
- 此擴充功能適用於 Edge、Chrome 及 Firefox。
 
如需詳細資訊，請參閱[我的應用程式安全登入擴充功能](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension)。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 傳統入口網站中的 Azure AD 系統管理體驗已淘汰

**類型：**已被取代   
**服務類別：**Azure AD  
**產品功能：**目錄
 

Azure 傳統入口網站中的 Azure AD 系統管理體驗自 2018 年 1 月 8 日起已淘汰。 Azure 傳統入口網站本身也隨之一起淘汰。 接下來，您應該使用 [Azure AD 系統管理中心](https://aad.portal.azure.com)來進行所有以入口網站為基礎的 Azure AD 系統管理。
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor Web 入口網站已淘汰

**類型：**已被取代  
**服務類別：**Azure AD  
**產品功能：**目錄
 

PhoneFactor Web 入口網站自 2018 年 1 月 8 日起已淘汰。 此入口網站是用來進行 MFA 伺服器的系統管理，但這些功能已移至位於 portal.azure.com 的 Azure 入口網站。 

MFA 設定位於：**Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>取代 Azure AD 報告


**類型：**已被取代  
**服務類別：**報告  
**產品功能：**身分識別生命週期管理  


在新的「Azure Active Directory 系統管理」主控台正式運作且現在已有適用於活動和安全性報告的新 API 之後，"/reports" 端點底下的報告 API 自 2017 年 12 月 31 日結束起已淘汰。


**可用的內容？**

在轉換至新系統管理主控台的過程中，我們提供了 2 個新 API 來擷取 Azure AD 活動記錄。 除了提供更豐富的稽核和登入活動外，新的 API 集還提供更豐富的篩選和排序功能。 現在可透過 Microsoft Graph 的 Identity Protection 風險事件 API 來存取先前透過安全性報告所使用的資料。

如需詳細資訊，請參閱

- [開始使用 Azure Active Directory 報告 API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>2017 年 12 月
 

### <a name="terms-of-use-in-the-access-panel"></a>存取面板中的使用規定

**類型：**新功能  
**服務類別：**使用規定  
**產品功能：**治理/合規性
 
您現在可以移至「存取面板」來檢視先前所接受的使用規定。

請遵循下列步驟：

1. 前往 [MyApp 入口網站](https://myapps.microsoft.com)並登入。

2. 在右上角中，選取您的名稱，然後從清單中選取 [設定檔]。 

3. 在您的 [設定檔] 上，選取 [檢閱使用規定]。 

4. 現在您可以檢閱您已接受的使用規定。 

如需詳細資訊，請參閱 [Azure AD 使用規定特性 (預覽)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)。
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>新的 Azure AD 登入體驗

**類型：**新功能  
**服務類別：**Azure AD  
**產品功能：**使用者驗證
 
Azure AD 和 Microsoft 帳戶身分識別系統 UI 已經過重新設計，具有一致的外觀與風格。 此外，Azure AD 登入頁面會先收集使用者名稱，然後在第二個畫面收集認證。

如需詳細資訊，請參閱[新的 Azure AD 登入體驗 (目前為公開預覽)](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) \(英文\)。
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>較少的登入提示次數：適用於 Azure AD 登入的新「讓我保持登入」體驗

**類型：**新功能  
**服務類別：**Azure AD  
**產品功能：**使用者驗證
 
Azure AD 登入頁面上的 [讓我保持登入] 核取方塊已被新的提示取代，此提示會在您成功驗證之後出現。 

如果您對此提示的回應是 [是]，服務就會提供您一個持續性的重新整理權杖。 此行為與在舊體驗中選取 [讓我保持登入] 核取方塊相同。 針對同盟租用戶，此提示會在您已成功向同盟服務驗證之後顯示。

如需詳細資訊，請參閱[較少的登入提示次數：適用於 Azure AD 登入的新「讓我保持登入」體驗 (預覽)](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) \(英文\)。 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>新增設定以要求在接受使用規定前先展開使用規定

**類型：**新功能  
**服務類別：**使用規定  
**產品功能：**治理/合規性
 
一個系統管理員選項，可要求其使用者在接受使用規定之前，先展開該規定。

請選取 [開啟] 或 [關閉] 來要求使用者展開使用規定。 [開啟] 設定會要求使用者必須先檢視使用規定，才能接受該規定。

如需詳細資訊，請參閱 [Azure AD 使用規定特性 (預覽)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)。
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>適用於合格角色指派的限域啟用

**類型：**新功能  
**服務類別：**Privileged Identity Management  
**產品功能：**Privileged Identity Management
 
您可以使用限域啟用，以比原始指派預設值更少的自主性來啟用合格的 Azure 資源角色指派。 例如，假設您獲指派為租用戶中訂用帳戶的擁有者。 在使用限域啟用的情況下，您最多可以針對該訂用帳戶內包含的 5 個資源 (例如資源群組與虛擬機器) 啟用擁有者角色。 限制啟用範圍可降低對重要 Azure 資源執行不需要之變更的可能性。

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)。
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD App 資源庫中的新同盟應用程式

**類型：**新功能  
**服務類別：**企業應用程式  
**產品功能：**協力廠商整合
 
在 2017 年 12 月，於應用程式庫中新增了下列支援同盟的新應用程式：

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523) \(機器翻譯\)、Adobe Experience Manager、[EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685) \(機器翻譯\)、[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe \(機器翻譯\)、[FactSet](https://go.microsoft.com/fwlink/?linkid=863525) \(機器翻譯\)、[IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517) \(機器翻譯\)、[MOBI](https://go.microsoft.com/fwlink/?linkid=863521)[MobileIron Azure AD 整合](https://go.microsoft.com/fwlink/?linkid=858027) \(機器翻譯\)、[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) \(機器翻譯\)、[SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520) \(機器翻譯\)、[SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519)[Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522) \(機器翻譯\)、WebHR、Zenegy Azure AD 整合。

如需所有可用教學課程的完整概觀，請參閱 [SaaS 應用程式與 Azure Active Directory 的整合](https://aka.ms/appstutorial)。

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 目錄角色的核准工作流程

**類型：**已變更的功能  
**服務類別：**Privileged Identity Management  
**產品功能：**Privileged Identity Management
 
Azure AD 目錄角色的核准工作流程已正式運作。

使用核准工作流程時，特殊權限角色系統管理員可以要求合格角色成員必須先要求角色啟用，才能使用特殊權限角色。 可以將核准責任委派給多個使用者和群組。 合格角色成員會在核准完成且其角色生效時收到通知。

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>傳遞驗證：商務用 Skype 支援

**類型：**已變更的功能  
**服務類別：**驗證 (登入)  
**產品功能：**使用者驗證


傳遞驗證現在支援使用者登入可支援新式驗證 (包括線上和混合式拓撲) 的「商務用 Skype」用戶端應用程式。 

如需詳細資訊，請參閱[以新式驗證支援的商務用 Skype 技術](https://technet.microsoft.com/library/mt803262.aspx) \(英文\)。
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>適用於 Azure RBAC 的 Azure AD Privileged Identity Management 更新 (預覽)

**類型：**已變更的功能  
**服務類別：**Privileged Identity Management  
**產品功能：**Privileged Identity Management
 
有了適用於 Azure 角色型存取控制 (RBAC) 的 Azure AD Privileged Identity Management (PIM) 公開預覽更新，您現在可以：

* 使用恰到好處的系統管理。
* 要求必須經過核准才能啟用資源角色。
* 排定要求必須經過核准才能取得 Azure AD 和 Azure RBAC 角色的未來角色啟用。

 
如需詳細資訊，請參閱[適用於 Azure 資源的 Privileged Identity Management (預覽)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) \(機器翻譯\)。

 
---
 
## <a name="november-2017"></a>2017 年 11 月
 
### <a name="access-control-service-retirement"></a>存取控制服務淘汰



**類型：**方案變更  
**服務類別：**存取控制服務  
**產品功能：**存取控制服務 


 Azure Active Directory 存取控制 (也稱為「存取控制」服務) 將於 2018 年底淘汰。 在未來幾週，我們將會提供包括詳細排程和概要移轉指引的詳細資訊。 您可以在此頁面留言詢問有關「存取控制」服務的任何問題，小組成員將會回答您的留言。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>限制瀏覽器對 Intune Managed Browser 的存取 


**類型：**方案變更  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護




您可以藉由使用 Intune Managed Browser 作為已核准的應用程式，限制瀏覽器對 Office 365 和其他已連線至 Azure AD 之雲端應用程式的存取。 

您現在可以為應用程式型條件式存取設定下列條件：

**用戶端應用程式：**瀏覽器

**變更的影響為何？**

目前，當您使用此條件時，系統會封鎖存取。 當預覽已可供使用時，所有存取都會要求使用 Managed Browser 應用程式。 

請在即將推出的部落格和版本資訊中，查看此功能及更多資訊。 

如需詳細資訊，請參閱 [Azure AD 中的條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>針對 Azure AD 應用程式型條件式存取之新的已核准用戶端應用程式

 
**類型：**方案變更  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護




已計畫將下列應用程式新增至[已核准的用戶端應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)清單：

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/) \(英文\)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


如需詳細資訊，請參閱

- [已核准的用戶端應用程式需求](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 應用程式型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>針對多種語言的使用規定支援



**類型：**新功能    
**服務類別：**使用規定  
**產品功能：**治理/合規性





系統管理員現在可以建立包含多個 PDF 文件的新使用規定。 您能以相對應的語言標記這些 PDF 文件。 系統會根據使用者的喜好設定，顯示相符語言的 PDF。 如果沒有相符項目，則會顯示預設語言。


---
 

### <a name="real-time-password-writeback-client-status"></a>即時密碼回寫用戶端狀態



**類型：**新功能  
**服務類別**自助式密碼重設  
**產品功能：**使用者驗證


 

您現在可以檢閱內部部署密碼回寫用戶端的狀態。 [[密碼重設](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)] 頁面的 [內部部署整合] 區段中有提供此選項。 

如果您在連線至內部部署回寫用戶端時遇到問題，將會看見包含下列資訊的錯誤訊息：

- 有關無法連線至內部部署回寫用戶端之原因的資訊。
- 可協助您解決該問題之文件的連結。 


如需詳細資訊，請參閱[內部部署整合](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)。

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 應用程式型條件式存取 



 
**類型：**新功能  
**服務類別：**Azure AD  
**產品功能：**身分識別安全性與保護





您現在可以藉由使用 [Azure AD 應用程式型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)，限制只有支援 Intune 應用程式防護原則的[已核准用戶端應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)，才能存取 Office 365 和其他已連線至 Azure AD 的雲端應用程式。 系統會使用 Intune 應用程式防護原則，來設定及保護這些用戶端應用程式上的公司資料。

透過結合[應用程式型](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)和[裝置型](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)的條件式存取原則，您將能彈性地保護個人及公司裝置上的資料。

下列條件及控制項現已能搭配應用程式型條件式存取使用：

**支援的平台條件**

- iOS
- Android

**用戶端應用程式條件**

- 行動裝置應用程式和桌面用戶端

**存取控制**

- 需要已核准的用戶端應用程式


如需詳細資訊，請參閱 [Azure AD 應用程式型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)。

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>在 Azure 入口網站中管理 Azure AD 裝置



**類型：**新功能  
**服務類別：**裝置註冊和管理  
**產品功能：**身分識別安全性與保護

 



您現在只要在一個位置，就可以找到所有已連線至 Azure AD 的裝置，以及與裝置相關的活動。 現已推出在 Azure 入口網站中管理所有裝置身分識別和設定的新系統管理體驗。 在這個版本中，您可以︰

- 檢視所有可供在 Azure AD 中進行條件式存取的裝置。
- 檢視屬性，包括您的混合式已加入 Azure AD 的裝置。
- 尋找已加入 Azure AD 之裝置的 BitLocker 金鑰、透過 Intune 管理裝置等等。
- 管理 Azure AD 裝置相關的設定。

如需詳細資訊，請參閱[使用 Azure 入口網站來管理裝置](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)。



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>支援使用 macOS 作為 Azure AD 條件式存取的裝置平台 



**類型：**新功能    
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護 
 

您現在可以在 Azure AD 條件式存取原則中，包含 (或排除) macOS 作為裝置平台條件。 除了將 macOS 新增為支援的裝置平台之外，您也可以：

- **使用 Intune 來註冊和管理 macOS 裝置。** 與其他平台 (例如 iOS 和 Android) 類似，有可供 macOS 用來進行統一註冊的公司入口網站應用程式。 您可以使用適用於 macOS 的新公司入口網站應用程式，向 Intune 註冊裝置，並向 Azure AD 註冊它。
- **確保 macOS 裝置遵守組織在 Intune 中定義的合規性原則。** 您現在可以在 Azure 入口網站上的 Intune 中，設定適用於 macOS 裝置的合規性原則。 
- **限制只有符合規範的 macOS 裝置才能存取 Azure AD 中的應用程式。** 條件式存取原則撰寫提供 macOS 作為個別裝置平台選項。 現在您可以針對在 Azure 中設定的目標應用程式，撰寫 macOS 特定的條件式存取原則。

如需詳細資訊，請參閱

- [使用 Intune 為 macOS 裝置建立裝置合規性原則](https://aka.ms/macoscompliancepolicy)
- [Azure AD 中的條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>適用於 Azure Multi-Factor Authentication 的網路原則伺服器擴充功能 


**類型：**新功能    
**服務類別：**多重要素驗證  
**產品功能：**使用者驗證




適用於 Azure Multi-Factor Authentication 的「網路原則伺服器」擴充功能會藉由使用現有的伺服器，將雲端式多重要素驗證功能新增至驗證基礎結構。 有了「網路原則伺服器」擴充功能，您便可以在現有的驗證流程中新增通話、簡訊或電話應用程式驗證。 您無須安裝、設定及維護新的伺服器。 

這個擴充功能是針對想要保護虛擬私人網路連線但又不想部署 Azure Multi-Factor Authentication Server 的組織所建立。 「網路原則伺服器」擴充功能會作為 RADIUS 與雲端式 Azure Multi-Factor Authentication 之間的配接器，可為同盟或同步的使用者提供第二驗證要素。


如需詳細資訊，請參閱[將現有的網路原則伺服器基礎結構與 Azure Multi-Factor Authentication 整合](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)。

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>還原或永久移除已刪除的使用者


**類型：**新功能    
**服務類別：**使用者管理  
**產品功能：**目錄 



在 Azure AD 系統管理中心中，您現在可以：

- 還原已刪除的使用者。 
- 永久刪除使用者。


**試用此功能：**

1. 在 Azure AD 系統管理中心內，於 [管理] 區段中選取 [[所有使用者](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All)]。 

2. 從 [顯示] 清單，選取 [最近刪除的使用者]。 

3. 選取一或多個最近刪除的使用者，然後將這些使用者還原或是永久刪除。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>針對 Azure AD 應用程式型條件式存取之新的已核准用戶端應用程式

 
**類型：**已變更的功能  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護


下列應用程式已新增至[已核准的用戶端應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)清單：

- Microsoft Planner
- Azure 資訊保護 


如需詳細資訊，請參閱

- [已核准的用戶端應用程式需求](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD 應用程式型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>在條件式存取原則中的控制項之間使用 "OR" 


**類型：**已變更的功能    
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護

 
您現在可以針對條件式存取控制項使用 "OR" (需要其中一個選取的控制項)。 您可以使用此功能建立在存取控制項之間具有 "OR" 的原則。 例如，您可以使用此功能來建立原則，以要求使用者使用多重要素驗證來進行登入，「或」使用符合規範的裝置來進行登入。

如需詳細資訊，請參閱 [Azure AD 條件式存取中的控制項](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)。

 
---

### <a name="aggregation-of-real-time-risk-events"></a>即時風險事件的彙總


**類型：**已變更的功能    
**服務類別：**身分識別保護  
**產品功能：**身分識別安全性與保護


在 Azure AD Identity Protection 中，現在會針對每個風險事件類型，彙總於指定日子源自於相同 IP 位址的所有即時風險事件。 這項變更會限制所顯示的風險事件量，但對使用者安全性並無任何影響。

基礎即時偵測仍然會在使用者每次登入時運作。 如果您有設定登入風險安全性原則以進行多重要素驗證或封鎖存取，在每次發生具風險的登入時，仍然會觸發該原則。

 
---
 




## <a name="october-2017"></a>2017 年 10 月


### <a name="deprecate-azure-ad-reports"></a>取代 Azure AD 報告


**類型：**方案變更  
**服務類別：**報告  
**產品功能：**身分識別生命週期管理  



Azure 入口網站可讓您使用：

- 新的 Azure AD 管理主控台。
- 活動和安全報告的新 API。
 
由於有這些新功能，因此 /reports 端點下的報告 API 已在 2017 年 12 月 10 日停用。 

---

### <a name="automatic-sign-in-field-detection"></a>自動登入欄位偵測


**類型：**固定   
**服務類別：**我的應用程式  
**產品功能：**單一登入  



Azure AD 針對呈現 HTML 使用者名稱和密碼欄位的應用程式，支援自動登入欄位偵測。 [如何自動擷取應用程式的登入欄位](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application)記錄下列步驟。 您可以在藉由在 [Azure 入口網站](http://aad.portal.azure.com) 的 [企業應用程式] 頁面上新增 [非資源庫] 應用程式來尋找此功能。 此外，您也可以在此新應用程式上將 [單一登入] 模式設定為 [密碼單一登入]，輸入 Web URL，然後再儲存頁面。
 
之前因為服務問題，而將此功能暫時停用。 此問題已解決，因此已恢復自動登入欄位偵測功能。

---

### <a name="new-multi-factor-authentication-features"></a>新的多重要素驗證功能


**類型：**新功能  
**服務類別：**多重要素驗證  
**產品功能：**身分識別安全性與保護  



多重要素驗證 (MFA) 是保護您組織不可或缺的環節。 為了使認證更具彈性，並讓使用體驗更加順暢，我們已新增下列功能： 

- 將多重要素挑戰結果直接整合至 Azure AD 登入報告中，包括以程式設計方式存取 MFA 的結果。
- 將 MFA 設定更深入地整合至 Azure 入口網站的 Azure AD 設定體驗中。

在此公開預覽中，MFA 管理和報告功能是核心 Azure AD 設定體驗相當重要的一部分。 現在您可以在 Azure AD 體驗內管理 MFA 管理入口網站功能。

如需詳細資訊，請參閱[在 Azure 入口網站中 MFA 報告的參考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)。 


---

### <a name="terms-of-use"></a>使用規定



**類型：**新功能  
**服務類別：**使用規定  
**產品功能：**治理/合規性  



您可以使用 Azure AD 使用規定向使用者顯示資訊，例如法務或合規性需求的相關免責聲明。

在下列案例中，您可以使用 Azure AD 使用規定：

- 適用於貴組織中所有使用者的一般使用規定
- 依使用者屬性 (例如，醫生與護士或國內員工與國際員工 (藉由動態群組的方式完成)) 區分的特定使用規定
- 適用於存取高影響力商務應用程式 (例如 Salesforce) 的特定使用規定

如需詳細資訊，請參閱 [Azure AD 使用規定](https://docs.microsoft.com/azure/active-directory/active-directory-tou)。


---

### <a name="enhancements-to-privileged-identity-management"></a>針對 Privileged Identity Management 的增強功能


**類型：**新功能  
**服務類別：**Privileged Identity Management  
**產品功能：**Privileged Identity Management  


有了 Azure AD Privileged Identity Management，您便可以管理、控制及監視下列項目對組織內 Azure 資源 (預覽) 的存取行為：

- 訂用帳戶
- 資源群組
- 虛擬機器 

所有在 Azure 入口網站內利用 Azure RBAC 功能的資源，都可以運用 Azure AD Privileged Identity Management 所提供的安全性和生命週期管理功能。

如需詳細資訊，請參閱[適用於 Azure 資源的 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。


---

### <a name="access-reviews"></a>存取權檢閱


**類型：**新功能  
**服務類別：**存取權檢閱  
**產品功能：**治理/合規性  



組織可以使用存取權檢閱 (預覽) 來有效率地管理群組成員資格，以及對企業應用程式的存取權： 

- 您可以重新認證來賓使用者存取權，方法為使用應用程式的存取權和群組的成員資格的存取權檢閱。 檢閱者可以根據存取權檢閱所提供的深入解析，有效率地決定是否要允許來賓繼續存取。
- 您可以利用存取權檢閱，重新認證員工對應用程式的存取權和群組成員資格。

您可以將存取權檢閱控制項收集到與組織相關的程式，以追蹤合規性或與風險相關之應用程式的檢閱。

如需詳細資訊，請參閱 [Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)。


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>從 [我的應用程式] 和 Office 365 應用程式啟動器隱藏協力廠商應用程式



**類型：**新功能  
**服務類別：**我的應用程式  
**產品功能：**單一登入  



您現在可以透過新的**隱藏應用程式**屬性，以更好的方式管理在使用者入口網站上顯示的應用程式。 當出現後端服務的應用程式圖格或重複的圖格，而造成使用者的應用程式啟動器凌亂時，您可以隱藏應用程式來協助改善此情況。 該切換參數位於協力廠商應用程式的 [屬性] 區段，標示為 [是否要向使用者顯示?] 您也可以透過 PowerShell 以程式設計方式隱藏應用程式。 

如需詳細資訊，請參閱[從 Azure AD 的使用者體驗中隱藏協力廠商應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。 


**可用的內容？**

 在轉換至新系統管理主控台的過程中，有兩個可擷取 Azure AD 活動記錄的新 API 可供使用。 新的 API 集合除了能提供更豐富的稽核和登入活動外，還提供更豐富的篩選和排序功能。 現在透過 Microsoft Graph 中的 Identity Protection 風險事件 API，即可存取先前透過安全性報告提供的資料。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>適用於 Identity Manager 的 Hotfix


**類型：**已變更的功能  
**服務類別：**Identity Manager  
**產品功能：**身分識別生命週期管理  



Identity Manager 2016 Service Pack 1 的 Hotfix 彙總套件 (組建 4.4.1642.0) 已自 2017 年 9 月 25 日起可供使用。 此彙總套件：

- 可解決問題並新增增強功能。
- 是一個累積更新，可取代到 Identity Manager 2016 組建 4.4.1459.0 為止的所有 Identity Manager 2016 Service Pack 1 更新。 
- 要求您必須具備 Identity Manager 2016 組建 4.4.1302.0。 

如需詳細資訊，請參閱 [適用於 Identity Manager 2016 Service Pack 1 的 Hotfix 彙總套件 (組建 4.4.1642.0) 已可供使用](https://support.microsoft.com/help/4021562) \(機器翻譯\)。 

---
