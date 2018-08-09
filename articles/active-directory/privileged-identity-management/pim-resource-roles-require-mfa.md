---
title: 使用 Privileged Identity Management 在 Azure 資源中強制執行 Azure Multi-Factor Authentication | Microsoft Docs
description: 本文件說明如何啟用 PIM 資源的多重要素驗證。
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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 42c93a107c7ed58d6f7412b61627dccaf9076bb7
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617865"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 在 Azure 資源中強制執行 Azure Multi-Factor Authentication

適用於 Azure 資源角色的 Privileged Identity Management (PIM) 可讓資源管理員和身分識別管理員利用有時限的成員資格和 Just-In-Time 存取權，來保護重要的 Azure 基礎結構。 此外，PIM 可針對兩個不同的案例選擇性地強制執行 Azure Multi-Factor Authentication。

## <a name="require-multi-factor-authentication-to-activate"></a>需要 Multi-Factor Authentication 才能啟用

資源管理員可以要求角色的合格成員先執行 Azure Multi-Factor Authentication，然後才可啟用。 此程序確保要求啟用的使用者是表明具有合理確定性的人員。 在使用者帳戶可能受到危害的情況下，強制執行這個選項可保護重要資源。 

若要強制執行這項需求，請從受控資源清單中選取資源。 在[概觀儀表板](pim-resource-roles-overview-dashboards.md)中，從畫面右下方的角色清單中選取角色。

此外，您可以從左窗格中的 [角色] 或 [角色設定] 索引標籤取得角色設定。

>[!Note]
>如果左窗格中的選項呈現灰色，而且在頁面頂端看到一個橫幅表示「您具有可啟用的合格角色」，則您不是有效的系統管理員。 這表示您必須先[啟用](pim-resource-roles-activate-your-roles.md)才能繼續執行。

![[角色] 和 [角色設定] 索引標籤 ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

若要檢視角色的成員資格，從畫面頂端列選取 [角色設定] 以開啟 [角色設定詳細資料]。

若要修改角色設定，選取頂端的 [編輯] 按鈕。

在 [啟用] 下方的區段中，選取 [啟用時需要 Multi-Factor Authentication] 核取方塊。 然後選取 [儲存]。

![啟用時需要 Multi-Factor Authentication](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>要求為指派執行 Multi-Factor Authentication

在某些情況下，資源管理員可能想要將成員短期 (例如一天) 指派給某個角色。 在此情況下，他們不需要指派的成員要求啟用。 在此案例中，PIM 無法在成員使用其角色指派時強制執行 Multi-Factor Authentication，因為從指派他們的那一刻起，他們就已經具備有效的角色。

若要確保執行指派的資源管理員身分屬實，您可以為指派強制執行 Multi-Factor Authentication。

在相同的 [角色設定詳細資料] 畫面上，核取 [要求為直接指派執行 Multi-Factor Authentication] 方塊。

![要求為直接指派執行 Multi-Factor Authentication](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>後續步驟

[需要核准才可啟用](pim-resource-roles-approval-workflow.md)

[使用稽核記錄](pim-resource-roles-use-the-audit-log.md)



