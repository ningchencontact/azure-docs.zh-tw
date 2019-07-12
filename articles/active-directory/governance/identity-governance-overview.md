---
title: 身分識別控管-Azure Active Directory |Microsoft Docs
description: Azure Active Directory 身分識別管理可讓您正確的程序與可見性的安全性和員工生產力的貴組織的需求之間取得平衡。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/29/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4f1563aa0437cd45c297b95a83119318a24624
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109583"
---
# <a name="what-is-azure-ad-identity-governance"></a>什麼是 Azure AD 身分識別控管？

Azure Active Directory (Azure AD) 身分識別管理可讓您正確的程序與可見性的安全性和員工生產力的貴組織的需求之間取得平衡。 它可供您確保適當使用者具有適當資源的適當存取權，並它可讓您保護、監視和稽核重要資產的存取，同時確保員工生產力。  

身分識別控管讓組織能夠跨員工、 商務夥伴和廠商，以及服務和應用程式中執行下列工作：

- 控管身分識別生命週期
- 控管存取生命週期
- 保護系統管理

具體來說，主要協助組織處理下列四個重要問題：

- 哪些使用者應該存取哪些資源？
- 使用者利用該存取權來做什麼？
- 是否有有效的組織控制項可管理存取權？
- 稽核人員可以確認這些控制項有用嗎？

## <a name="identity-lifecycle"></a>身分識別生命週期

身分識別控管可協助組織達成之間取得平衡*產能*-速度的人都可以存取的資源所需，例如何時加入我的組織？ 與*安全性* - 其存取權應該如何隨著時間變更，例如由於該人員的雇用狀態變更？  身分識別生命週期管理是身分識別管理的基礎，並有效大規模的治理需要現代化的應用程式的身分識別生命週期管理基礎結構。

![身分識別生命週期](./media/identity-governance-overview/identity-lifecycle.png)

對許多組織來說，員工的身分識別生命週期會繫結到該使用者在 HCM (人力資本管理) 系統中的代表項目。  Azure AD Premium 會在 Active Directory 與 Azure Active Directory 中自動維護人員在 Workday 中表示的使用者身分識別，如 [Workday 輸入佈建 (預覽) 教學課程](../saas-apps/workday-inbound-tutorial.md)中所述。  Azure AD Premium 也包含 [Microsoft Identity Manager](/microsoft-identity-manager/)，其可從內部部署 HCM 系統 (例如 SAP、Oracle eBusiness 和 PeopleSoft) 匯入記錄。

有愈來愈多的案例需要與組織外部人員共同作業。 [Azure AD B2B](/azure/active-directory/b2b/) 共同作業可讓您與來賓使用者和來自任何組織的外部夥伴安全地共用貴組織的應用程式與服務，同時持續控制貴公司的資料。

## <a name="access-lifecycle"></a>存取生命週期

組織需有一個程序，可管理在建立使用者的身分識別時起初為該使用者所佈建以外的存取權。  此外，企業組織必須能夠擴充有效率地縮放，才能夠持續開發並強制執行存取原則和控制項。

![存取生命週期](./media/identity-governance-overview/access-lifecycle.png)

一般而言，IT 會將存取核准決策委派給商務決策者。  此外，IT 可能是使用者本身。  例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者可能不知道他們受邀的組織中資料的處理需求。

組織可以透過[動態群組](../users-groups-roles/groups-dynamic-membership.md)這類技術，並結合使用者佈建至 [SaaS 應用程式](../saas-apps/tutorial-list.md)或[與 SCIM 整合的應用程式](../manage-apps/use-scim-to-provision-users-and-groups.md)來自動存取生命週期程序。  組織也可以控制哪些[來賓使用者能夠存取內部部署應用程式](../b2b/hybrid-cloud-to-on-premises.md)。  接著可以利用週期性 [Azure AD 存取權檢閱](access-reviews-overview.md)，定期檢閱這些存取權限。

當使用者嘗試存取應用程式時，Azure AD 會強制[條件式存取](/azure/active-directory/conditional-access/)原則。 例如，條件式存取原則可以包含顯示[使用規定](../conditional-access/terms-of-use.md)並[確保使用者已經同意這些條款](../conditional-access/require-tou.md)才能夠存取應用程式。

## <a name="privileged-access-lifecycle"></a>特殊權限的存取生命週期

在過去，特殊權限的存取被描述其他廠商所提供做為個別的功能，從身分識別控管。 不過，在 Microsoft，我們認為管理特殊權限的存取是身分識別控管，尤其是考慮與這些組織可能會導致權限的系統管理員相關聯的誤用的可能性的重要部分。 具有系統管理權限的員工、廠商和約聘人員都需要加以控管。

![特殊權限的存取生命週期](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) 提供量身訂做的其他控制項，以保護 Azure AD、Azure 和其他 Microsoft Online Services 中資源的存取權限。  在 just-in-time 存取和角色變更警示由 Azure AD PIM，除了多重要素驗證和條件式存取的功能，請提供一組完整的治理控制項，可協助保護公司資源 （目錄中，Office 365 和 Azure 資源角色）。 如同其他形式的存取權，組織可以使用存取權檢閱，對具有系統管理員角色的所有使用者設定週期性存取權重新確認。

## <a name="getting-started"></a>使用者入門

雖然沒有完美的解決方案或每位客戶的建議，下列組態會提供 Microsoft 建議您的基準原則的遵循的指南，以確保更安全且更有生產力的工作力。

- [身分識別與裝置存取設定](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [保護特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md)

您也可以查看的 [入門] 索引標籤**身分識別控管**若要開始使用權利管理在 Azure 入口網站，存取權檢閱、 Privileged Identity Management 及使用規定。

![開始使用的身分識別控管](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure AD 權限管理？(預覽)](entitlement-management-overview.md)
- [什麼是 Azure AD 存取權檢閱？](access-reviews-overview.md)
- [什麼是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)
- [使用規定對我有何幫助？](active-directory-tou.md)
