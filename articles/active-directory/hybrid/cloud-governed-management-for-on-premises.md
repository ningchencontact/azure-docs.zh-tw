---
title: Azure AD 雲端受管理的內部工作負載-Azure
description: 本主題描述適用於內部部署工作負載的控管的雲端管理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109510"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>如何在 Azure AD 提供雲端控管內部工作負載的管理

Azure Active Directory (Azure AD) 是全方位的身分識別即服務 (IDaaS) 解決方案數百萬個組織橫跨身分識別、 存取管理和安全性的所有層面所使用。 Azure AD 擁有超過 10 億個使用者的身分識別，並幫助使用者登入，並安全地存取：

* 外部資源，例如 Microsoft Office 365、 Azure 入口網站中，以及數千個其他軟體做為服務 (SaaS) 應用程式。
* 內部資源，例如在組織的公司網路和內部網路，以及任何由該組織所開發的雲端應用程式的應用程式。

如果它們是 「 純雲端 」 或 「 混合式 」 為有在內部部署工作負載，組織可以使用 Azure AD。 Azure AD 的混合式部署可以組織將其 IT 資產移轉至雲端，或繼續與新的雲端服務的現有內部部署基礎結構整合策略的一部分。

在過去，「 混合式 」 組織已了解 Azure AD，因為擴充其現有的內部部署基礎結構。 在這些部署中，在內部部署身分識別控管系統管理，Windows Server Active Directory 或其他內部目錄系統，為控制點，以及使用者和群組從同步處理這些系統的雲端目錄，例如 Azure AD。 在雲端中這些身分識別之後，它們可以對使用 Office 365、 Azure 和其他應用程式。

