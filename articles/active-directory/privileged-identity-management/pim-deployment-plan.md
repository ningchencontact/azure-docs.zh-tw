---
title: 部署 Privileged 的 Identity Management (PIM)-Azure Active Directory |Microsoft Docs
description: 說明如何規劃 Azure AD Privileged Identity Management (PIM) 的部署。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1755d627473b0ae47bbc4bc74a3f0d2210e5372b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60440591"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>部署 Azure AD Privileged Identity Management (PIM)

本指南將逐步說明如何規劃部署的 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 在您的組織。

> [!TIP]
> 在本文件各處，您會看到有下列標示的項目：
> 
> :heavy_check_mark:**Microsoft 建議**
> 
> 這些項目屬於一般性建議，請在其適用於貴企業的特定需求時才加以實作。

## <a name="step-1-learn-about-pim"></a>步驟 1. 了解 PIM

Azure AD Privileged Identity Management (PIM) 可協助您管理 Azure AD、Azure 資源和其他 Microsoft Online Services 中的特殊權限系統管理角色。 在會獲派和遺忘特殊權限身分識別的世界中，PIM 可提供 Just-In-Time 存取、要求核准工作流程和完全整合的存取權檢閱等等的解決方案，讓您能夠即時地識別、發現和防止特殊權限角色的惡意活動。 部署 PIM 來徹底管理組織中的特殊權限角色，既可大幅減少風險，又能呈現關於特殊權限角色活動的寶貴深入解析。

### <a name="business-value-of-pim"></a>PIM 的商業價值

**管理風險** - 藉由強制執行[最低權限存取](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)和 Just-In-Time 存取的原則來保護組織。 您可以藉由只將最少量的使用者永久指派為特殊權限角色，並強制執行要經過核准和通過 MFA 才可提高權限的機制，來大幅減少與組織中特殊權限存取相關的安全性風險。 強制執行最最低權限和 Just-In-Time 存取也可讓您檢視特殊權限角色的存取記錄，並追蹤所發生的安全性問題。

**因應合規性和治理** - 部署 PIM 可建立能夠持續管理身分識別的環境。 以 Just-In-Time 的方式提高特殊權限身分識別的權限，可讓 PIM 有辦法追蹤組織中的特殊權限存取活動。 您也能夠檢視和收到組織內所有永久和合格角色指派的通知。 透過存取權檢閱，您可以定期稽核和移除不必要的特殊權限身分識別，並確保組織可符合最嚴格的身分識別、存取和安全性標準。

**降低成本** - 藉由正確部署 PIM 來消除效率不彰、人為錯誤和安全性問題，從而降低成本。 最終結果就是與特殊權限身分識別相關聯的網路犯罪件數減少了，因此不必付出高昂成本又不會於事後難以復原。 在符合法規和標準方面，PIM 還能協助組織降低與稽核存取資訊相關聯的成本。

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="licensing-requirements"></a>授權需求

若要使用 PIM，目錄必須具有下列其中一個付費或試用授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

如需詳細資訊，請參閱[使用 PIM 的授權需求](subscription-requirements.md)。

### <a name="key-pim-terminology"></a>PIM 的重要術語

| 詞彙或概念 | 描述 |
| --- | --- |
| 合格 | 需要使用者執行一或多個動作才能使用角色的角色指派。 如果使用者已有資格使用角色，即表示他們可以在需要執行特殊權限工作時啟用該角色。 使用者不論是具有永久角色指派還是合格角色指派，獲得的存取權並無差異。 唯一的差異在於有些使用者並不一直需要該存取權。 |
| 啟用 | 此程序會執行一或多個動作，讓使用者使用有資格使用的角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。 |
| Just-In-Time (JIT) 存取 | 一種模型，使用者會在其中獲得臨時權限以執行特殊權限的工作，這可防止惡意或未經授權的使用者在權限過期後取得存取權。 只有當使用者需要時才會獲得存取權。 |
| 最低權限存取的原則 | 建議的安全性做法，每位使用者只會獲得所需的最低權限，以便完成他們獲得授權而可執行的工作。 這種做法只需要最少量的全域管理員，並會改為針對特定案例使用特定的管理員角色。 |

