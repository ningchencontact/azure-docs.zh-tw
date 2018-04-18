---
title: 適用於 Azure 資源的 Privileged Identity Management - 探索和管理 Azure 資源 | Microsoft Docs
description: 說明如何保護 Azure 資源。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>探索和管理 Azure 資源

如果貴組織已經使用 Azure AD PIM 來保護您目錄中的管理員，或者您是想要保護所在生產資源的訂用帳戶擁有者。

當您第一次啟用適用於 Azure 資源的 PIM 時，您必須探索並選取要使用 PIM 保護的資源。 使用 PIM 可以管理的資源數目沒有限制，但是建議您先從最重要的 (生產) 資源著手。

> [!Note]
> 您只能搜尋並選取訂用帳戶資源進行管理。 選擇在 PIM 中管理訂用帳戶，也將啟用所有子資源的管理。

## <a name="discover-resources"></a>探索資源

巡覽至 Azure AD PIM，然後在左導覽功能表的 [管理] 區段中選取 Azure 資源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

如果這是您第一次使用適用於 Azure 資源的 PIM，您必須執行探索來尋找要管理的資源。
按一下畫面中央的 [探索資源] 按鈕，以啟動探索體驗。

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

如果貴組織中的其他資源或目錄管理員已使用 PIM 管理 Azure 資源，或者您具有某項資源的合格角色指派，則清單檢視會包含以下訊息：「探索資源或啟用合格角色指派以繼續執行」。 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

當您選取動作列中或 [探索資源] 畫面中央的按鈕時，您會看到可供管理的訂用帳戶清單。 此時，如果您看見醒目提示的訂用帳戶，則表示這些訂用帳戶受 PIM 保護。

> [!Note]
> 若要防止其他資源管理員移除 PIM 設定，訂用帳戶一旦受控，就無法變成非受控。

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

將滑鼠暫留在您想要使用 PIM 保護的訂用帳戶上，並選取資料列最左邊的方塊。 您可以同時選取多個訂用帳戶。

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

若要起始上線程序，請選取畫面頂端列中的 [管理資源] 按鈕。

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

選取的資源現在由 PIM 管理。 使用頁面右上角的 "X" 來關閉探索畫面，然後按一下 [管理 Azure 資源] 畫面頂端列中的 [重新整理]，即可開始管理 PIM 設定及指派成員。

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>後續步驟

[配置角色設定](pim-resource-roles-configure-role-settings.md)

[在 PIM 中指派角色](pim-resource-roles-assign-roles.md)
