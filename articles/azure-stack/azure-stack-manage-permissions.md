---
title: 在 Azure Stack 中針對每位使用者管理資源的權限 (服務系統管理員與租用戶) | Microsoft Docs
description: 您身為服務系統管理員或租用戶，應了解如何管理 RBAC 權限。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 26ce7139b856fc2f8d7d2cad549b3dd3c0f5e406
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304678"
---
# <a name="manage-role-based-access-control"></a>管理角色型存取控制

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

Azure Stack 中的使用者可以是訂用帳戶、資源群組或服務中每個執行個體的讀者、擁有者或參與者。 例如，使用者 A 對於訂用帳戶 1 可能擁有讀者權限，但對於虛擬機器 7 則具備擁有者權限。

 - 讀取器：使用者可以檢視所有事項，但無法進行任何變更。
 - 參與者：使用者可以管理一切，但無法管理對資源的存取。
 - 擁有者：使用者可以管理一切，包括對於資源的存取。

## <a name="set-access-permissions-for-a-user"></a>設定使用者的存取權限

1. 使用具備擁有者權限的帳戶登入您想要管理的資源。
2. 在 [資源] 刀鋒視窗中，按一下 [存取] 圖示 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在 [使用者] 刀鋒視窗中，按一下 [角色]。
4. 在 [角色] 刀鋒視窗中，按一下 [新增] 即可加入使用者的權限。

## <a name="set-access-permissions-for-a-universal-group"></a>設定通用群組的存取權限 

> [!Note]  
僅適用於 Active Directory 同盟服務 (AD FS)。

1. 使用具備擁有者權限的帳戶登入您想要管理的資源。
2. 在 [資源] 刀鋒視窗中，按一下 [存取] 圖示 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在 [使用者] 刀鋒視窗中，按一下 [角色]。
4. 在 [角色] 刀鋒視窗中，按一下 [新增] 為通用群組「Active Directory 群組」新增權限。

## <a name="next-steps"></a>後續步驟
[新增 Azure Stack 租用戶](azure-stack-add-new-user-aad.md)

