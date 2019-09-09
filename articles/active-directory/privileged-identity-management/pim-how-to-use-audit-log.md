---
title: 在 PIM 中查看 Azure AD 角色的 audit 歷程記錄-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中查看 Azure AD 角色的審核歷程記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3547ff218e9a15151e4abe2ceff53292d3f01ac0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804319"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>在 PIM 中查看 Azure AD 角色的審核歷程記錄

您可以使用 Azure Active Directory （Azure AD） Privileged Identity Management （PIM） audit 歷程記錄，查看過去30天內所有特殊許可權角色的所有角色指派和啟用。 如果您想要查看目錄中活動的完整審核歷程記錄（包括系統管理員、使用者和同步處理活動），您可以使用[Azure Active Directory 安全性和活動報告](../reports-monitoring/overview-reports.md)。

## <a name="view-audit-history"></a>檢視稽核記錄

請遵循下列步驟來查看 Azure AD 角色的審核歷程記錄。

1. 以屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]。

1. 按一下 [**目錄角色**] [audit 歷程記錄]。

    根據您的 audit 歷程記錄，會顯示直條圖，以及啟用總數、每天最大啟用數，以及每天的平均啟用。

    ![目錄角色稽核歷程記錄](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在頁面底部，會顯示一個資料表，其中包含可用的審核歷程記錄中每個動作的相關資訊。 這些資料行具有下列意義：

    | 「資料行」 | 描述 |
    | --- | --- |
    | Time | 當動作發生時。 |
    | 要求者 | 要求啟用或變更角色的使用者。 如果值是**Azure 系統**，請查看 azure audit 歷程記錄以取得詳細資訊。 |
    | Action | 要求者所採取的動作。 動作可以包含指派、解除指派、啟用、停用或 AddedOutsidePIM。 |
    | 成員 | 啟用或指派給角色的使用者。 |
    | Role | 使用者指派或啟用的角色。 |
    | 推理 | 啟用期間輸入 [原因] 欄位中的文字。 |
    | 到期 | 當已啟用的角色過期時。 僅適用于合格的角色指派。 |

1. 若要排序 audit 歷程記錄，請按一下 [**時間**]、[**動作**] 和 [**角色**] 按鈕。

## <a name="filter-audit-history"></a>篩選稽核記錄

1. 在 [audit 歷程記錄] 頁面頂端，按一下 [**篩選**] 按鈕。

    [**更新圖表參數**] 窗格隨即出現。

1. 在 [**時間範圍**] 中，選取時間範圍。

1. 在 [**角色**] 中，為您要查看的角色新增核取記號。

    ![更新圖表參數窗格](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 按一下 [**完成**] 以查看篩選過的 audit 歷程記錄。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中檢視 Azure 資源角色的活動和稽核記錄](azure-pim-resource-rbac.md)
