---
title: 適用於 Azure 資源的 Privileged Identity Management - 啟用角色 | Microsoft Docs
description: 說明如何在 PIM 中啟用角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - 資源角色 - 啟用
啟用 Azure 資源的角色可導入新的體驗，讓合格的角色成員能夠排定未來日期/時間的啟用，並可在上限 (由系統管理員設定) 範圍內選取特定的啟用持續時間。 了解[如何啟用 Azure AD 角色](../active-directory-privileged-identity-management-how-to-activate-role.md)。

## <a name="activate-roles"></a>啟用角色
巡覽至左導覽列上的 [我的角色] 區段。 對您想要啟用角色按一下 [啟用]。
![](media/azure-pim-resource-rbac/rbac-roles.png)

從 [啟用] 功能表中，輸入所需的開始日期和時間以啟用角色。 選擇性地減少啟用持續時間 (角色的啟用時間長度)，並輸入理由 (如有必要)；然後按一下 [啟用]。

如果未修改開始日期和時間，將會在數秒內啟用角色。 在 [我的角色] 頁面上，您會看到角色已排入啟用橫幅訊息的佇列。 按一下 [重新整理] 按鈕以清除此訊息。

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

如果排定角色在未來日期時間啟用，暫止的要求會出現在左側導覽功能表的 [擱置要求] 索引標籤。 在事件中，已不再需要再啟用角色，使用者可透過按一下頁面右側的 [取消] 按鈕取消要求。

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>恰到好處的系統管理

配合 PIM 的 Azure 資源，您可在指派資源角色時輕易使用剛恰到好處的系統管理 (JEA) 最佳作法。 使用者和群組與 Azure 訂用帳戶或資源群組中指派的成員，可以現有角色指派在較小的範圍內啟用。 

從 [搜尋] 頁面中，尋找您需要管理的次及資源。

![](media/azure-pim-resource-rbac/azure-resources-02.png)

從左側導覽功能表中選取 [我的角色]，然後選擇適當的角色以啟用。 請註意，系統會繼承指派類型，因為角色已在訂用帳戶中獲指派，而非資源群組中 (如下所示)。

![](media/azure-pim-resource-rbac/my-roles-02.png)