如需詳細資訊，請參閱[術語](pim-configure.md#terminology)。

### <a name="high-level-overview-of-how-pim-works"></a>PIM 運作方式的高階概述

1. 設定 PIM，讓使用者有資格成為特殊權限角色。
1. 符合資格的使用者在需要使用其特殊權限角色時，便可在 PIM 中啟動該角色。
1. 根據您為該角色設定的 PIM 設定，使用者必須完成某些步驟 (例如，執行多重要素驗證、獲得核准，或指定原因)。
1. 使用者成功啟動其角色後，便可在預先設定好的期間內獲得該角色。
1. 系統管理員可以在稽核記錄中檢視所有 PIM 活動的過往記錄。 系統管理員還能進一步保護其租用戶，並使用存取權檢閱和警示等 PIM 功能來符合合規性。

如需詳細資訊，請參閱[什麼是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="roles-that-can-be-managed-by-pim"></a>PIM 所能管理的角色

**Azure AD 角色**– 全都放在 Azure Active Directory （例如全域系統管理員、 Exchange 系統管理員和安全性系統管理員） 是這些角色。 您可以在 [Azure Active Directory 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)中深入了解這些角色及其功能。 如需協助以便判斷要對系統管理員指派哪些角色，請參閱[依工作區分的最低特殊權限角色](../users-groups-roles/roles-delegate-by-task.md)。

**Azure 資源角色** - 這些角色會連結至 Azure 資源、資源群組、訂用帳戶或管理群組。 PIM 同時針對內建角色 (例如，擁有者、使用者存取系統管理員和參與者) 以及[自訂角色](../../role-based-access-control/custom-roles.md)提供 Just-In-Time 存取。 如需 Azure 資源角色的詳細資訊，請參閱[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)。

如需詳細資訊，請參閱[您無法在 PIM 中管理的角色](pim-roles.md)。

## <a name="step-2-plan-your-deployment"></a>步驟 2. 規劃您的部署

本節內容聚焦在您於組織中部署 PIM 之前所需做好的準備。 請務必遵循文中指示來進行，並了解本節中的概念，因為其可引導您建立專為組織的特殊權限身分識別所打造的最佳規劃。

### <a name="identify-your-stakeholders"></a>識別專案關係人

下面這節可協助您識別專案所涉及，且需要簽字、審核或掌握最新消息的所有專案關係人。 文中包含不同的資料表，分別適用於為 Azure AD 角色部署 PIM 以及為 Azure 資源角色部署 PIM。 請將專案關係人新增至下列適用於貴組織的資料表。

- SO = 簽字同意此專案
- R = 審核此專案，並提供輸入
- I = 知悉此專案的進度

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>專案關係人：適用於 Azure AD 角色的 PIM

| 名稱 | 角色 | 動作 |
| --- | --- | --- |
| 名稱和電子郵件 | **身分識別架構設計師或 Azure 全域系統管理員**<br/>身分識別管理小組所推派的代表人員，負責定義這項變更要如何與組織的核心身分識別管理基礎結構相配合。 | SO/R/I |
| 名稱和電子郵件 | **服務擁有者/部門經理**<br/>單一或一組服務的 IT 擁有者所推派的代表人員。 這些人員是為其團隊做出決策並協助推廣 PIM 的關鍵人物。 | SO/R/I |
| 名稱和電子郵件 | **安全性擁有者**<br/>安全性團隊所推派的代表人員，可簽字同意該規劃符合組織的安全性需求。 | SO/R |
| 名稱和電子郵件 | **IT 支援管理員/技術服務人員**<br/>IT 支援組織所推派的代表人員，可從技術服務人員的觀點就是否能支援這項變更來提出看法。 | R/I |
| 試驗使用者的名稱和電子郵件 | **特殊權限角色使用者**<br/>作為特殊權限身分識別管理機制實作對象的一群使用者。 他們必須了解如何在 PIM 完成實作後啟動其角色。 | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>專案關係人：Azure 資源角色的 PIM

| 名稱 | 角色 | 動作 |
| --- | --- | --- |
| 名稱和電子郵件 | **訂用帳戶/資源擁有者**<br/>要為其部署 PIM 的每個訂用帳戶或資源，其 IT 擁有者所推派的代表人員 | SO/R/I |
| 名稱和電子郵件 | **安全性擁有者**<br/>安全性團隊所推派的代表人員，可簽字同意該規劃符合組織的安全性需求。 | SO/R |
| 名稱和電子郵件 | **IT 支援管理員/技術服務人員**<br/>IT 支援組織所推派的代表人員，可從技術服務人員的觀點就是否能支援這項變更來提出看法。 | R/I |
| 試驗使用者的名稱和電子郵件 | **RBAC 角色使用者**<br/>作為特殊權限身分識別管理機制實作對象的一群使用者。 他們必須了解如何在 PIM 完成實作後啟動其角色。 | I |

### <a name="enable-pim"></a>啟用 PIM

在規劃時，您必須先藉由遵循[開始使用 PIM](pim-getting-started.md) 文件的指示，來同意並啟用 PIM。 啟用 PIM 後，您就可以存取某些特別設計來協助您完成部署的功能。

如果您的目標是要為 Azure 資源部署 PIM，請遵循[在 PIM 中探索要管理的 Azure 資源](pim-resource-roles-discover-resources.md)文件的指示。 只有資源、資源群組和訂用帳戶的擁有者才能夠在 PIM 內探索這些項目。 如果您是全域系統管理員想要部署您的 Azure 資源的 PIM，您可以[提高管理所有 Azure 訂用帳戶的存取權限](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)，讓您有存取目錄中的所有 Azure 資源進行探索。 不過，我們的建議是，先尋求每個訂用帳戶擁有者的同意，再使用 PIM 管理其資源。

### <a name="enforce-principle-of-least-privilege"></a>強制執行最低權限原則

請務必確定您已在組織中針對 Azure AD 和 Azure 資源角色強制執行最低權限原則。

#### <a name="azure-ad-roles"></a>Azure AD 角色

針對 Azure AD 角色，組織通常會指派全域系統管理員角色給大量的系統管理員，但大部分的系統管理員其實只需要一兩個特定的系統管理員角色。 特殊權限角色的指派往往也沒人管理。

> [!NOTE]
> 委派角色時的常見陷阱有：
>
> - 負責 Exchange 的系統管理員獲得全域系統管理員角色，但他們其實只需要 Exchange 系統管理員角色就能執行日常工作。
> - 因為 Office 系統管理員同時需要安全性系統管理員和 SharePoint 系統管理員角色，而對其指派全域系統管理員角色，但只委派一個角色會更容易些。
> - 系統管理員很久以前獲派安全性系統管理員角色以便執行工作，但卻從未移除。

請遵循下列步驟來為 Azure AD 角色強制執行最低權限原則。

1. 閱讀及了解[可用的 Azure AD 系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)，來了解角色的細微性。 您和您的小組也應該參考 [Azure AD 中依識別工作區分的系統管理員角色](../users-groups-roles/roles-delegate-by-task.md)，文內會說明特定工作的最低特殊權限角色。

1. 列出組織中具有特殊權限角色的人員。 您可以使用 [PIM 精靈](pim-security-wizard.md#run-the-wizard)來前往如下所示的頁面。

    ![探索特殊權限角色](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. 針對組織中的所有全域系統管理員，了解其為何需要該角色。 根據前面的文件所述，如果該人員的工作可由一或多個細微的系統管理員角色執行，您就應該將其從全域系統管理員角色移除，並據此在 Azure Active Directory 內進行指派 (供您參考：Microsoft 目前只有大約 10 個系統管理員具有全域系統管理員角色。 請深入了解 [Microsoft 如何使用 PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access))。

1. 對於其他所有 Azure AD 角色，請檢閱指派清單、識別不再需要該角色的系統管理員，並從其指派中將其移除。

若要自動執行步驟 3 和 4，您可以利用 PIM 內的存取權檢閱功能。 藉由遵循[在 PIM 中開始 Azure AD 角色的存取權檢閱](pim-how-to-start-security-review.md)中的步驟，即可為每個具有一或多個成員的 Azure AD 角色設定存取權檢閱。

![建立存取權檢閱](./media/pim-deployment-plan/create-access-review.png)

請將檢閱者設定為 [成員 (本身)]  。 這會對屬於該角色的所有成員傳送電子郵件，讓這些成員確認其是否需要此存取權。 也請開啟進階設定中的 [需要核准的原因]  ，讓使用者可以陳述其為何需要該角色。 根據這項資訊，您就能夠將使用者從不必要的角色中移除，並對其委派更細微的系統管理員角色 (就全域系統管理員來說)。

存取權檢閱會依靠電子郵件，來通知相關人員檢閱其對於角色的存取權。 如果您的特殊權限帳戶未與電子郵件連結，請務必在這些帳戶上填入次要電子郵件欄位。 如需詳細資訊，請參閱 [Azure AD 中的 proxyAddresses 屬性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)。

#### <a name="azure-resource-roles"></a>Azure 資源角色

針對 Azure 訂用帳戶和資源，您可以設定類似的存取權檢閱程序來檢閱每個訂用帳戶或資源中的角色。 此程序的目標是要讓連結至每個訂用帳戶或資源的擁有者和使用者存取系統管理員指派減到最少，以及要移除不必要的指派。 不過，組織通常會將這類工作委派給每個訂用帳戶或資源的擁有者，因為其對特定角色的了解更深 (特別是自訂角色)。

如果您是具有全域管理員角色的 IT 系統管理員嘗試將部署適用於您組織中的 Azure 資源的 PIM，您可以[提高管理所有 Azure 訂用帳戶的存取權限](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)來取得每個訂用帳戶的存取。 然後，您可以尋找每個訂用帳戶的擁有者，然後與其合作來移除不必要的指派，並將擁有者角色的指派數量減到最少。

具有 Azure 訂用帳戶擁有者角色的使用者也可利用 [Azure 資源的存取權檢閱](pim-resource-roles-start-access-review.md)，來稽核和移除不必要的角色指派，方法類似稍早針對 Azure AD 角色所述的程序。

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>決定應該讓 PIM 保護哪些角色指派

在清除組織中的特殊權限角色指派後，您必須決定要使用 PIM 保護哪些角色。

如果某個角色受到 PIM 保護，則指派成為該角色的合格使用者就必須提高其權限，才能使用該角色所授與的特殊權限。 提高權限的程序中也可包含取得核准、執行多重要素驗證和/或提供啟動原因的機制。 PIM 也可透過通知和 PIM 與 Azure AD 的稽核事件記錄來追蹤權限提升事件。

選擇要使用 PIM 保護哪些角色並不是件容易的事，而且每個組織的情形各不相同。 本節會針對 Azure AD 和 Azure 資源角色提供我們的最佳做法建議。

#### <a name="azure-ad-roles"></a>Azure AD 角色

請務必設定在保護具有最多權限的 Azure AD 角色時所應有的優先順序。 根據所有 PIM 客戶的使用模式，由 PIM 管理的前 10 大 Azure AD 角色如下：

1. 全域管理員
1. 安全性系統管理員
1. 使用者管理員
1. Exchange 系統管理員
1. SharePoint 管理員
1. Intune 管理員
1. 安全性讀取者
1. 服務管理員
1. 計費管理員
1. 商務用 Skype 的管理員

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您使用 PIM 作為管理所有全域系統管理員和安全性系統管理員的第一個步驟，因為這些系統管理員是遭到入侵時會造成最大損害的人員。

請務必想好，對於貴組織來說最為敏感的資料和權限是什麼。 舉例來說，某些組織可能會想用 PIM 保護其 Power BI 系統管理員角色或其小組系統管理員角色，因為這些角色可以存取資料和/或變更核心的工作流程。

如果有任何指派了來賓使用者的角色，其特別容易受到攻擊。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您使用 PIM 來管理所有具有來賓使用者的角色，以降低來賓使用者帳戶遭到入侵時所可能衍生的相關風險。

一般認為，相較於其他角色，有時候目錄讀取者、訊息中心讀取者和安全性讀取者等讀取者角色的重要性較低，原因是這些讀取者角色沒有寫入權限。 不過，我們發現部分客戶也會保護這些角色，因為獲得這些帳戶存取權的攻擊者或許就能讀取敏感性資料，例如個人識別資訊 (PII)。 在決定是否需要使用 PIM 來管理組織中的讀取者角色時，請將這一點納入考量。

#### <a name="azure-resource-roles"></a>Azure 資源角色

在決定應使用 PIM 管理 Azure 資源的哪些角色指派時，您必須先識別對組織最為重要的訂用帳戶/資源。 舉例來說，這類訂用帳戶/資源有：

- 裝載最敏感資料的資源
- 核心的客戶面向應用程式所依賴的資源

如果您是全域系統管理員，且無法決定哪些訂用帳戶/資源最為重要，請與組織中的訂用帳戶擁有者聯繫，以收集每個訂用帳戶所管理資源的清單。 然後，請與訂用帳戶擁有者合作，根據這些資源遭到入侵時的嚴重性層級 (低、中、高) 來將這些資源分組。 請根據此嚴重性層級來設定使用 PIM 管理這些資源時的優先順序。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您與重要服務的訂用帳戶/資源擁有者合作，來為敏感訂用帳戶/資源內的所有角色設定 PIM 工作流程。

Azure 資源的 PIM 可支援有時間限制的服務帳戶。 在對待服務帳戶時，態度應與對待一般使用者帳戶時完全相同。

對於不是那麼重要的訂用帳戶/資源，則不必為所有角色設定 PIM。 不過，仍應使用 PIM 保護擁有者和使用者存取系統管理員角色。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您使用 PIM 管理所有訂用帳戶/資源的擁有者角色和使用者存取系統管理員角色。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>決定哪些角色指派應該是永久的或符合資格的

在決定好要由 PIM 管理的角色清單後，您必須決定哪些使用者應該獲得符合資格的角色或永久有效的角色。 永久有效的角色是透過 Azure Active Directory 和 Azure 資源指派的一般角色，符合資格的角色則只能在 PIM 中指派。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您除了建議的[兩個急用緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)外，就不要再為 Azure AD 角色和 Azure 資源角色提供任何永久有效的指派，因為這兩個帳戶應該就有永久性的全域系統管理員角色。

雖然我們建議不要有永久性的系統管理員，但組織有時難以立即實現這一點要求。 以下是進行這項決策時所要考量的事項：

- 提高權限的頻率 - 如果使用者只需要一次特殊權限指派，就不應擁有永久性指派。 反之，如果使用者需要該角色才能執行日常工作，且使用 PIM 會大幅降低其生產力，則可以考慮讓其擁有永久性角色。
- 組織特有情況 - 如果獲得合格角色的人員來自位處遠方的小組或其身分是高階主管，而讓溝通和強制執行提高權限的程序變得困難，則可以考慮讓其擁有永久性角色。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您對擁有永久性角色指派的使用者 (如果有的話) 設定週期性的存取權檢閱。 請至本部署規劃的最後一節來深入了解週期性的存取權檢閱

### <a name="draft-your-pim-settings"></a>草擬 PIM 設定

在實作 PIM 解決方案之前，最好先針對組織所使用的每個特殊權限角色草擬 PIM 設定。 本節會提出特殊角色的一些 PIM 設定範例 (這些範例僅供參考，可能會與貴組織的設定不同)。 在資料表後面，會有這些設定各自的詳細說明和 Microsoft 的建議。

#### <a name="pim-settings-for-azure-ad-roles"></a>Azure AD 角色的 PIM 設定

| 角色 | 需要 MFA | 通知 | 事件票證 | 需要核准 | 核准者 | 啟動持續時間 | 永久性系統管理員 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 全域管理員 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 其他全域系統管理員 | 1 小時 | 緊急存取帳戶 |
| Exchange 系統管理員 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2 小時 | None |
| 服務台系統管理員 | :x: | :x: | :heavy_check_mark: | :x: | None | 8 小時 | None |

#### <a name="pim-settings-for-azure-resource-roles"></a>Azure 資源角色的 PIM 設定

| 角色 | 需要 MFA | 通知 | 需要核准 | 核准者 | 啟動持續時間 | 有效系統管理員 | 有效到期日 | 合格到期日 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 重要訂用帳戶的擁有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 訂用帳戶的其他擁有者 | 1 小時 | None | n/a | 3 個月 |
| 較不重要訂用帳戶的使用者存取系統管理員 | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1 小時 | None | n/a | 3 個月 |
| 虛擬機器參與者 | :x: | :heavy_check_mark: | :x: | None | 3 小時 | None | n/a | 6 個月 |

下表說明每個設定。

| 設定 | 描述 |
| --- | --- |
| 角色 | 要為其定義設定的角色名稱。 |
| 需要 MFA | 符合資格的使用者是否必須先執行 MFA 才能啟動該角色。<br/><br/> :heavy_check_mark:**Microsoft 建議**您對所有系統管理員角色 (尤其是如果該角色有來賓使用者) 強制執行 MFA。 |
| 通知 | 如果設定為 true，則組織中的全域系統管理員、特殊權限角色系統管理員和安全性系統管理員會在符合資格的使用者啟動該角色時收到電子郵件通知。<br/><br/>**附註：** 某些組織並未將電子郵件地址繫結至其系統管理員帳戶，若要取得這些電子郵件通知，請設定替代的電子郵件地址，以便讓系統管理員能夠收到這些電子郵件。 |
| 事件票證 | 符合資格的使用者是否必須在啟動其角色時記錄下事件票證號碼。 此設定可協助組織透過內部事件號碼來識別每個啟動，以減少不必要的啟動。<br/><br/> :heavy_check_mark:**Microsoft 建議**您利用事件票證號碼，以讓 PIM 與內部系統繫結在一起。 這特別適用於需要知道啟動情境的核准者。 |
| 需要核准 | 符合資格的使用者是否必須先獲得核准才能啟動該角色。<br/><br/> :heavy_check_mark:**Microsoft 建議**您為具有最多權限的角色設定核准機制。 根據所有 PIM 客戶的使用模式，全域系統管理員、使用者系統管理員、Exchange 系統管理員、安全性系統管理員和密碼系統管理員是會設定核准機制的最常見角色。 |
| 核准者 | 如果需要核准才能啟動符合資格的角色，請列出應該核准要求的人員。 根據預設，PIM 會將核准者設定為所有屬於特殊權限角色系統管理員 (無論其為永久性或符合資格的系統管理員) 的使用者。<br/><br/>**附註：** 如果使用者同時有資格成為 Azure AD 角色和該角色的核准者，則無法核准其自身。<br/><br/> :heavy_check_mark:**Microsoft 建議**您在選擇核准者時，要找最了解特定角色和其經常性使用者的人員，而不是找全域系統管理員。 |
| 啟動持續時間 | 使用者可以在到期前以該角色啟動的時間長度。 |
| 永久性系統管理員 | 將成為該角色永久性 (永遠不必啟動) 系統管理員的使用者清單。<br/><br/> :heavy_check_mark:**Microsoft 建議**您除了全域系統管理員外，所有角色都不要有永久性的系統管理員。 請於本規劃的應該讓誰符合資格以及應該讓誰永久有效的章節中深入了解這方面的資訊。 |
| 有效系統管理員 | 針對 Azure 資源，有效的系統管理員是永遠不需要啟動就能使用該角色的使用者清單。 這並不是像 Azure AD 角色中的永久性系統管理員，因為您可以設定到期時間來讓使用者到時候失去此角色。 |
| 有效到期日 | 在這個設定好的期間過後，Azure 資源角色的有效角色指派就會過期。 您可以選擇 15 天、1 個月、3 個月、6 個月、1 年或永久有效。 |
| 合格到期日 | 在這個設定好的期間過後，Azure 資源角色的合格角色指派就會過期。 您可以選擇 15 天、1 個月、3 個月、6 個月、1 年或永久合格。 |

## <a name="step-3-implement-your-solution"></a>步驟 3. 實作您的解決方案

有了適當的規劃後，您就可以據此成功地使用 Azure Active Directory 來部署應用程式。  此基礎可提供智慧型的安全性和整合，既能簡化登入，又能降低成功完成部署所需的時間。  兩者相結合，便可確保應用程式能夠輕鬆整合，同時減少使用者所遇到的停機時間。

### <a name="identify-test-users"></a>識別測試使用者

請使用本節，識別用以驗證此實作的一組使用者或使用者群組。 根據您在規劃章節所選取的設定，識別您想要針對每個角色來測試的使用者。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您讓每個 Azure AD 角色的服務擁有者成為測試使用者，以便其可先熟悉此程序，而成為內部鼓吹推廣此實作的擁護者。

在下表中，識別會確認每個角色的設定有效的測試使用者。

| 角色名稱 | 測試使用者 |
| --- | --- |
| &lt;角色名稱&gt; | &lt;要測試角色的使用者&gt; |
| &lt;角色名稱&gt; | &lt;要測試角色的使用者&gt; |

### <a name="test-implementation"></a>測試實作

現在，您已識別出測試使用者，接下來請使用此步驟來為測試使用者設定 PIM。 如果貴組織想要將 PIM 工作流程併入您自己的內部應用程式，而不是在 Azure 入口網站內使用 PIM 的使用者介面，則透過[圖形 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root) 也可支援 PIM 中的所有作業。

#### <a name="configure-pim-for-azure-ad-roles"></a>為 Azure AD 角色設定 PIM

1. [設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)根據您計劃。

1. 瀏覽至 **Azure AD 角色**，按一下 [角色]  ，然後選取您剛才設定的角色。

1. 針對測試使用者群組，如果他們已經是永久性系統管理員，則您可以藉由搜尋這些系統管理員，並將其從永久性轉換為符合資格 (藉由按一下其資料列上的三個點)，來使其成為符合資格的系統管理員。 如果這些系統管理員還未具有角色指派，則您可以[進行新的合格指派](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)。

1. 針對您想要測試的所有角色重複執行步驟 1 至 3。

1. 在設定好測試使用者後，請傳送如何[啟動其 Azure AD 角色](pim-how-to-activate-role.md)的連結給這些使用者。

#### <a name="configure-pim-for-azure-resource-roles"></a>為 Azure 資源角色設定 PIM

1. 為訂用帳戶或資源內要測試的角色[設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)。

1. 瀏覽至該訂用帳戶的 **Azure 資源**，然後按一下 [角色]  ，並選取您剛才設定的角色。

1. 針對測試使用者群組，如果他們已經是有效系統管理員，則您可以藉由搜尋這些系統管理員來使其成為符合資格的系統管理員，然後[更新其角色指派](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)。 如果這些系統管理員還未具有該角色，則您可以[指派新的角色](pim-resource-roles-assign-roles.md#assign-a-role)。

1. 針對您想要測試的所有角色重複執行步驟 1 至 3。

1. 在設定好測試使用者後，請傳送如何[啟動其 Azure 資源角色](pim-resource-roles-activate-your-roles.md)的連結給這些使用者。

請使用這個階段來確認您為角色設定的所有組態是否可正常運作。 請使用下表來記載測試。 也請使用這個階段來將與受影響使用者的溝通方式最佳化。

| 角色 | 啟動期間的預期行為 | 實際結果 |
| --- | --- | --- |
| 全域管理員 | (1) 需要 MFA<br/>(2) 需要核准<br/>(3) 核准者收到通知並可核准<br/>(4) 角色在預設時間過後到期 |  |
| 訂用帳戶 X  的擁有者 | (1) 需要 MFA<br/>(2) 符合資格的指派在所設定的期間過後到期 |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>將 PIM 傳達給受影響的專案關係人

部署 PIM 會對特殊權限角色的使用者帶來額外步驟。 雖然 PIM 可大幅減少與特殊權限身分識別相關聯的安全性問題，但在部署到整個租用戶之前，您必須有效地傳達這項變更。 根據受影響的系統管理員數目，組織通常會選擇建立關於此變更的內部文件、影片或電子郵件。 這些溝通內容中經常包含：

- 何謂 PIM
- 對組織有何好處
- 誰會受到影響
- 何時會推廣 PIM
- 使用者必須另外執行哪些步驟才能啟動其角色
    - 您應該傳送文件的連結：
    - [啟動 Azure AD 角色](pim-how-to-activate-role.md)
    - [啟動 Azure 資源角色](pim-resource-roles-activate-your-roles.md)
- 可供解決 PIM 相關問題的連絡資訊或技術服務人員連結

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您安排時間帶領技術服務人員/支援小組逐步了解 PIM 的工作流程 (如果貴組織有內部的 IT 支援小組的話)。 請向這些人提供適當的文件以及您的連絡資訊。

### <a name="move-to-production"></a>移動至生產環境

在測試完成並成功後，請針對 PIM 組態中每個已定義角色的所有使用者重複執行測試階段中的所有步驟，以便將 PIM 移到生產環境。 對於 Azure AD 角色的 PIM，組織通常會先針對全域系統管理員測試和推廣 PIM，再為其他角色測試和推廣 PIM。 而對於 Azure 資源，組織一般會一次針對一個 Azure 訂用帳戶測試和推廣 PIM。

### <a name="in-the-case-a-rollback-is-needed"></a>在需要復原的情況下

如果 PIM 在生產環境中無法如您所想地運作，下列復原步驟可協助您還原為設定 PIM 之前的已知良好狀態：

#### <a name="azure-ad-roles"></a>Azure AD 角色

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。
1. 按一下 [Azure AD 角色]  ，然後按一下 [角色]  。
1. 針對您已設定的每個角色，對具有合格指派的所有使用者按一下省略符號 ( **...** )。
1. 按一下 [設為永久]  選項讓角色指派具有永久性。

#### <a name="azure-resource-roles"></a>Azure 資源角色

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。
1. 按一下 [Azure 資源]  ，然後按一下您想要復原的訂用帳戶或資源。
1. 按一下 [角色]  。
1. 針對您已設定的每個角色，對具有合格指派的所有使用者按一下省略符號 ( **...** )。
1. 按一下 [設為永久]  選項讓角色指派具有永久性。

## <a name="step-4-next-steps-after-deploying-pim"></a>步驟 4. 部署 PIM 之後的後續步驟

成功在生產環境中部署 PIM，等於往保護組織的特殊權限身分識別跨出一大步。 部署 PIM 也會帶來額外的 PIM 功能，請將其用於確保安全性與合規性。

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>使用 PIM 警示來保護特殊權限的存取

請利用 PIM 的內建警示功能來加強保護租用戶。 如需詳細資訊，請參閱[安全性警訊](pim-how-to-configure-security-alerts.md#security-alerts)。 這些警示包含：系統管理員未使用特殊權限角色、在 PIM 外指派角色、角色遭到頻繁啟動等等。 若要完整保護組織，請定期瀏覽警示清單並修正問題。 您可以透過下列方式檢視和修正警示：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。
1. 按一下 [Azure AD 角色]  ，然後按一下 [警示]  。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您立即處理所有標示為高度嚴重性的警示。 至於中度與低度的嚴重性警示，則請隨時了解情況，並在您認為有安全性威脅的時候進行變更。

如果有任何特定警示沒有用或不適用於貴組織，您一律可以在警示頁面上關閉警示。 您之後隨時可以在 Azure AD 的設定頁面中還原此關閉動作。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>設定週期性的存取權檢閱以便定期稽核貴組織的特殊權限身分識別

若想詢問獲派特殊權限角色的使用者或要求特定檢閱者每位使用者是否需要特殊權限身分識別，最好的方法就是存取權檢閱。 如果您想要減少受攻擊面並持續符合規範，存取權檢閱非常實用。 如需如何啟動存取權檢閱的詳細資訊，請參閱 [Azure AD 角色的存取權檢閱](pim-how-to-start-security-review.md)和 [Azure 資源角色的存取權檢閱](pim-resource-roles-start-access-review.md)。 某些組織必須執行定期的存取權檢閱，以便持續遵守法律及法規，至於其他人，若要在組織中全面強制執行最低權限原則，存取權檢閱會是最好的方式。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您為所有 Azure AD 和 Azure 資源角色設定每季的存取權檢閱。

在大部分情況下，Azure AD 角色的檢閱者是使用者本身，而 Azure 資源角色的檢閱者是該角色所在訂用帳戶的擁有者。 不過，擁有特殊權限帳戶的公司往往未與任何特定人員的電子郵件地址連結。 在這些情況下，就沒有人會讀取並檢閱該存取權。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您針對所有具有特殊權限角色指派 (且未連結至定期檢查的電子郵件地址) 的帳戶，新增次要電子郵件地址

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>充分利用稽核記錄來改善安全性與合規性

稽核記錄可讓您隨時掌握最新情況和並符合法規。 目前，PIM 會在其稽核記錄內儲存貴組織 30 天內的所有記錄，包括：

- 合格角色的啟動/停用
- PIM 內外的角色指派活動
- 角色設定的變更
- 角色啟動 (設定了核准機制) 的要求/核准/拒絕活動
- 警示的更新

如果您是全域系統管理員或特殊權限角色系統管理員，就可以存取這些稽核記錄。 如需詳細資訊，請參閱 [Azure AD 角色的稽核記錄](pim-how-to-use-audit-log.md)和 [Azure 資源角色的稽核記錄](azure-pim-resource-rbac.md)。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您每週讓至少一位系統管理員看過所有稽核事件，並每月匯出稽核事件。

如果您想要長期自動儲存稽核事件，PIM 的稽核記錄會自動同步到 [Azure AD 稽核記錄](../reports-monitoring/concept-audit-logs.md)中。

> [!TIP]
> :heavy_check_mark:**Microsoft 建議**您設定 [Azure 記錄監視](../reports-monitoring/concept-activity-logs-azure-monitor.md)，將稽核事件封存在 Azure 儲存體帳戶中，以滿足安全性與合規性的需要。
