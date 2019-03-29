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
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c080173af8ddd31b077bb820ea19d82eb2b29300
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576134"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>檢視稽核記錄的 Azure AD PIM 中的角色
若要查看所有特殊權限的角色在指定的期間內的所有使用者指派與啟用，您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 稽核歷程記錄。 如果您想要查看租用戶活動 (包括系統管理員、使用者和同步處理活動) 的完整稽核歷程記錄，您可以使用 [Azure Active Directory 存取和使用情況報告](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>瀏覽至稽核記錄
從 [Azure 入口網站](https://portal.azure.com)儀表板選取 [Azure AD Privileged Identity Management] 應用程式。 從該處，按一下 PIM 儀表板中的 [管理特殊權限角色] > [稽核記錄] 來存取稽核記錄。

![稽核歷程記錄](media/azure-ad-pim-approval-workflow/image021.png)

> [!NOTE]
> 您可以依 [動作] 排序資料，並尋找「已核准啟用」


## <a name="audit-history-graph"></a>稽核記錄圖表
您可以使用稽核記錄，在折線圖中檢視啟用總數、每天最大啟用數目，以及每天平均啟用數目。  如果稽核記錄中有一個以上的角色，您也可以依角色篩選資料。

使用 [時間]、[動作] 和 [角色] 按鈕來排序記錄。

## <a name="audit-history-list"></a>稽核記錄清單
稽核記錄清單中的欄如下：

* **要求者** - 要求啟用或變更角色的使用者。  如果值為「Azure System (Azure 系統)」，請檢查 Azure 稽核記錄以取得詳細資訊。
* **使用者** - 啟用角色或被指派給角色的使用者。
* **角色** - 使用者所指派或啟用的角色。
* **動作** - 要求者所採取的動作。 這可包括指派、解除指派、啟用或停用。
* **時間** - 動作發生的時間。
* **推理** - 如果在啟用過程中於 [原因] 欄位輸入任何文字，則它將會顯示於此處。
* **到期** - 只與角色啟用相關。

## <a name="filter-audit-history"></a>篩選稽核記錄
您可以按一下 [篩選]  按鈕，來篩選稽核記錄中顯示的資訊。  隨即會出現 [更新圖表參數]  刀鋒視窗。

設定篩選之後，請按一下 [更新]  來篩選記錄中的資料。  如果資料並未立即出現，請重新整理頁面。

### <a name="change-the-date-range"></a>變更日期範圍
使用 [今天]、[過去一週]、[過去一個月] 或 [自訂] 按鈕來變更稽核記錄的時間範圍。

當您選擇 [自訂] 按鈕時，將會看見可指定記錄日期範圍的 [開始] 日期欄位和 [結束] 日期欄位。  您可以使用 MM/DD/YYYY 格式輸入日期，或者按一下 [月曆]  圖示並從月曆中選擇日期。

### <a name="change-the-roles-included-in-the-history"></a>變更記錄中包含的角色
選取或取消選取每個角色旁邊的 [角色]  核取方塊，即可將其納入記錄或排除在記錄之外。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

- [在 PIM 中檢視 Azure 資源角色的活動和稽核記錄](azure-pim-resource-rbac.md)
