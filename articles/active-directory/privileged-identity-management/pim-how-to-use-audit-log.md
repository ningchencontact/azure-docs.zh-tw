---
title: 在 PIM-Azure Active Directory 中檢視稽核記錄的 Azure AD 角色 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中檢視 Azure AD 角色的稽核歷程記錄。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053966"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>檢視稽核記錄的 Azure AD PIM 中的角色

若要查看所有角色指派及啟用所有特殊權限的角色在過去 30 天內，您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 稽核歷程記錄。 如果您想要查看完整的稽核活動的歷程記錄中您的目錄，包括系統管理員、 使用者和同步處理活動，您可以使用[Azure Active Directory 安全性及活動報告](../reports-monitoring/overview-reports.md)。

## <a name="view-audit-history"></a>檢視稽核記錄

請遵循下列步驟來檢視 Azure AD 角色的稽核歷程記錄。

1. 以屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]  。

1. 按一下 **目錄角色稽核歷程記錄**。

    根據您的稽核歷程記錄，直條圖會顯示啟用總數、 最大啟用每日及每天平均啟用數目。

    ![目錄角色稽核歷程記錄](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在頁面底部，資料表會顯示可用的稽核歷程記錄中的每個動作的相關資訊。 資料行具有下列意義：

    | 欄 | 描述 |
    | --- | --- |
    | Time | 當發生的動作。 |
    | 要求者 | 要求啟用或變更角色的使用者。 如果值為**Azure 系統**，請檢查 Azure 稽核記錄，如需詳細資訊。 |
    | 動作 | 要求者所採取的動作。 動作包括指派、 取消指派、 啟用、 停用或 AddedOutsidePIM。 |
    | member | 使用者會啟動，或指派給角色。 |
    | 角色 | 角色指派，或由使用者啟用。 |
    | 推理 | 在啟用期間輸入的 [原因] 欄位的文字。 |
    | 到期 | 當到期已啟用的角色。 僅適用於合格角色指派。 |

1. 若要排序的稽核歷程記錄，請按一下**時間**，**動作**，並**角色**按鈕。

## <a name="filter-audit-history"></a>篩選稽核記錄

1. 在 稽核記錄 頁面頂端，按一下**篩選** 按鈕。

    **更新圖表參數**窗格隨即出現。

1. 在 **時間範圍**，選取時間範圍。

1. 在 **角色**，新增您想要檢視角色的核取記號。

    ![更新圖表參數 窗格](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 按一下 **完成**檢視篩選的稽核歷程記錄。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中檢視 Azure 資源角色的活動和稽核記錄](azure-pim-resource-rbac.md)
