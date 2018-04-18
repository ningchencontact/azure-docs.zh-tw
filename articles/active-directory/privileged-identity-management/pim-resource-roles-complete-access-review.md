---
title: 適用於 Azure 資源的 Privileged Identity Management - 完成 Azure 資源的存取權檢閱 | Microsoft Docs
description: 說明如何完成 Azure 資源的存取權檢閱。
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
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - 資源角色 - 完成存取權檢閱
在 [開始安全性檢閱](pim-resource-roles-start-access-review.md)之後，特殊權限角色管理員就可以檢閱特殊權限存取權。 適用於 Azure 資源的 Privileged Identity Management (PIM) 會自動傳送電子郵件，提示使用者檢閱其存取權。 如果使用者未收到電子郵件，您可以將 [Azure AD Privileged Identity Management：如何執行安全性檢閱](pim-resource-roles-perform-access-review.md)中的指示傳送給他們。

安全性檢閱時間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟管理檢閱並查看結果。

## <a name="manage-security-reviews"></a>管理安全性檢閱
1. 移至 [Azure 入口網站](https://portal.azure.com/)，然後在儀表板上選取 [Azure 資源] 應用程式。
2. 選取您的資源。
3. 選取儀表板的 [存取權檢閱]  區段。
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. 選取您想要管理的存取權檢閱。

在存取權檢閱的詳細資料刀鋒視窗上，有一些可管理該檢閱的選項。
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
所有的存取權檢閱都有結束日期，但是您可以使用 [停止]  按鈕來提早結束檢閱。 如果此時有任何使用者尚未受到檢閱，在您停止檢閱之後，他們將無法受到檢閱。 在停止檢閱之後，即無法重新開始該檢閱。

### <a name="reset"></a>重設
您可以重設存取權檢閱，以移除在其上所做的所有決策。 一旦重設存取權檢閱，所有使用者都會再次標示為「未檢閱」。 

### <a name="apply"></a>套用
在存取權檢閱因您已達到結束日期或手動停止它而完成之後，[套用]  按鈕就會實作該檢閱的結果。 如果使用者的存取權在檢閱中被拒絕，則在這個步驟就會將其角色指派移除。  

### <a name="delete"></a>刪除
如果對檢閱不再有任何興趣，請刪除它。 [刪除]  按鈕會將檢閱從 PIM 應用程式中移除。

## <a name="results"></a>結果
在 [結果] 索引標籤上檢視和下載檢閱結果清單。![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>檢閱者
檢視檢閱者並將其新增至現有的存取權檢閱。 提醒檢閱者完成其檢閱。
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



