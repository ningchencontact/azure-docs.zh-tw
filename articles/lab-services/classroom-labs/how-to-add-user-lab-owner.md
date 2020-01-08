---
title: 如何將使用者新增為 Azure 實驗室服務中的實驗室擁有者
description: 本文會說明系統管理員如何將使用者新增為實驗室的擁有者。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480849"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>如何將使用者新增為 Azure 實驗室服務中的教室實驗室擁有者
本文說明如何在 Azure 實驗室服務中將使用者新增為實驗室的擁有者。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>將使用者新增至實驗室帳戶的 [讀者] 角色
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 搜尋 [**實驗室服務**]，然後選取它。
3. 從清單中選取您的**實驗室帳戶**。 
2. 在 [**實驗室帳戶] 頁面**上，選取左側功能表上的 **[存取控制（IAM）** ]。 
2. 在 [**存取控制（IAM）** ] 頁面上，選取工具列上的 [**新增**]，然後選取 [**新增角色指派**]。

    ![實驗室帳戶的角色指派 ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. 在 [**新增角色指派**] 頁面上，執行下列步驟： 
    1. 選取**角色**的 [**讀取**者]。 
    2. 選取使用者。 
    3. 選取 [儲存]。 

        ![將使用者新增至實驗室帳戶的 [讀者] 角色 ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>將使用者新增至實驗室的擁有者角色

1. 回到 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**所有實驗室**]。
2. 選取您想要將使用者新增為擁有者的**實驗室**。 
    
    ![選取實驗室 ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. 在 [**實驗室**] 頁面上，選取左側功能表上的 **[存取控制（IAM）** ]。
4. 在 [**存取控制（IAM）** ] 頁面上，選取工具列上的 [**新增**]，然後選取 [**新增角色指派**]。
5. 在 [**新增角色指派**] 頁面上，執行下列步驟： 
    1. 選取**角色**的 [**擁有**者]。 
    2. 選取使用者。 
    3. 選取 [儲存]。 

## <a name="next-steps"></a>後續步驟
確認使用者在登入[實驗室服務入口網站](https://labs.azure.com)時看到實驗室。