![身分識別生命週期](media/cloud-governed-management-for-on-premises//image1.png)

當組織移動多個其 IT 基礎結構，以及他們的應用程式至雲端時，有許多要尋找的改良的安全性和身分識別管理即服務簡化的管理功能。 Azure AD 的雲端提供 IDaaS 功能加速轉換至雲端所提供的解決方案和功能，可讓組織以快速採用，並將其身分識別管理的多個從傳統內部中移動的控管的管理Azure AD 中，同時繼續支援現有與新的應用程式的系統。

這份文件概述為混合式 IDaaS Microsoft 的策略，並說明如何組織時，可以為其現有的應用程式使用 Azure AD。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD 針對雲端控管的身分識別管理

隨著組織改到雲端，他們需要保證能控制其完整的環境-更高的安全性和更多的可見性，支援的自動化和主動式的深入解析的活動。 「**雲端控管管理**」 描述組織管理和控管其使用者、 應用程式、 群組和裝置，從雲端的方式。

在這個現代化的世界中，組織需要能夠有效地大規模管理因為 SaaS 應用程式的激增和共同作業和外部身分識別的日益重要的角色。 雲端的新風險橫向表示組織必須回應速度更快-惡意執行者入侵雲端使用者可能會影響雲端和內部部署應用程式。

特別是，混合式組織必須能夠委派和自動化工作，這在過去 IT 未以手動方式。 若要自動化工作，他們需要 Api 和協調不同身分識別相關的資源 （使用者、 群組、 應用程式、 裝置） 的生命週期的處理程序讓他們可以將這些資源的日常管理委派給之外的更多個人核心 IT 人員。 Azure AD 會解決這些需求，透過使用者帳戶管理和原生驗證的使用者，而不需要內部部署身分識別基礎結構。 不會在內部部署基礎結構建置可獲益的組織，有新的使用者，例如商業夥伴，這不是源自於其內部部署目錄，但其存取管理是達到業務成果的社群。

此外，管理未完成沒有控管---美元，且在這個新環境的控管是身分識別系統，而不是附加元件的整合的部分。 身分識別管理可讓組織能夠管理的身分識別和存取跨員工、 商務夥伴和廠商，以及服務和應用程式的生命週期。

將身分識別管理可讓您更輕鬆地讓公司轉為雲端控管的管理，可讓 IT 能夠調整，來賓可應付新挑戰，並提供更深入的深入解析和比客戶已具備的自動化在內部部署基礎結構。 在這個新環境的控管表示透明度、 可見性，與適當的控制項，在組織內的資源的存取權的組織的能力。 與 Azure AD 安全性作業和稽核團隊有哪些資源 （在哪些裝置） 組織，這些使用者具有該存取權的執行和的組織是否有並且使用適當的可視性誰---應-存取若要移除或限制存取公司或法規的原則根據的控制項。

新的管理模型權益與 SaaS 和特定業務 (LOB) 應用程式的組織，因為它們可更輕鬆地管理及保護這些應用程式的存取。 藉由使用 Azure AD 整合的應用程式，組織將能夠使用及管理跨這兩個雲端的存取，並在內部部署環境一致地產生身分識別。 應用程式生命週期管理變得更自動化，且 Azure AD 提供豐富的深入了解應用程式不是在內部部署身分識別管理中輕易實現的使用方式。 透過 Azure AD、 Office 365 群組和小組自助功能，組織可以輕鬆地建立群組存取管理和共同作業和新增或移除使用者在雲端啟用共同作業及存取管理需求。

選取的權限的 Azure AD 功能，控管的管理取決於要使用的應用程式的雲端與這些應用程式與 Azure AD 的整合方式。 下列各節說明的 AD 整合的應用程式，並使用 （比方說，SAML、 OAuth 或 OpenID Connect） 的同盟通訊協定的應用程式應採取的方法。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 整合的應用程式的控管的雲端管理

Azure AD 可改善組織的內部部署 Active Directory 整合式應用程式可以透過安全的遠端存取 」 和 「 條件式存取這些應用程式的管理。 此外，Azure AD 也提供帳戶生命週期管理和認證管理使用者的現有 AD 帳戶，包括：

* **安全遠端存取和內部部署應用程式的條件式存取**

對許多組織來說，從雲端進行內部部署 AD 整合的 web 和遠端桌面應用程式管理存取權的第一個步驟是將[應用程式 proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)前面提供這些應用程式保護遠端存取。

單一登入 Azure AD 後，使用者可以透過外部 URL 或內部應用程式入口網站存取雲端和內部部署應用程式。 例如，應用程式 Proxy 會提供遠端存取和單一登入遠端桌面，SharePoint，以及應用程式，例如 Tableau 及 Qlik 及企業營運 (LOB) 應用程式。 此外，條件式存取原則可以包括顯示[使用條款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)並[確保使用者已經同意這些](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou)才能存取應用程式。

![應用程式 Proxy 架構](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 帳戶的的自動生命週期管理**

身分識別控管可協助組織達成之間取得平衡*產能*---速度的人都可以存取的資源所需，例如何時加入組織？ ---並*安全性*---應該如何其存取權變更經過一段時間，例如當那個人的工作狀態的變更？ 身分識別生命週期管理是身分識別控管的基礎，而大規模有效控管必須使應用程式的身分識別生命週期管理基礎結構現代化。

對許多組織來說，員工的身分識別生命週期會繫結到該使用者在人力資本管理 (HCM) 系統。 使用 Workday 做為其 HCM 系統的組織，Azure AD 能確保在 AD 中的使用者帳戶皆[自動佈建和取消佈建的背景工作角色在 Workday 中](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)。 執行如此的潛在客戶，來提升的使用者產能透過 birthright 的自動化帳戶，並管理風險確保應用程式的存取會自動更新使用者角色會變更，或離開組織時。 Workday 驅動的使用者佈建[部署計劃](https://aka.ms/WorkdayDeploymentPlan)引導組織透過 Workday 的最佳作法實作五個步驟的程序中的 Active Directory 使用者佈建解決方案的逐步指南。

Azure AD Premium 也包含 Microsoft Identity Manager，可以匯入記錄，從其他內部 HCM 系統，包括 SAP、 Oracle eBusiness、 和 PeopleSoft。

越來越多企業對企業共同作業會需要存取權授與組織外部人員。 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)共同作業可讓組織安全地分享其應用程式和服務來賓使用者與外部夥伴同時保有對自己的公司資料的控制項。

Azure AD 可以[自動在 AD 中建立帳戶的來賓使用者](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)如有需要讓商務來賓存取內部部署 AD 整合的應用程式而不需要其他密碼。 組織可以設定[來賓使用者的多重要素驗證 (MFA) 原則](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s 讓 MFA 檢查應用程式 proxy 驗證期間完成。 此外，任何[存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)，對的雲端 B2B 使用者套用到內部部署使用者。 例如，如果透過生命週期管理原則刪除雲端使用者，則也會刪除內部部署使用者。

**認證為 Active Directory 帳戶的管理**Azure AD 的自助式密碼重設忘記其密碼，重新驗證及重設其密碼，以變更密碼的使用者可以透過[寫入在內部部署 Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)。 密碼重設程序也可以使用內部部署 Active Directory 密碼原則：當使用者重設其密碼時，它會檢查以確保其符合內部部署 Active Directory 原則，然後再認可到該目錄。 自助式密碼重設[部署計劃](https://aka.ms/deploymentplans/sspr)概述推出自助式密碼重設使用者透過 web 和 Windows 整合式體驗的最佳作法。

![Azure AD SSPR 架構](media/cloud-governed-management-for-on-premises/image3.png)

最後，對於允許使用者變更其密碼，在 AD 中的組織而言，AD 可以設定為使用相同的密碼原則，因為透過 Azure AD 中使用組織[Azure AD 密碼保護功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)目前中公開預覽狀態。

當組織準備好要移至雲端的 AD 整合的應用程式移至 Azure，應用程式裝載的作業系統[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)提供相容的 AD 網域服務 （例如網域加入群組原則、 LDAP、 Kerberos/NTLM 驗證)。 與組織的現有 Azure AD 租用戶，讓使用者使用其公司認證來登入，azure AD 網域服務整合。 此外，現有的群組和使用者帳戶可用來安全地存取資源，確保順暢 '和轉移' 的 Azure 基礎結構服務的內部部署資源。

![Azure AD 網域服務](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>控管的雲端管理內部部署同盟型應用程式

已使用的內部部署身分識別提供者的組織，移至 Azure AD 的應用程式啟用更安全的存取和同盟管理系統更容易管理體驗。 Azure AD 可讓您設定更細微的每個應用程式存取控制項，包括 Azure Multi-factor Authentication，使用 Azure AD 條件式存取。 Azure AD 支援更多的功能，包括應用程式特定的權杖簽署憑證和可設定的憑證到期日。 這些功能、 工具和指引可讓要淘汰其內部部署身分識別提供者的組織。 Microsoft 自己的 IT，例如，已移 17,987 應用程式從 Microsoft 的內部 Active Directory Federation Services (AD FS) 至 Azure AD。

![Azure AD 的發展](media/cloud-governed-management-for-on-premises/image5.png)

若要開始移轉同盟應用程式至 Azure AD 作為身分識別提供者，請參閱 https://aka.ms/migrateapps 所包含的連結：

* 技術白皮書[將您應用程式移轉至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)，其中顯示移轉的優點，並說明如何規劃移轉中明確所述的四個階段： 探索、 分類、 移轉和持續管理。 將會引導您了解如何思考程序，並細分成容易取用的片段專案。 此文件全篇都有在過程中可協助您的重要資源連結。

* 解決方案指南[移轉應用程式驗證從 Active Directory 同盟服務至 Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide)探索更多詳細資料中相同的規劃和執行應用程式移轉專案的四個階段. 本指南中，您將了解如何將這些階段套用至從 Active Directory Federation Services (AD FS) 的應用程式移至 Azure AD 的特定目標。

* [Active Directory Federation Services 移轉整備指令碼](https://aka.ms/migrateapps/adfstools)可以判斷是否可以移轉至 Azure AD 的應用程式的現有內部部署 Active Directory Federation Services (AD FS) 伺服器上執行。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>跨雲端和內部部署應用程式的持續存取管理

組織需要管理可調整的存取權的程序。 使用者繼續累積的存取權限，並得到超過項目一開始佈建它們。 此外，企業組織需要能夠有效率地調整來開發及強制執行存取原則和持續的控制項。

一般而言，IT 會將存取核准決策委派給商務決策者。 此外，IT 可能是使用者本身。 例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者也可能不會察覺資料至他們受邀的組織中的處理需求。

組織可以自動存取生命週期程序，透過技術這類[動態群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)，並結合使用者佈建至[SaaS 應用程式](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，或[應用程式整合使用 System for Cross-domain Identity Management (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) 標準。 組織也可以控制哪些[來賓使用者能夠存取內部部署應用程式](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)。 接著可以利用週期性 [Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)，定期檢閱這些存取權限。

## <a name="future-directions"></a>未來方向

在混合式環境中，Microsoft 的策略是以啟用部署位置**雲端是身分識別控制平面**，內部部署目錄和其他身分識別系統，例如 Active Directory 和其他內部部署應用程式，都具有存取權的使用者佈建的目標。 此策略會繼續以確保權限、 識別和存取這些應用程式與依賴它們的工作負載中。 在此結束狀態下，組織將可提升使用者產能完全從雲端。

![Azure AD 架構](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>後續步驟

如需有關如何開始這趟旅程的詳細資訊，請參閱 Azure AD 部署計畫中，位於<https://aka.ms/deploymentplans>。 它們提供有關如何部署 Azure Active Directory (Azure AD) 功能的端對端指引。 每個計劃說明的商業價值，規劃考量、 設計和操作成功導入常見的 Azure AD 功能所需的程序。 Microsoft 持續更新的部署計劃時從 cloud 與 Azure AD 管理的情況下，我們在增加新功能，從客戶部署和其他意見反應經驗的最佳做法。
