---
title: 自訂原則]-[Azure Active Directory B2C 的開發人員注意事項 |Microsoft Docs
description: 開發人員在使用自訂原則來設定和維護 Azure AD B2C 時的注意事項。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d0be4ec2ed8feb308839377e0494ef2f4b78368
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510203"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 自訂原則的開發人員注意事項

在 Azure Active Directory B2C 自訂原則設定現在已正式推出。 這種組態的方法為目標的進階身分識別開發人員建置複雜的身分識別解決方案。 自訂原則提供強大的身分識別體驗架構的 Azure AD B2C 租用戶中。 進階開發人員使用自訂原則應該計劃投入時間，以完成逐步解說課程和閱讀參考文件的身分識別。

雖然大部分的自訂原則選項可以使用已正式推出，有基礎的功能，例如，技術設定檔類型及內容的定義，軟體生命週期的不同階段的 Api。 更多即將推出。 下表指定的層級更細微的層級的可用性。  

## <a name="features-that-are-generally-available"></a>現已公開推出的功能

- 使用自訂原則來撰寫和上傳自訂驗證使用者旅程。  
    - 逐步將使用者旅程描述為宣告提供者之間的交換。  
    - 定義使用者旅程中的條件式分支。  
- 與 REST API 啟用您的自訂驗證使用者旅程圖的服務交互操作。  
- 與符合 OpenIDConnect 通訊協定的身分識別提供者同盟。  
- 與遵守 SAML 2.0 通訊協定的身分識別提供者同盟。   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自訂原則功能集開發人員的責任

手動設定原則會授與較低層級存取基礎平台的 Azure AD B2C 和一個唯一的信任架構建立的結果。 許多可能的變化的自訂身分識別提供者、 信任關係，與外部服務，以及逐步工作流程整合需要按步就班的方法，來設計和組態。 

使用自訂原則功能集開發人員應遵守下列指導方針：

- 熟悉自訂的原則和金鑰/祕密管理的設定語言。 如需詳細資訊，請參閱 < [TrustFrameworkPolicy](trustframeworkpolicy.md)。 
- 完整掌控案例和自訂整合。 記錄您的工作，並通知您即時站台的組織。  
- 有系統地執行案例測試。 
- 對至少一個開發和測試環境以及一個生產環境遵循軟體開發和分段進行的最佳做法。 
- 掌握與您整合之識別提供者和服務的最新開發資訊。 例如，追蹤密碼的變更，以及服務的排程和未排程變更。 
- 設定主動式監控，並監視生產環境的回應能力。 如需有關如何使用 Application Insights 整合的詳細資訊，請參閱[Azure Active Directory B2C:收集記錄檔](active-directory-b2c-custom-guide-eventlogger-appins.md)。 
- 在 Azure 訂用帳戶中保有最新的連絡人電子郵件地址，並持續回應 Microsoft 即時網站小組的電子郵件。 
- 在 Microsoft 即時網站小組的建議下及時採取動作。

## <a name="terms-for-features-in-public-preview"></a>功能現供公開預覽條款

- 建議您僅針對評估用途使用的公開預覽功能。 
- 服務等級協定 (Sla) 不適用於公開預覽功能。
- 公開預覽功能的支援要求可以透過標準支援管道來提出。 

## <a name="features-by-stage-and-known-issues"></a>依階段和已知問題的功能

自訂原則/識別體驗架構功能正在持續不斷且快速的開發。 下表是功能和元件可用性的索引。

### <a name="identity-providers-tokens-protocols"></a>識別提供者、權杖、通訊協定

| 功能 | 開發 | 預覽 | GA | 注意 |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | 例如，Google +。  |
| IDP-OAUTH2 |  |  | X | 例如，Facebook。  |
| IDP OAUTH1 (twitter) |  | X |  | 例如，Twitter。 |
| IDP OAUTH1 (例如 twitter) |  |  |  | 不支援 |
| IDP-SAML |  |   | X | 例如，Salesforce、 ADFS。 |
| IDP-WSFED | X |  |  |  |
| 信賴憑證者的合作對象 OAUTH1 |  |  |  | 不支援。 |
| 信賴憑證者的合作對象 OAUTH2 |  |  | X |  |
| 信賴憑證者 OIDC |  |  | X |  |
| 信賴憑證者 SAML | X |  |  |  |
| 信賴憑證者 WSFED | X |  |  |  |
| 使用基本的 REST API 和憑證驗證 |  |  | X | 例如，Azure Logic Apps。 |

### <a name="component-support"></a>元件支援

| 功能 | 開發 | 預覽 | GA | 注意 |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| 作為本機目錄的 Azure Active Directory |  |  | X |  |
| 以電子郵件驗證的 azure 電子郵件子系統 |  |  | X |  |
| 多語言支援|  |  | X |  |
| 述詞的驗證 |  |  | X | 例如，密碼複雜性。 |
| 使用協力廠商電子郵件服務提供者 | X |  |  |  |

### <a name="content-definition"></a>內容定義

| 功能 | 開發 | 預覽 | GA | 注意 |
| ------- | ----------- | ------- | -- | ----- |
| 錯誤頁面，api.error |  |  | X |  |
| IDP 選取項目頁面，api.idpselections |  |  | X |  |
| IDP 註冊選擇，api.idpselections.signup |  |  | X |  |
| 忘了密碼，api.localaccountpasswordreset |  |  | X |  |
| 本機帳戶登入，api.localaccountsignin |  |  | X |  |
| 本機帳戶登入，api.localaccountsignup |  |  | X |  |
| MFA 頁面，api.phonefactor |  |  | X |  |
| 自我判斷提示的社交帳戶註冊，api.selfasserted |  |  | X |  |
| 自我判斷設定檔更新，api.selfasserted.profileupdate |  |  | X |  |
| 統一的註冊或登入頁面，api.signuporsignin，使用參數"disableSignup 」 |  |  | X |  |
| JavaScript / 頁面合約 |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 整合

| 功能 | 開發 | 預覽 | GA | 注意 |
| ------- | ----------- | ------- | -- | ----- |
| 查詢字串參數 domain_hint |  |  | X | 可作為宣告，可以傳遞至 IDP。 |
| 查詢字串參數 login_hint |  |  | X | 可作為宣告，可以傳遞至 IDP。 |
| 透過 client_assertion 將 JSON 插入至 UserJourney | X |  |  | 將被取代。 |
| 將 JSON 插入至 UserJourney 作為 id_token_hint |  | X |  | 傳遞 JSON 的前進方法。 |
| 將 IDP 權杖傳遞至應用程式 |  | X |  | 例如，從 Facebook 應用程式。 |

### <a name="session-management"></a>工作階段管理

| 功能 | 開發 | 預覽 | GA | 注意 |
| ------- | ----------- | ------- | -- | ----- |
| SSO 工作階段提供者 |  |  | X |  |
| 外部登入工作階段提供者 |  |  | X |  |
| SAML SSO 工作階段提供者 |  |  | X |  |
| 預設 SSO 工作階段提供者 |  |  | X |  |

### <a name="security"></a>安全性

| 功能 | 開發 | 預覽 | GA | 注意 |
|-------- | ----------- | ------- | -- | ----- |
| 原則機碼產生，手動上傳 |  |  | X |  |
| 原則機碼 - RSA/憑證，密碼 |  |  | X |  |
| 原則上傳 |  |  | X |  |

### <a name="developer-interface"></a>開發人員介面

| 功能 | 開發 | 預覽 | GA | 注意 |
| ------- | ----------- | ------- | -- | ----- |
| Azure 入口網站 IEF UX |  |  | X |  |
| Application Insights UserJourney 記錄 |  | X |  | 用於在開發期間疑難排解。  |
| Application Insights 事件記錄 （透過協調流程步驟） |  | X |  | 用來監視在生產環境中的使用者流程。 |

## <a name="next-steps"></a>後續步驟
[深入了解自訂原則和使用者流程差異](active-directory-b2c-overview-custom.md)。
