---
title: 使用資源的儀表板執行 PIM-Azure Active Directory 中的存取權檢閱 |Microsoft Docs
description: 說明如何使用資源儀表板在 Azure AD Privileged Identity Management (PIM) 中執行存取權檢閱。
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
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5404d1b85821458aedef64b72ae635ea49aa1ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602500"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>使用資源的儀表板在 PIM 中執行存取權檢閱

若要在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 執行存取權檢閱，您可以使用資源的儀表板。 「系統管理員檢視」儀表板有三個主要元件：

- 展示資源角色啟用的圖表。
- 依指派類型顯示角色指派分佈的兩個圖表。
- 關於新增角色指派的資料區域。

![顯示圖表的系統管理員檢視儀表板螢幕擷取畫面](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![顯示資料清單的系統管理員檢視儀表板螢幕擷取畫面](media/pim-resource-roles-overview-dashboards/role-settings.png)

展示過去七天資源角色啟用的圖表。 此資料範圍限制在所選資源，並會顯示最常見角色的啟用 (擁有者、參與者和使用者存取系統管理員)，也會將所有角色的啟用結合顯示。

啟用圖形的右邊為兩個圖表，顯示依指派類型分類的角色指派分佈，類型為使用者和群組。 選取圖表的一部分，即可將值更改為特定百分比 (反之亦然)。

圖表下方會顯示過去 30 天中獲指派新角色的使用者和群組數，以及以總計指派數排序的角色清單 (順序為遞減)。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中起始 Azure 資源角色的存取權檢閱](pim-resource-roles-start-access-review.md) 
