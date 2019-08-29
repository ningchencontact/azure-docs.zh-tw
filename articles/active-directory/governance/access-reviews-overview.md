---
title: 什麼是存取權檢閱？ - Azure Active Directory | Microsoft Docs
description: 您可以使用 Azure Active Directory 的存取權審查, 控制群組成員資格和應用程式存取, 以符合組織中的治理、風險管理和合規性計畫。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe8b50775390d60f75fafeae490e0307912600f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125269"
---
# <a name="what-are-azure-ad-access-reviews"></a>什麼是 Azure AD 存取權檢閱？

Azure Active Directory (Azure AD) 存取審查可讓組織有效率地管理群組成員資格、企業應用程式的存取權, 以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的人員會具有持續存取權。

以下影片會快速地概述存取權檢閱：

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>存取權檢閱為何重要？

Azure AD 可讓您在組織內部以及與外部組織 (例如，合作夥伴) 的使用者共同作業。 使用者可以加入群組、邀請來賓、連線至雲端應用程式，以及從他們的工作或個人裝置遠端工作。 由於能夠方便地運用自助式服務的能力，因此需要更好的存取管理功能。

- 當有新員工加入時，如何確保他們會有適當的存取權，以便發揮生產力？
- 當有員工調動到其他小組或離職時，如何確保他們的舊有存取權會移除，特別是當此存取權涉及來賓時？
- 多餘的存取權限可能會導致稽核後果與洩露，因為這表示公司對於存取權的控制不夠嚴謹。
- 您必須主動與資源擁有者合作，以確保他們會定期檢閱可存取其資源的使用者。

## <a name="when-to-use-access-reviews"></a>何時要使用存取權檢閱？

- **太多使用者具有特殊權限角色：** 建議您檢查有多少使用者擁有系統管理存取權、其中有多少是全域管理員, 以及是否有任何受邀來賓或合作夥伴未在指派執行系統管理工作之後移除。 您可以在[Azure AD 角色](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)(例如全域系統管理員) 或[Azure 資源角色](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)(例如[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)體驗中的使用者存取系統管理員) 中重新認證角色指派使用者。
- **無法自動化時：** 您可以在安全性群組或 Office 365 群組上建立動態成員資格的規則，但如果 HR 資料未放在 Azure AD 中，或如果使用者在離開群組之後依然需要存取權以便訓練其接替者呢？ 這時，您就可以在該群組上建立檢閱，以確保仍需要存取權的人員應該會具有持續存取權。
- **當群組用於新的用途時：** 如果您有要同步處理至 Azure AD 的群組，或如果您計劃讓銷售小組群組中的每個人都能使用 Salesforce 應用程式，則要求群組擁有者先檢閱群組成員資格，再將群組用於不同風險內容中的做法會很有用。
- **商務關鍵性資料存取:** 對於某些資源, 可能需要要求 it 外的人員定期登出, 並提供理由來說明他們為何需要存取以進行審核。
- **為了維護原則的例外狀況清單：** 在理想的世界中, 所有使用者都會遵循存取原則來保護您組織資源的存取。 不過，有時候會有需要您視為例外狀況的商務案例。 身為 IT 系統管理員，您可以管理這項工作、免於監督原則例外狀況，並向稽核人員證明您有定期檢閱這些例外狀況。
- **要求群組擁有者確認他們的群組中仍然需要來賓：** 員工存取權可能會透過一些內部部署 IAM 來自動化, 但不會受邀的來賓。 如果有群組賦予來賓存取商務機密內容的權限，該群組的擁有者就有責任確認其來賓仍有合理獲得存取權的商務需求。
- **反覆定期檢閱：** 您可以設定以一定的頻率 (例如，每週、每月、每季或每年) 週期性地檢閱使用者的存取權，每次檢閱開始時，檢閱者都會收到通知。 檢閱者可以透過容易使用的介面與智慧建議的協助，來核准或拒絕存取權。

## <a name="where-do-you-create-reviews"></a>在哪裡建立檢閱？

根據您想要檢查的內容, 您將會在 Azure AD 存取評論、Azure AD 企業應用程式 (預覽) 或 Azure AD PIM 中建立存取權審查。

