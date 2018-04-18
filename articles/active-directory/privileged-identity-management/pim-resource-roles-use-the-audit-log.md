---
title: 適用於 Azure 資源的 Privileged Identity Management - 資源稽核 | Microsoft Docs
description: 說明如何取得指定資源的所有角色活動檢視。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - 資源角色 - 稽核

資源稽核可讓您檢視資源的所有角色活動。 您可以使用預先定義的日期或自訂範圍來篩選資訊。
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

資源稽核也可讓使用者快速檢視使用者的活動詳細資料。 在 [稽核類型] 下選取 [啟動]。 按一下 [活動] 可在 Azure 資源上看到該使用者的動作。
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>我的稽核

「我的稽核」可讓您檢視使用者的個人角色活動。 您可以使用預先定義的日期或自訂範圍來篩選資訊。
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>檢視啟用與 Azure 資源活動

如果您需要查看各種資源的特定使用者所採取了什麼動作，您可以檢閱與特定啟用期間相關聯的 Azure 資源活動 (適用於符合資格的使用者)。 若要開始，請從 [成員] 檢視或從特定角色的成員成員中選取使用者。 結果以圖表形式顯示，包含使用者在 Azure 資源上的動作 (依日期排序)，以及相同時間內最近的角色啟用。

![](media/azure-pim-resource-rbac/rbac-user-details.png)

取選特定角色啟用會顯示角色啟用的詳細資料，以及使用者作用中時發生的相應的 Azure Resource 活動。

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

