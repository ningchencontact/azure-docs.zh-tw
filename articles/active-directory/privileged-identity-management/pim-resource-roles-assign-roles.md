---
title: 使用 Privileged Identity Management 指派適用於 Azure 資源的角色 | Microsoft Docs
description: 說明如何在 PIM 中指派角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 246467d5eeebd43b8d89d98a30fdfc1c5ca0909a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233650"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 指派適用於 Azure 資源的角色

## <a name="assign-roles"></a>指派角色

若要在檢視 [角色] 窗格時將使用者或群組指派給角色，請選取角色，然後選取 [新增使用者]。 

![含有 [新增使用者] 按鈕的 [角色] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

您也可以從 [成員] 窗格選取 [新增使用者]。

![含有 [新增使用者] 按鈕的 [成員] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


如果從 [成員] 窗格新增使用者或群組，您必須： 

1. 先從 [選取角色] 窗格選擇角色，然後才能選取使用者或群組。

   ![[選取角色] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. 從目錄選擇使用者或群組。

3. 從下拉式功能表中選擇適當的指派類型： 

   - **Just In Time**：針對一段指定期間或無限期 (如果已在角色設定中設定)，為使用者或群組成員提供合資格但不持續的角色存取。 
   - **直接**：不需要使用者或群組成員即可啟用角色指派 (稱為持續存取)。 我們建議您使用直接指派供短期使用，當工作完成時就不需要存取。 範例包括待命班和時間緊迫的活動。

4. 如果指派應為永久性 (適用於 Just-In-Time 指派的永久合格，或者適用於直接指派的永久有效)，請選取 [指派類型] 下拉式清單下方的核取方塊。

   ![含有 [指派類型] 方塊及相關核取方塊的 [成員資格設定] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >如果其他管理員已在角色設定中指定每個指派類型的最長指派時間，則可能無法修改此核取方塊。

   若要指定特定指派的持續時間，請清除核取方塊，並修改開始和/或結束日期和時間方塊。

   ![含有開始日期、開始時間、結束日期和結束時間方塊的 [成員資格設定] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>管理角色指派

管理員可以藉由從左側窗格中選取 [角色] 或 [成員] 來管理角色指派。 選取 [角色] 可讓管理員將其管理工作的範圍限制為特定的角色。 選取 [成員] 會顯示資源的所有使用者和群組角色指派。

![[角色] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![[成員] 窗格](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
如果有擱置中角色啟用，當您檢視成員資格時，窗格頂端會顯示通知橫幅。


## <a name="modify-existing-assignments"></a>修改現有的指派

若要從使用者/群組詳細資料檢視中修改現有的指派，從動作列選取 [變更設定]。 將指派類型變更為 **Just In Time** 或**直接**。

![含有 [變更設定] 按鈕的 [使用者詳細資料] 窗格](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