| 使用者的存取權限 | 檢閱者可以是 | 檢閱建立於 | 檢閱者體驗 |
| --- | --- | --- | --- |
| 安全性群組成員</br>Office 群組成員 | 指定的檢閱者</br>群組擁有者</br>自我審查 | Azure AD 存取權檢閱</br>Azure AD 群組 | 存取面板 |
| 指派給已連線的應用程式 | 指定的檢閱者</br>自我審查 | Azure AD 存取權檢閱</br>Azure AD 企業應用程式 (預覽版) | 存取面板 |
| Azure AD 角色 | 指定的檢閱者</br>自我審查 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 入口網站 |
| Azure 資源角色 | 指定的檢閱者</br>自我審查 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 入口網站 |


## <a name="onboard-access-reviews"></a>上架存取權檢閱

若要上架存取權審查, 請遵循下列步驟。

1. 身為全域管理員或使用者系統管理員, 請登入您想要使用存取權審查的[Azure 入口網站](https://portal.azure.com)。

1. 在左側導覽列中，按一下 [Azure Active Directory]。

1. 在左側功能表中, 按一下 [身分**識別管理**]。

1. 按一下 [**存取評論**]。
 
    ![存取審查起始頁](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. 在頁面上, 按一下 [**立即上架**] 按鈕。
    
      ![存取上架的評論](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>瞭解存取權審查

若要深入了解如何建立和執行存取權檢閱，請觀看這個簡短示範：

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

如果您已準備好在組織中部署存取權檢閱，請遵循影片中的這些步驟，以便上架、訓練系統管理員，以及建立您的第一個存取權檢閱！

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>哪些使用者必須有授權？

與存取評論互動的每個使用者都必須具有付費 Azure AD Premium P2 授權。 範例包括：

- 建立存取權審查的系統管理員
- 執行存取權審查的群組擁有者
- 指派為審核者的使用者
- 執行自我審查的使用者

您也可以要求來賓使用者檢閱自己的存取權。 針對您指派給貴組織的其中一個使用者的每個付費 Azure AD Premium P2 授權, 您可以使用 Azure AD 的企業對企業 (B2B), 以外部使用者額度來邀請最多五位來賓使用者。 這些來賓使用者也可以使用 Azure AD Premium P2 功能。 如需詳細資訊, 請參閱[AZURE AD B2B 共同作業授權指引](../b2b/licensing-guidance.md)。

以下是一些範例案例, 可協助您判斷您必須擁有的授權數目。

| 狀況 | 計算 | 所需的授權數目 |
| --- | --- | --- |
| 系統管理員會使用500使用者來建立群組 A 的存取權審查。 將3個群組擁有者指派為審查員。 | 1每個群組擁有者的系統管理員 + 3 授權許可權作為審核者。 | 4 |
| 系統管理員會使用500使用者來建立群組 A 的存取權審查。 讓它成為自我審查。 | 1將每位使用者的系統管理員 + 500 授權作為自我審核者。 | 501 |
| 系統管理員會建立群組 B 的存取權審查, 其中有5位使用者和25位來賓使用者。 讓它成為自我審查。 | 1每位使用者的系統管理員 + 5 授權作為自我審核者。<br/>(來賓使用者會以所需的1:5 比例涵蓋) | 6 |
| 系統管理員會建立群組 C 的存取權審查, 其中包含5位使用者和108來賓使用者。 讓它成為自我審查。 | 1每位使用者的系統管理員 + 5 授權 (身為自我審核者 + 16 個), 以所需的1:5 比例涵蓋所有108來賓使用者。<br/>1 + 5 = 6 個授權, 涵蓋 5\*6 = 30 個來賓使用者。 針對剩餘的 (108-5\*6) = 78 個來賓使用者, 需要 78/5 = 16 個額外的授權。 因此, 總共需要 6 + 16 = 22 個授權。 | 22 |

如需如何指派授權給使用者的相關資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

## <a name="next-steps"></a>後續步驟

- [建立群組或應用程式的存取權審查](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [審查群組或應用程式的存取權](perform-access-review.md)
- [完成群組或應用程式的存取權審查](complete-access-review.md)
