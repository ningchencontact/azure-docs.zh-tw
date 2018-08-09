---
title: 使用 Privileged Identity Management 探索與管理 Azure 資源 | Microsoft Docs
description: 說明如何使用 PIM 保護 Azure 資源。
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
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: c5b26c01028e2a5746132939a2058cacdcad859f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622097"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 探索與管理 Azure 資源

了解當您在 Azure Active Directory (Azure AD) 中使用 Privileged Identity Management (PIM) 時，如何探索與管理 Azure 資源。 此資訊對於已經使用 PIM 來保護系統管理員資源的組織，以及想要保護生產資源的訂用帳戶擁有者而言非常實用。

當您第一次設定適用於 Azure 資源的 PIM 時，必須探索並選取要使用 PIM 保護的資源。 您可以使用 PIM 管理的資源數目並無任何限制。 不過，我們建議從您最重要的 (生產) 資源開始。

> [!NOTE]
> 您只能使用 PIM 來搜尋並選取要管理的訂用帳戶資源。 當您在 PIM 中管理訂用帳戶時，也可以管理該訂用帳戶中的子資源。

## <a name="discover-resources"></a>探索資源

在 Azure 入口網站中，移至 [Privileged Identity Management] 窗格。 在左側功能表的 [管理] 區段中，選取 [Azure 資源]。

![[Privileged Identity Management - Azure 資源] 窗格](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

如果這是您第一次針對 Azure 資源使用 PIM，請先執行探索來尋找要管理的資源。 在 [探索資源] 窗格中，選取 [探索資源] 按鈕以啟動探索體驗。

![[探索資源] 窗格](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

如果組織中的其他資源或目錄系統管理員已使用 PIM 來管理 Azure 資源，或者您具有某個資源的合格角色指派，則清單檢視會顯示以下訊息：**探索資源或啟用合格的角色指派以繼續**。 

![[Privileged Identity Manager - Azure 資源] 窗格中的 [探索資源] 按鈕](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

當您選取 [探索資源] 按鈕時，不論是從上層功能表或在窗格中間，都會出現您可以管理的訂用帳戶清單。 反白顯示的訂用帳戶已受到 PIM 保護。

> [!NOTE]
> 為了防止其他資源系統管理員移除 PIM 設定，一旦將訂用帳戶設定為受控之後，就無法將其變成非受控。

![[Azure 資源 - 探索] 窗格](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

在 [資源] 欄中，將滑鼠放在您想要使用 PIM 保護的訂用帳戶上方。 然後，選取資源名稱左邊的核取方塊。 您可以同時選取多個訂用帳戶。

![[Azure 資源 - 探索] 窗格中的資源清單](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

若要起始上線程序，請在上層功能表中選取 [管理資源]。

![[Azure 資源 - 探索] 窗格中的 [管理資源] 按鈕](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

選取的資源現在由 PIM 管理。 若要關閉探索畫面，請選取右上角的 **X**。若要開始管理 PIM 設定並指派成員，在 [Privileged Identity Management - Azure 資源] 窗格頂端的功能表中，選取 [重新整理] 按鈕。

![[Privileged Identity Management - Azure 資源] 窗格頂端功能表中的 [重新整理] 按鈕](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>後續步驟

- [配置角色設定](pim-resource-roles-configure-role-settings.md)
- [在 PIM 中指派角色](pim-resource-roles-assign-roles.md)
