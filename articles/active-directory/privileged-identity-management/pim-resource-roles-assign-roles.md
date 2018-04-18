---
title: 適用於 Azure 資源的 Privileged Identity Management - 指派角色 | Microsoft Docs
description: 說明如何在 PIM 中指派角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - 資源角色 - 指派

## <a name="assign-roles"></a>指派角色

若要將使用者或群組指派至角色，請選取角色 (若要檢視角色)， 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

或在動作列中按一下 [新增] (如在 [成員] 檢視上)。

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


如果從 [成員] 索引標籤新增使用者或群組，您必須： 

1. 先從 [新增] 功能表選擇角色，才可以選取使用者或群組。

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. 從目錄選擇使用者或群組。

3. 從下拉式功能表中選擇適用的作業類型。 

    - **及時指派：**為使用者或群組成員提供合資格但不持續的的角色存取，時間可在指定期間或無限期 (如在角色設定中設定)。 
    - **直接指派：**無須使用者或群組成員即可啟用角色指派 (稱為持續存取)。 Microsoft 建議在短期使用上使用直接指派 (例如待命移位或以時間區分的活動)，這些工作完成時皆不需要存取。

指派類型下拉式清單下方的核取方塊可讓您指定指派是否應為永久性 (永久合資格啟用及時指派/永久啟用直接指派)。

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>如果其他系統管理員角色已在角色設定中，指定每個指派類型的最長指派時間，則核取方塊可能無法修改。

 若要指定特定指派的持續時間，取消選取核取方塊並修改開始和/或結束日期和時間欄位。

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>管理角色指派

系統管理員管理角色指派，方法是從左側導覽選取 [角色] 或 [成員]。 選取 [角色] 可讓系統管理員將管理工作範圍限制至特定角色，而選取 [成員] 則會顯示該資源的所有使用者和群組角色指派。

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
如果您有擱置中的啟用角色，檢視成員資格時，系統會在頁面頂端顯示通知橫幅。


## <a name="modify-existing-assignments"></a>修改現有的指派

若要從使用者/群組詳細資料檢視修改現有的指派，請從頁面頂端的 [動作] 列選取 [變更設定]。 將指派類型變更「為時指派」或「直接指派」。

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
