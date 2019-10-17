---
title: 條件式存取採用套件-Azure Active Directory
description: 採用 Azure AD 的條件式存取來存取資源
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
ms.openlocfilehash: c34f59c3e9f679adf8ae410f648cb7de6dba6447
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430048"
---
#  <a name="adopting-azure-ad-conditional-access"></a>採用 Azure AD 條件式存取

在行動優先、雲端優先的世界中，使用者可以使用不同種類的裝置和應用程式，從任何地方存取貴組織的資源。 如此一來，只要專注于誰可以存取資源就夠了。 您可以控制誰擁有存取權，以及識別使用者的所在位置，以及所使用的裝置及其他更多功能。

為了提供此控制項， **Azure Active Directory （AD）條件式存取**可讓您指定任何使用者必須符合才能存取應用程式的條件，例如多重要素驗證（MFA）。 使用條件式存取原則可控制授權使用者（已獲得雲端應用程式存取權的使用者）在特定條件下存取雲端應用程式的方式。 如需詳細資訊，請參閱[Azure Active Directory 的條件式存取](overview.md)。

## <a name="key-benefits"></a>主要權益

使用 Azure AD 條件式存取的主要優點如下：

* **提高生產力：** 條件式存取（CA）原則可讓您將提示使用者使用 MFA、封鎖存取權，或必須使用受信任裝置的目標點。 例如，您可以設定原則，例如僅要求使用者在關閉公司網路時，MFA 進入應用程式。 減少 MFA 要求可讓使用者更具生產力，而不是他們每次登入時都必須進行 MFA。 此外，Azure AD 條件式存取可讓您指定每個使用者的原則，也會建立應用程式特定的原則。
* **管理風險：** 啟用條件式存取原則可為您提供雲端規模的身分識別保護、風險型存取控制功能和原生多重要素驗證支援。 結合條件式存取與 identity protection 可讓您定義何時封鎖或閘道存取應用程式。
* **解決規範與治理：** 使用 Azure AD 來審核應用程式的存取要求和核准，並瞭解整體應用程式的使用方式，因為它支援每個執行的應用程式存取要求的原生 audit 記錄。 「審核」包括要求者身分識別、要求的日期、商業理由、核准狀態和核准者身分識別。 您也可以從 API 取得這項資料，這可讓您將此資料匯入至所選的安全性事件和事件監視（SIEM）系統。
* **管理成本：** 將存取原則移至 Azure AD 可減少依賴自訂或內部部署解決方案（例如 Active Directory 同盟服務（ADFS））進行條件式存取，以降低執行該基礎結構的成本。

## <a name="customer-case-studies"></a>客戶案例研究

探索大部分的組織如何使用 Azure AD 條件式存取來定義和執行自動化存取控制決策，以根據條件來存取雲端應用程式。 下列精選案例會示範如何滿足這些客戶的需求。

