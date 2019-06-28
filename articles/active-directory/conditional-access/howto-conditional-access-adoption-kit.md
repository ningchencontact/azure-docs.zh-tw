---
title: 條件式存取採用套件-Azure Active Directory
description: 採用 Azure AD 條件式存取資源的存取權
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387569"
---
#  <a name="adopting-azure-ad-conditional-access"></a>採用 Azure AD 條件式存取

在行動優先、 雲端優先的世界裡，使用者可以從任何位置使用不同類型的裝置和應用程式存取您組織的資源。 如此一來，只將焦點放在誰可以存取的資源已不再足夠。 您可以控制誰可以存取，並找出使用者所在並正在使用哪一種裝置，以及更多。

若要提供這項控制**Azure Active Directory (AD) 條件式存取**可讓您指定的任何使用者必須符合為應用程式，例如 Multi-factor Authentication (MFA) 的存取條件。 使用條件式存取原則控制如何授權的使用者 （已授與存取權的雲端應用程式的使用者） 存取雲端應用程式在特定情況下的。 請參閱[什麼是 Azure Active Directory 中的條件式存取](overview.md#conditional-access-policies)如需詳細資訊。

## <a name="key-benefits"></a>主要權益

使用 Azure AD 條件式存取的主要優點為：

* **提高生產力：** 條件式存取 (CA) 原則可讓您為目標的系統會提示使用者使用 MFA，封鎖，存取點，或使用受信任的裝置所需。 例如，您可以設定原則，例如只到離開公司網路時應用程式需要 mfa 的使用者。 減少 MFA 要求會保留使用者的生產力比它們是否 mfa 每次登入。 此外，Azure AD 條件式存取可讓您指定原則的每個使用者為基礎，而且也會建立應用程式特定的原則。
* **管理風險：** 啟用條件式存取原則提供雲端規模的身分識別保護、 以風險為基礎的存取控制功能，與原生的多重要素驗證的支援。 使用 identity protection 中結合條件式存取，可讓您定義當應用程式的存取會遭到封鎖，或閘道。
* **解決相容性和控管：** 因為它支援每個應用程式存取要求執行的原生的稽核記錄檔，稽核存取要求和核准的應用程式，並了解整體的應用程式使用情況是您更輕鬆與 Azure AD。 稽核會包括要求者的身分識別、 要求的日期、 業務正當理由、 核准狀態以及核准者身分識別。 這項資料也會提供 API，這樣可以將此資料匯入到安全性事件和事件監視 (SIEM) 系統選擇。
* **管理成本：** 移至 Azure AD 的存取原則可減少依賴自訂或內部部署解決方案 Active Directory Federation Services (ADFS) 例如條件式存取，降低執行該基礎結構的成本。

## <a name="customer-case-studies"></a>客戶案例研究

探索如何大部分的組織使用 Azure AD 條件式存取來定義和實作自動化的存取控制決定存取以條件為基礎的雲端應用程式。 下列的精選的案例示範如何符合這些客戶需求。

* [**Wipro**磁碟機與 Microsoft 雲端安全性工具，來改善客戶參與的行動產能。](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) 在 Azure AD 中的條件式存取原則已啟用共用文件、 資源和應用程式與受信任的外部實體---可以使用他們自己的認證---美元，同時維持其本身的公司資料控制權的公司。
* [**Accenture**與 Microsoft Cloud App security 保護其移動到雲端](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)Accenture 正在評估[Conditional Access App Control](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) Cloud App Security 使用 Azure 作用中的功能Directory 條件式存取根據特定條件限制應用程式存取權限。 LePenske 說，這項功能很有用，說： 啟用唯讀檔案存取，同時禁止次要的下載項目。
* [**Aramex**傳遞有限-邏輯和運輸的全球性公司使用身分識別和存取管理解決方案建立連接到雲端的 office](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)。 確保安全的存取是特別難以 Aramex 的遠端員工。 該公司現在套用條件式存取，讓這些遠端的員工存取 SaaS 應用程式從網路外部。 條件式存取規則會決定是否要強制執行 Multi-factor Authentication，提供適當的人員正確的存取權。

若要深入了解客戶和合作夥伴的體驗，Azure AD 條件式存取，請造訪-[看令人驚奇的事情人們如何運用 Azure](https://azure.microsoft.com/case-studies/?service=active-directory)。

## <a name="announcements"></a>通知

Azure AD 會持續不斷進行改進。 若要隨時掌握最新的開發，請參閱[的 Azure Active Directory 中的新功能？](../fundamentals/whats-new.md)

最近使用的技術社群和 Microsoft 身分識別部門的部落格：

* 2018 年 9 月 24 日， [Azure Databricks 中的 Azure Active Directory 條件式存取](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018 年 9 月 21 日[自訂控制項處於公開預覽階段的 Azure AD 條件式存取](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018 年 9 月 21 日[現已與 Microsoft Cloud App Security 的有限存取權的 Azure AD 條件式存取支援](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018 年 9 月 21 日[Azure AD 條件式存取：受管理的瀏覽器支援，適用於 iOS/Android 平台，現以預覽](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018 年 9 月 21 日[處於公開預覽狀態的國家/地區代碼的 Azure AD 條件式存取](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018 年 9 月 21 日[Azure AD--使用規定現已開放下載](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>學習資源

請遵循下列連結，概略了解 Azure AD 條件式存取函式。

* 了解 「[什麼是 Azure Active Directory 中的條件式存取？](overview.md)"
* 了解 「[什麼是 Azure Active Directory 條件式存取中的條件？](conditions.md)"
* 了解 「[什麼是 Azure Active Directory 條件式存取的位置條件？](location-condition.md)"
* 了解 「[什麼是存取在 Azure Active Directory 條件式存取控制？](controls.md)"
* 尋找 「[什麼是如果工具在 Azure Active Directory 條件式存取？ 」](what-if-tool.md)
* 請依照下列[中 Azure Active Directory 條件式存取的最佳作法](best-practices.md)

此外，請參閱下列連結以取得指導方針來保護與 Azure Active Directory 整合的所有服務的存取權。

* [什麼是基準保護 （預覽）？](baseline-protection.md) 基準保護可確保您至少有在 Azure Active Directory 環境中啟用的安全性基準層級。
* [身分識別和裝置存取設定](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)。 描述如何設定安全存取雲端服務，透過 Enterprise Mobility + Security 產品藉由實作建議的環境和組態，包括指定的一份條件式存取原則和相關的功能。
* [Azure Active Directory 條件式存取設定參考](technical-reference.md)。 了解：
   * 哪些應用程式使用條件式存取？
   * 使用條件式存取，可啟用哪些服務？
* [啟用安全的使用者存取 Azure Active Directory 條件式存取](https://www.youtube.com/watch?v=eLAYBwjCGoA)。 觀看這段影片以了解條件式存取其他企業和 Mobility Suite 的工作負載中所扮演的角色。

### <a name="training-videos"></a>訓練影片

**Enterprise Mobility + Security 中的條件式存取**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**裝置型條件式存取**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**啟用安全的使用者存取 Azure Active Directory 條件式存取**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>線上課程

請參閱下列的條件式存取課程以及其他有關[Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com:[設計 Microsoft Azure 中的身分識別管理](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * 「 這堂課程引導您完成想要知道設計您的身分識別管理解決方案與 Azure AD 的重要項目。 」 Azure AD 條件式存取會涵蓋在 「 使用角色和與 Azure AD 存取控制 」 模組。

* Pluralsight.com:[設計驗證適用於 Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * 「 本課程說明如何使用 Azure AD 來解決所有您的雲端驗證需求 」。 Azure AD 條件式存取會涵蓋 「 驗證需求的不同案例 」 模組中。

* Pluralsight.com:[設計適用於 Microsoft Azure 的授權](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * 「 這項課程教導授權選項適用於 Azure 和 Azure AD。 」 Azure AD 條件式存取會涵蓋 「 Azure Resource Manager 與 Azure AD 的授權 」 模組中。

### <a name="books"></a>叢書

* O'Reilly-[實作第二個版本的 Azure 解決方案。](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * 「 取得啟動並執行 Azure 服務和了解如何在組織中實作它們。 本章涵蓋 azure AD 條件式存取[部署與同步處理 Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)。 」

* Wiley-[精通 Microsoft Azure 基礎結構服務](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * 「 以下是您需要了解、 評估、 部署及維護環境，以便利用 Microsoft Azure 的所有項目。 」

## <a name="white-papers"></a>白皮書

* 發行 2018 年 12 月 18 日，[與 Azure Active Directory 中建立具有恢復功能的存取控制管理策略](../authentication/concept-resilient-controls.md)
   * 本文件提供指引策略的組織可能會採用時提供復原功能，以減少在發生未預期的中斷期間的鎖定的風險。

* 發行 2018 年 9 月 18 日，[資源移轉至 Azure Active Directory 的應用程式](../manage-apps/migration-resources.md)
   * 本白皮書中包括一份資源可以幫助您移轉至 Azure Active Directory (Azure AD) 的應用程式存取和驗證。

* 發行 2018 年 7 月 12 日[Azure 安全性與合規性藍圖：英國官方的工作負載裝載 PaaS Web 應用程式](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure 藍圖是由指引文件和自動化範本所組成，可部署雲端式架構，以將解決方案提供給有認證或合規性需求的案例。

## <a name="guidance-for-it-administrators"></a>IT 系統管理員指南

登入[Azure 入口網站](https://portal.azure.com/)為全域管理員、 安全性系統管理員或條件式存取系統管理員。 請參閱[Azure Active Directory 中的系統管理員角色權限。](../users-groups-roles/directory-assign-admin-roles.md)

身為 IT 系統管理員，使用[Azure AD 條件式存取](overview.md)要求使用者使用 Azure Multi-factor Authentication 進行驗證，請登入從信任的網路或受信任的裝置。

以下是要幫助您入門的實用連結：

* [Azure Active Directory 中條件式存取的最佳作法](best-practices.md)
* [使用 Azure AD 存取權檢閱來管理已排除的條件式存取原則的使用者](../governance/conditional-access-exclusion.md)
* [如何：規劃 Azure Active Directory 中的條件式存取部署](plan-conditional-access.md)
* [快速入門：需要針對特定應用程式使用 Azure Active Directory 條件式存取進行 MFA](app-based-mfa.md)
* [快速入門：必須接受使用規定才可存取雲端應用程式](require-tou.md)
* [快速入門：使用 Azure Active Directory 條件式存取偵測到有工作階段的風險時封鎖存取](app-sign-in-risk.md)
* [Azure AD 條件式存取常見問題集](faqs.md)
   * 如有其他問題，您也可以檢視[MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)。
   * 如果找不到問題的答案，我們的支援團隊一直都能協助您進一步的。 使用[請連絡 Microsoft 支援](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)。

### <a name="tutorials"></a>教學課程

* [**快速入門：需要針對特定應用程式使用 Azure Active Directory 條件式存取進行 MFA**](app-based-mfa.md)
   * 本快速入門示範如何設定您的環境中所選取的雲端應用程式需要 multi-factor authentication 的 Azure AD 條件式存取原則。

* [**快速入門：需要存取雲端應用程式之前接受的使用規定**](require-tou.md)
   * 本快速入門示範如何設定您的環境中所選取的雲端應用程式需要接受本 ToU 的 Azure AD 條件式存取原則。

* [**快速入門：使用 Azure Active Directory 條件式存取偵測到有工作階段的風險時封鎖存取**](app-sign-in-risk.md)
   * 本快速入門示範如何設定條件式存取原則，偵測到已設定的登入風險層級時，封鎖登入。

* [教學課程：**移轉要求多重要素驗證在 Azure 入口網站中的傳統原則**](policy-migration-mfa.md)
   * 本教學課程會示範如何移轉要求雲端應用程式的多重要素驗證 (MFA) 的傳統原則。

## <a name="end-user-readiness-and-communication"></a>使用者整備和通訊

條件式存取會使用其他 Azure AD 功能，可能會影響使用者體驗。 比方說，您可以使用 Azure multi-factor authentication，若要啟用增強式驗證的使用者。 在此情況下，您將使用 Azure mfa 的使用者範本。

## <a name="next-steps"></a>後續步驟

* 開始使用您部署[規劃文件的條件式存取部署](plan-conditional-access.md)。
