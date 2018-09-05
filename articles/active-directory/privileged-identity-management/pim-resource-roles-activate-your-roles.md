---
title: 在 PIM 中啟用我的 Azure 資源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中啟用 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189484"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中啟用我的 Azure 資源角色
Privileged Identity Management (PIM) 在啟用適用於 Azure 資源的角色方面引進了全新體驗。 符合資格的角色成員可以排程在未來的日期和時間啟用。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。 如需更多資訊，請參閱 [Azure AD Privileged Identity Management：如何啟用或停用角色](pim-how-to-activate-role.md)。

## <a name="activate-a-role"></a>啟用角色
瀏覽至左側窗格中 [我的角色] 區段。 針對您想要啟用的角色選取 [啟用]。

![[我的角色] 窗格中的 [符合資格的角色] 索引標籤。](media/azure-pim-resource-rbac/rbac-roles.png)

從 [啟用] 功能表中，輸入開始日期和時間以啟用角色。 選擇性地減少啟用持續時間 (角色的啟用時間長度)，並輸入理由 (如有必要)。 然後選取 [啟用]。

如果未修改開始日期和時間，幾秒內就會啟用該角色。 在 [我的角色] 窗格中，橫幅訊息會顯示角色已排入佇列進行啟用。 選取 [重新整理] 按鈕以清除此訊息。

![含橫幅訊息的 [我的角色] 窗格以及等待核准的相關通知](media/azure-pim-resource-rbac/rbac-activate-notification.png)

如果排定角色在未來日期和時間啟用，暫止的要求會出現在左側窗格的 [擱置要求] 索引標籤。 如果不再需要啟用角色，您可以選取 [取消] 按鈕來取消要求。

![含 [取消] 按鈕的擱置要求清單](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>啟用後可立即使用角色

因為快取的關係，啟用不會立即在 Azure 入口網站中生效，必須重新整理畫面。 如果您需要降低啟用角色之後的延遲機率，您可以使用入口網站中的**應用程式存取**頁面。 從此頁面存取的應用程式會立即檢查新的角色指派。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [應用程式存取] 頁面。

    ![PIM 應用程式存取 - 螢幕擷取畫面](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. 按一下 [Azure 資源]，即可重新開啟入口網站的 [所有資源] 頁面。

    當您按一下此連結後，即可強制重新整理，並檢查新的 Azure 資源角色指派。

## <a name="apply-just-enough-administration-practices"></a>套用恰到好處的系統管理做法

配合 PIM 的 Azure 資源，您可在指派資源角色時輕易使用恰到好處的系統管理 (JEA) 最佳做法。 在 Azure 訂用帳戶或資源群組中具有指派的使用者與群組成員，可在較小的範圍內啟用其現有的角色指派。 

從 [搜尋] 頁面中，尋找您需要管理的次級資源。

![選取一項資源](media/azure-pim-resource-rbac/azure-resources-02.png)

從左側窗格中選取 [我的角色]，然後選擇適當的角色以啟用。 系統會**繼承**指派類型，因為角色已在訂用帳戶中獲指派，而非資源群組中。

![符合資格的角色指派清單，指派類型會反白顯示](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中啟用我的 Azure AD 目錄角色](pim-how-to-activate-role.md)