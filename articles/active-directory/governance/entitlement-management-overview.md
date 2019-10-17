---
title: 什麼是 Azure AD 權利管理？ （預覽）-Azure Active Directory
description: 取得 Azure Active Directory 權利管理的總覽，以及如何使用它來管理內部和外部使用者對群組、應用程式和 SharePoint Online 網站的存取。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a467856550bf2deaab931b3fe2f54b7986f12f8a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430306"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>什麼是 Azure AD 權利管理？ (預覽)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

組織中的員工需要存取各種群組、應用程式和網站，才能執行其工作。 管理這項存取是一項挑戰。 在大部分情況下，專案的使用者所需的所有資源並沒有組織的清單。 專案經理對於所需的資源、涉及的人員，以及專案的持續時間有很大的瞭解。 不過，專案經理通常沒有核准或授與存取權給其他人的許可權。 當您嘗試使用外部個人或公司時，此案例會變得更複雜。

Azure Active Directory （Azure AD）權利管理可協助您管理內部使用者和組織外部使用者的群組、應用程式和 SharePoint Online 網站的存取權。

這段影片提供權利管理和其商業價值的總覽：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>為何要使用權利管理？

企業組織通常面臨的挑戰，就是管理資源的存取權，例如：

- 使用者可能不知道他們應該擁有哪些存取權
- 使用者可能難以找到正確的個人或適當的資源
- 一旦使用者找到並接收資源的存取權，他們可能會因為商務目的而讓存取時間超過所需

這些問題對於需要來自另一個目錄（例如來自供應鏈組織或其他商務合作夥伴的外部使用者）存取權的使用者而言是更複雜的。 例如：

- 組織可能不知道其他目錄中的特定人員能夠邀請他們
- 即使組織能夠邀請這些使用者，組織可能也不記得以一致的方式管理所有使用者的存取權

Azure AD 權利管理有助於解決這些挑戰。

## <a name="what-can-i-do-with-entitlement-management"></a>我可以使用權利管理來做什麼？

以下是一些權利管理的功能：

- 建立使用者可以要求的相關資源套件
- 定義規則，以瞭解如何要求資源以及存取何時過期
- 管理內部和外部使用者存取的生命週期
- 委派資源的管理
- 指定核准要求的核准者
- 建立報表以追蹤歷程記錄

如需身分識別治理和權利管理的總覽，請觀看 Ignite 2018 會議的下列影片：

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>我可以管理哪些資源？

以下是您可以使用權利管理來管理存取權的資源類型：

- Azure AD 安全性群組
- Office 365 群組
- Azure AD 企業應用程式，包括 SaaS 應用程式和支援同盟或布建的自訂整合應用程式
- SharePoint Online 網站集合和網站

您也可以控制依賴 Azure AD 安全性群組或 Office 365 群組之其他資源的存取權。  例如：

- 您可以使用存取套件中的 Azure AD 安全性群組，以及為該群組設定以[群組為基礎的授權](../users-groups-roles/licensing-groups-assign.md)，為使用者提供 Microsoft Office 365 授權
- 您可以使用存取套件中的 Azure AD 安全性群組，並建立該群組的[azure 角色指派](../../role-based-access-control/role-assignments-portal.md)，以授與使用者管理 azure 資源的存取權

## <a name="what-are-access-packages-and-policies"></a>什麼是存取套件和原則？

權利管理引進了*存取套件*的概念。 存取套件是使用者處理專案或執行其工作所需之所有資源的組合。 這些資源包括對群組、應用程式或網站的存取權。 存取套件是用來管理內部員工的存取權，以及組織外部的使用者。 存取套件會定義在名為*目錄*的容器中。

存取套件也包含一或多個*原則*。 原則會定義用來存取存取封裝的規則或護欄。 啟用原則會強制只授與正確的使用者存取權、正確的資源，以及正確的時間量。

![存取套件和原則](./media/entitlement-management-overview/elm-overview-access-package.png)

透過存取套件和其原則，存取套件管理員會定義：

- 資源
- 使用者資源所需的角色
- 有資格要求存取權的外部使用者內部使用者和夥伴組織
- 核准程式和可以核准或拒絕存取的使用者
- 使用者存取的持續時間

下圖顯示 [權利管理] 中不同元素的範例。 它會顯示兩個範例存取封裝。

- **存取套件 1**包含單一群組做為資源。 存取是以原則定義，可讓目錄中的一組使用者要求存取權。
- **存取套件 2**包含群組、應用程式和 SharePoint Online 網站做為資源。 存取會以兩個不同的原則定義。 第一個原則可讓目錄中的一組使用者要求存取權。 第二個原則可讓外部目錄中的使用者要求存取權。

![權利管理總覽](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>詞彙

若要進一步瞭解權利管理及其檔，您應參閱下列條款。

| 詞彙或概念 | 描述 |
| --- | --- |
| 權利管理 | 指派、撤銷和管理存取套件的服務。 |
| 存取套件 | 小組或專案所需且受原則規範的資源組合。 存取套件一律包含在目錄中。 |
| 存取要求 | 存取存取套件中資源的要求。 要求通常會經過工作流程。 |
| 原則 | 定義存取生命週期的一組規則，例如使用者取得存取權的方式、核准者，以及使用者可以存取的時間長度。 範例原則包括員工存取權和外部存取。 |
| catalog | 相關資源和存取封裝的容器。 |
| 一般目錄 | 一律可使用的內建目錄。 若要將資源新增至一般目錄，需要特定許可權。 |
| 資源 | 使用者可被授與許可權的資產或服務（例如，Office 群組、安全性群組、應用程式或 SharePoint Online 網站）。 |
| 資源類型 | 資源類型，包括群組、應用程式和 SharePoint Online 網站。 |
| 資源角色 | 與資源相關聯的許可權集合。 |
| 資原始目錄 | 具有一或多個要共用之資源的目錄。 |
| 指派的使用者 | 將存取套件指派給使用者，讓使用者擁有該存取套件的所有資源角色。 |
| enable | 讓使用者可以要求存取套件的程式。 |

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

特製化雲端（例如 Azure Government、Azure 德國和 Azure 中國世紀）目前無法在此預覽中使用。

### <a name="which-users-must-have-licenses"></a>哪些使用者必須有授權？

您的租使用者必須有至少多個 Azure AD Premium P2 授權，因為您擁有使用中的成員使用者。 權利管理中的有效成員使用者包括：

- 起始或核准存取封裝要求的使用者。
- 已獲指派存取套件的使用者。 
- 管理存取套件的使用者。

在成員使用者的授權中，您也可以允許許多來賓使用者與權利管理互動。 如需如何計算可包含的來賓使用者數目的詳細資訊，請參閱[AZURE ACTIVE DIRECTORY B2B 共同作業授權指引](../b2b/licensing-guidance.md)。

如需如何將授權指派給使用者的詳細資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

## <a name="next-steps"></a>後續步驟

- [教學課程：建立您的第一個存取套件](entitlement-management-access-package-first.md)
- [常見案例](entitlement-management-scenarios.md)
