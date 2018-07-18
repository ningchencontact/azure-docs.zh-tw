---
title: 使用 Privileged Identity Management 啟用適用於 Azure 資源的角色 | Microsoft Docs
description: 說明如何在 PIM 中啟用角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 483f30a6546cf21d93b96b08eede6afae828d9e4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548299"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 啟用適用於 Azure 資源的角色
Privileged Identity Management (PIM) 在啟用適用於 Azure 資源的角色方面引進了全新體驗。 符合資格的角色成員可以排程在未來的日期和時間啟用。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。 如需更多資訊，請參閱 [Azure AD Privileged Identity Management：如何啟用或停用角色](pim-how-to-activate-role.md)。

## <a name="activate-roles"></a>啟用角色
瀏覽至左側窗格中 [我的角色] 區段。 針對您想要啟用的角色選取 [啟用]。

![[我的角色] 窗格中的 [符合資格的角色] 索引標籤。](media/azure-pim-resource-rbac/rbac-roles.png)

從 [啟用] 功能表中，輸入開始日期和時間以啟用角色。 選擇性地減少啟用持續時間 (角色的啟用時間長度)，並輸入理由 (如有必要)。 然後選取 [啟用]。

如果未修改開始日期和時間，幾秒內就會啟用該角色。 在 [我的角色] 窗格中，橫幅訊息會顯示角色已排入佇列進行啟用。 選取 [重新整理] 按鈕以清除此訊息。

![含橫幅訊息的 [我的角色] 窗格以及等待核准的相關通知](media/azure-pim-resource-rbac/rbac-activate-notification.png)

如果排定角色在未來日期和時間啟用，暫止的要求會出現在左側窗格的 [擱置要求] 索引標籤。 如果不再需要啟用角色，您可以選取 [取消] 按鈕來取消要求。

![含 [取消] 按鈕的擱置要求清單](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>套用恰到好處的系統管理做法

配合 PIM 的 Azure 資源，您可在指派資源角色時輕易使用恰到好處的系統管理 (JEA) 最佳做法。 在 Azure 訂用帳戶或資源群組中具有指派的使用者與群組成員，可在較小的範圍內啟用其現有的角色指派。 

從 [搜尋] 頁面中，尋找您需要管理的次級資源。

![選取一項資源](media/azure-pim-resource-rbac/azure-resources-02.png)

從左側窗格中選取 [我的角色]，然後選擇適當的角色以啟用。 系統會**繼承**指派類型，因為角色已在訂用帳戶中獲指派，而非資源群組中。

![符合資格的角色指派清單，指派類型會反白顯示](media/azure-pim-resource-rbac/my-roles-02.png)
