---
title: 適用於 Azure 資源的 Privileged Identity Management - MFA | Microsoft Docs
description: 本文件說明如何啟用 PIM 資源的多重要素驗證。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - 資源角色 - MFA

適用於 Azure 資源的 PIM 角色可讓資源管理員和身分識別管理員利用有時限的成員資格和 Just-In-Time 存取權，來保護重要的 Azure 基礎結構。 此外，PIM 針對兩個不同的案例提供選擇性強制 Azure Multi-Factor Authentication (MFA)。

## <a name="require-mfa-to-activate"></a>需要 MFA 才可啟用

資源管理員可以要求角色的合格成員先成功執行 Azure MFA 才可啟用。 此程序可確保要求啟用的使用者是表明具有合理確定性的人員。 在使用者帳戶可能受到危害的情況下，強制執行這個選項可保護重要資源。 

若要強制執行這項需求，請從受控資源清單中選取資源。 在[概觀儀表板](pim-resource-roles-overview-dashboards.md)中，從畫面右下方的角色清單中選取角色。

此外，您可以從左導覽功能表的 [角色] 或 [角色設定] 索引標籤取得角色設定。

>[!Note]
>如果左導覽功能表中的選項呈現灰色，而且您在頁面頂端看到陳述「您有可啟用的合格角色」的橫幅，您就不是有效的管理員，而且必須先[啟用](pim-resource-roles-activate-your-roles.md)才能繼續執行。

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

如果檢視角色的成員資格，請從畫面頂端列選取 [角色設定] 以開啟 [角色設定詳細資料]。

按一下頂端的 [編輯] 按鈕以修改角色設定。

在 [啟用] 之下的區段中，按一下 [需要多重要素驗證才可啟用] 核取方塊，然後按一下 [儲存]。

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>指派需要 MFA

在某些情況下，資源管理員可以將成員短期 (例如一天) 指派給某個角色，而不需要指派的成員要求啟用。 在此情況下，PIM 無法在成員使用其角色指派時強制執行 MFA，因為從指派他們的那一刻起，他們就已經具備有效的角色。

若要確保執行指派的資源管理員身分屬實，您可以對指派強制執行 MFA。

在相同的 [角色設定詳細資料] 畫面上，核取 [指派需要多重要素驗證] 方塊。

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>後續步驟

[需要核准才可啟用](pim-resource-roles-approval-workflow.md)

[使用稽核記錄](pim-resource-roles-use-the-audit-log.md)



