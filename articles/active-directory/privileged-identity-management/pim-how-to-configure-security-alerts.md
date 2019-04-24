---
title: 在 PIM-Azure Active Directory 中設定 Azure AD 角色的安全性警示 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中設定 Azure AD 角色的安全性警示。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0d99fb283be8cbeba6f8a7954ff49161a2d511
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60288496"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>在 PIM 中設定 Azure AD 角色的安全性警示

在您的環境中有可疑或不安全的活動時，azure Active Directory (Azure AD) Privileged Identity Management (PIM) 會產生警示。 觸發警示時，會顯示在 PIM 儀表板上。 選取警示，以查看詳列觸發警示之使用者或角色的報告。

![PIM 安全性警示 - 螢幕擷取畫面](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>安全性警示

此區段會列出 Azure AD 角色，以及如何修正問題，以及如何避免的所有安全性警示。 嚴重性具有下列意義：

* **高**：因為發生原則違規而需要立即採取行動。
* **中**：不需要立即採取行動，但表示可能發生原則違規。
* **低**：不需要立即採取行動，但建議進行原則變更。

### <a name="administrators-arent-using-their-privileged-roles"></a>系統管理員未使用其特殊權限角色

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 將特殊權限角色指派給不需要的使用者會增加受攻擊的機會。 攻擊者也比較容易忽視未受到積極使用的帳戶。 |
| **如何修正？** | 檢閱清單中的使用者，然後將他們從他們不需要的特殊權限角色中移除。 |
| **預護** | 只將特殊權限角色指派給具有正當業務理由的使用者。 </br>排程定期[存取權檢閱](pim-how-to-start-security-review.md)，以確認使用者仍然需要其存取權。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發程序** | 當使用者一段特定時間未啟用角色時，就會觸發。 |
| **天數** | 此設定指定使用者可以維持不啟用角色的天數 (從 0 到 100)。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色不需要多重要素驗證來進行啟用

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 在沒有 MFA 的情況下，遭到入侵的使用者可以啟用特殊權限角色。 |
| **如何修正？** | 檢閱角色清單並針對每一個角色[要求 MFA](pim-how-to-change-default-settings.md)。 |
| **預護** | 針對每一個角色[要求 MFA](pim-how-to-change-default-settings.md)。  |
| **入口網站內風險降低措施** | 將特殊權限角色設定為需要 MFA 才能啟用。 |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>租用戶不具 Azure AD Premium P2

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 目前的租用戶不具 Azure AD Premium P2。 |
| **如何修正？** | 檢閱 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的相關資訊。 升級至 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>特殊權限角色中的潛在過時帳戶

| | |
| --- | --- |
| **嚴重性** | 中 |
| **為什麼會收到此警示？** | 特殊權限中的角色在過去 90 天內未變更其帳戶密碼。 這些帳戶可能是未受到維護且容易遭到攻擊的服務或共用帳戶。 |
| **如何修正？** | 檢閱清單中的帳戶。 如果他們不再需要存取，請將它們從其特殊權限角色中移除。 |
| **預護** | 確定共用帳戶會在知道密碼的使用者有所變更時，輪替使用強式密碼。 </br>使用[存取權檢閱](pim-how-to-start-security-review.md)功能定期檢閱具備特殊權限角色的帳戶，並移除不再需要的角色指派。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **最佳做法** | 如果共用、服務及緊急存取帳戶均會使用密碼進行驗證，並已指派給具高度特殊權限的系統管理角色 (例如，全域系統管理員或安全性系統管理員)，則應該針對下列情況輪替其密碼：<ul><li>在涉及系統管理存取權限誤用或遭到入侵的安全性事件之後</li><li>在變更任意使用者的權限，使其不再為系統管理員之後 (例如，在先前為系統管理員的員工離開 IT 或離開組織之後)</li><li>定期 (例如每季或每年)，即使沒有任何已知的缺口或 IT 人員的變更也一樣</li></ul>由於多人有權存取這些帳戶的認證，因此，應該輪替認證，以確保已離開其角色的人員無法再存取帳戶。 [深入了解](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>在 PIM 外指派角色

| | |
| --- | --- |
| **嚴重性** | 高 |
| **為什麼會收到此警示？** | 在 PIM 外進行特殊權限角色指派不會受到適當監視，且可能表示正受到攻擊。 |
| **如何修正？** | 檢閱清單中的使用者，然後將他們從在 PIM 外指派的特殊權限角色移除。 |
| **預護** | 調查使用者已在 PIM 外獲指派的特殊權限角色，並禁止未來從該處指派。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |

### <a name="there-are-too-many-global-administrators"></a>全域管理員太多

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 全域管理員是最高特殊權限角色。 如果全域管理員遭到入侵，攻擊者會取得其所有權限的存取權，這會讓整個系統面臨風險。 |
| **如何修正？** | 檢閱清單中的使用者，並移除任何非絕對需要的全域管理員角色。 </br>指派較低特殊權限角色給這些使用者。 |
| **預護** | 將使用者所需的最低特殊權限角色指派給使用者。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發程序** | 當符合兩個不同的條件時就會觸發，而您可以同時設定這兩個條件。 首先，您必須達到特定的全域管理員臨界值。 其次，角色指派總數中必須有特定百分比是全域管理員。 如果只符合這些測量結果其中之一，就不會出現警示。 |
| **全域管理員數目下限** | 此設定會指定您視為不安全數量的全域管理員數目 (從 2 到 100)。 |
| **全域管理員百分比** | 此設定會指定在您環境中視為不安全、擔任全域管理員的系統管理員百分比下限 (從 0% 到 100%)。 |

### <a name="roles-are-being-activated-too-frequently"></a>啟用角色的次數太頻繁

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 同一使用者多次啟用相同的特殊權限角色是受到攻擊的徵兆。 |
| **如何修正？** | 檢閱清單中的使用者，並確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓他們執行工作。 |
| **預護** | 請確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓使用者執行他們的工作。</br>針對具備由多個系統管理員共用帳戶的特殊權限角色[要求 MFA](pim-how-to-change-default-settings.md)。 |
| **入口網站內風險降低措施** | N/A |
| **觸發程序** | 當使用者在指定期間內多次啟用相同的特殊權限角色時，就會觸發。 您可以同時設定時段和啟用次數。 |
| **啟用更新時間範圍** | 此設定會以天、小時、分鐘及秒為單位，指定您想要用來追蹤可疑更新的時段。 |
| **啟用更新次數** | 此設定會指定在您選擇的時間範圍內，您視為值得發出警示的啟用次數 (從 2 到 100)。 您可以移動滑桿或在文字方塊中輸入數字，以變更此設定。 |

## <a name="configure-security-alert-settings"></a>設定安全性警示設定

您可以在 PIM 中自訂一些安全性警示，以便配合您的環境和安全性目標。 請遵循下列步驟開啟安全性警示設定：

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]。

1. 按一下 [設定]，然後按一下 [警示]。

    ![瀏覽至安全性警示設定](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 按一下要設定該警示設定的警示名稱。

    ![安全性警示設定](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