* [**Wipro**使用 Microsoft 雲端安全性工具來提升行動生產力，以改善客戶的參與度。](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD 中的條件式存取原則，讓公司能夠與受信任的外部實體共用文件、資源和應用程式---他們可以使用自己的認證---同時保有自己的公司資料控制權。
* [**Accenture**透過 Microsoft Cloud App security Accenture 來保護其移至雲端](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)的工作，以評估 Cloud App Security 的[條件式存取應用程式控制](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)功能，這會使用 Azure Active Directory 的條件式存取閘道以特定條件為基礎的應用程式存取。 LePenske 指出，這項功能在禁止下載時啟用唯讀檔案存取可能很有用。
* [ **Aramex**傳遞有限-全球物流和運輸公司以身分識別和存取管理解決方案建立雲端連線的辦公室](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)。 確保安全存取對於 Aramex 的遠端員工而言特別艱難。 公司現在正在套用條件式存取，讓這些遠端員工從網路外部存取其 SaaS 應用程式。 條件式存取規則會決定是否要強制執行多重要素驗證，只提供適當的人員存取權。

若要深入瞭解 Azure AD 條件式存取的客戶和合作夥伴體驗，請造訪-[查看人們使用 Azure 執行的](https://azure.microsoft.com/case-studies/?service=active-directory)絕佳功能。

## <a name="announcements"></a>公告

Azure AD 會持續不斷進行改進。 若要保持最新的開發，請參閱[Azure Active Directory 的新功能？](../fundamentals/whats-new.md)

技術小組和 Microsoft 身分識別部門最近的 blog：

* 2018年9月24日， [Azure Active Directory Azure Databricks 中的條件式存取](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018年9月21日， [Azure AD 條件式存取自訂控制項目前為公開預覽](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)狀態
* 2018年9月21日，[已可使用 Microsoft Cloud App Security 的有限存取 Azure AD 條件式存取支援](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018年9月21日[Azure AD 條件式存取： iOS/Android 平臺的受控瀏覽器支援現供預覽](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018年9月21日， [Azure AD 國家/地區代碼的條件式存取現已開放公開預覽](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018年9月21日， [Azure AD 使用規定現已推出](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>學習資源

請遵循下列連結，以深入瞭解如何 Azure AD 條件式存取功能。

* 瞭解「[什麼是 Azure Active Directory 中的條件式存取？](overview.md)」
* 知道「[Azure Active Directory 條件式存取中的條件為何？](conditions.md)」
* 知道「[Azure Active Directory 條件式存取中的位置條件為何？](location-condition.md)」
* 知道「[什麼是 Azure Active Directory 條件式存取中的存取控制？](controls.md)」
* 尋找「[Azure Active Directory 條件式存取中的假設工具為何？](what-if-tool.md) 」
* 遵循[Azure Active Directory 中條件式存取的最佳做法](best-practices.md)

此外，請參閱下列連結以取得指導方針，以保護與 Azure Active Directory 整合的所有服務的存取權。

* 身分[識別與裝置存取](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)設定。 說明如何藉由執行建議的環境和設定（包括一組指定的條件式存取原則和相關功能），透過 Enterprise Mobility + Security 產品來設定雲端服務的安全存取。
* [Azure Active Directory 條件式存取設定參考](technical-reference.md)。 瞭解
   * 哪些應用程式會使用條件式存取？
   * 哪些服務已啟用條件式存取？
* [啟用 Azure Active Directory 安全使用者存取的條件式存取](https://www.youtube.com/watch?v=eLAYBwjCGoA)。 觀看這段影片，瞭解條件式存取如何在其他企業和行動套件的工作負載中扮演角色。

### <a name="training-videos"></a>訓練影片

**Enterprise Mobility + Security 中的條件式存取**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**以裝置為基礎的條件式存取**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**啟用安全使用者存取的條件式存取 Azure Active Directory**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>線上課程

如需[Pluralsight.com](https://www.pluralsight.com/)的詳細資訊，請參閱下列條件式存取課程：

* Pluralsight.com：[在 Microsoft Azure 中設計身分識別管理](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * 「本課程會引導您完成使用 Azure AD 設計身分識別管理解決方案所需瞭解的重要專案。」 「使用角色和存取控制搭配 Azure AD」課程涵蓋 Azure AD 條件式存取。

* Pluralsight.com： [Microsoft Azure 的設計驗證](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * 「本課程說明如何使用 Azure AD 來解決您所有的雲端驗證需求。」 「不同案例的驗證需求」課程中涵蓋了 Azure AD 條件式存取。

* Pluralsight.com： [Microsoft Azure 的設計授權](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * 「本課程教 Azure 和 Azure AD 提供的授權選項。」 「使用 Azure Resource Manager 和 Azure AD 進行授權」課程中涵蓋了 Azure AD 條件式存取。

### <a name="books"></a>通訊錄

* O'Reilly-[實現 Azure 解決方案-第二版。](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * 「開始使用 Azure 服務，並瞭解如何在您的組織中執行。 [Azure Active Directory 的部署和同步](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)處理一節中會涵蓋 Azure AD 條件式存取。」

* Wiley-[主控 Microsoft Azure 的基礎結構服務](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * 「以下是您瞭解、評估、部署和維護使用 Microsoft Azure 之環境所需的一切。」

## <a name="white-papers"></a>技術白皮書

* 2018年12月18日發行，[使用 Azure Active Directory 建立彈性的存取控制管理原則](../authentication/concept-resilient-controls.md)
   * 本檔提供組織可能會採用的策略指導方針，以降低在意外中斷期間鎖定的風險。

* 2018年9月18日發行[的資源，可將應用程式遷移至 Azure Active Directory](../manage-apps/migration-resources.md)
   * 本白皮書包含資源清單，可協助您將應用程式存取和驗證遷移至 Azure Active Directory （Azure AD）。

* 2018年7月12日發行[Azure 安全性與合規性藍圖：適用于英國官方工作負載的 PaaS Web 應用程式裝載](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure 藍圖是由指引文件和自動化範本所組成，可部署雲端式架構，以將解決方案提供給有認證或合規性需求的案例。

## <a name="guidance-for-it-administrators"></a>適用于 IT 系統管理員的指引

以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入[Azure 入口網站](https://portal.azure.com/)。 請參閱[Azure Active Directory 中的系統管理員角色許可權。](../users-groups-roles/directory-assign-admin-roles.md)

身為 IT 系統管理員，請使用[Azure AD 條件式存取](overview.md)，以要求使用者使用 Azure 多重要素驗證進行驗證、從受信任的網路登入或受信任的裝置。

以下是有用的連結，可協助您開始使用：

* [Azure Active Directory 中的條件式存取最佳做法](best-practices.md)
* [使用 Azure AD 存取審查來管理已從條件式存取原則中排除的使用者](../governance/conditional-access-exclusion.md)
* [如何：在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)
* [快速入門：使用 Azure Active Directory 條件式存取來要求特定應用程式的 MFA](app-based-mfa.md)
* [快速入門：存取雲端應用程式之前需要接受使用規定](require-tou.md)
* [快速入門：使用 Azure Active Directory 條件式存取偵測到會話風險時封鎖存取](app-sign-in-risk.md)
* [Azure AD 條件式存取常見問題](faqs.md)
   * 如有其他問題，您也可以觀看[MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)。
   * 如果您找不到問題的答案，我們的支援小組一律可協助您進一步瞭解。 [請使用連絡人 Microsoft 支援服務](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)。

### <a name="tutorials"></a>教學課程

* [**快速入門：使用 Azure Active Directory 條件式存取來要求特定應用程式的 MFA**](app-based-mfa.md)
   * 本快速入門示範如何針對環境中選取的雲端應用程式，設定需要多重要素驗證的 Azure AD 條件式存取原則。

* [**快速入門：存取雲端應用程式之前需要接受使用規定**](require-tou.md)
   * 本快速入門示範如何設定 Azure AD 條件式存取原則，以要求在您的環境中選取的雲端應用程式必須接受 ToU。

* [**快速入門：使用 Azure Active Directory 條件式存取偵測到會話風險時封鎖存取**](app-sign-in-risk.md)
   * 本快速入門說明如何設定條件式存取原則，以在偵測到設定的登入風險層級時封鎖登入。

* [教學課程：**在 Azure 入口網站中遷移需要多重要素驗證的傳統原則**](policy-migration-mfa.md)
   * 本教學課程說明如何針對雲端應用程式，遷移需要多重要素驗證（MFA）的傳統原則。

## <a name="end-user-readiness-and-communication"></a>使用者準備就緒和通訊

條件式存取會使用其他可能影響使用者體驗的 Azure AD 功能。 例如，您可以使用 Azure 多重要素驗證，為使用者啟用增強式驗證。 在此情況下，您將使用 Azure MFA 的終端使用者範本。

## <a name="next-steps"></a>後續步驟

* 使用[條件式存取部署規劃檔](plan-conditional-access.md)開始進行部署。
