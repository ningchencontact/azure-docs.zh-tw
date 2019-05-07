---
title: 檢閱在 PIM-Azure Active Directory 中的 Azure 資源角色的存取權 |Microsoft Docs
description: 了解如何檢閱存取權的 Azure AD Privileged Identity Management (PIM) 中的 Azure 資源角色。
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
ms.openlocfilehash: 4e49f0077444f86eabeed22cc7cecf5e0ac7f8b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141193"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>檢閱在 PIM 中的 Azure 資源角色的存取權
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 簡化了企業管理特殊權限的存取，在 Azure 中的資源。 

如果您已被指派系統管理角色，貴組織的特殊權限角色管理員可能會要求您定期確認您仍需要該角色來執行作業。 您可能會收到包含連結的電子郵件，或請直接移至 [Azure 入口網站](https://portal.azure.com)。 請遵循本文中的步驟，執行獲指派角色的自我檢閱。

如果您是對存取權檢閱感興趣的特殊權限角色管理員，請在 [如何開始存取權檢閱](pim-resource-roles-start-access-review.md)中取得更多詳細資訊。

## <a name="add-the-privileged-identity-management-application"></a>加入 Privileged Identity Management 應用程式
您可以在 [Azure 入口網站](https://portal.azure.com/) 中使用 Azure Active Directory (Azure AD) PIM 應用程式來執行檢閱。 如果您的入口網站中沒有應用程式，請遵循這些步驟來開始作業。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 Azure 入口網站右上角的使用者名稱，然後選取您要操作的目錄。
3. 選取 [所有服務] 並使用 [篩選器] 方塊來搜尋 [Azure AD Privileged Identity Management]。
4. 核取 [釘選到儀表板]，然後選取 [建立]。 PIM 應用程式會隨即開啟。

## <a name="approve-or-deny-access"></a>核准或拒絕存取
當您核准或拒絕存取權時，只是在告訴檢閱者您是否仍然使用此角色。 如果您想要繼續擔任此角色，請選擇 [核准]，如果您不再需要此存取權，則請選擇 [拒絕]。 只有當檢閱者套用結果時，您的狀態才會變更。

請依照下列步驟來尋找並完成存取權檢閱︰
1. 瀏覽至 Azure AD PIM 應用程式。
2. 選取 [檢閱存取權] 刀鋒視窗。

   ![PIM 應用程式的螢幕擷取畫面，其中已選取檢閱存取權刀鋒視窗](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. 選取您想要完成的檢閱。 
4. 選擇 [核准] 或 [拒絕]。 您可能需要在 [提供原因]  方塊中包含您的決定原因。

   ![檢閱詳細資料頁面的螢幕擷取畫面](media/azure-pim-resource-rbac/rbac-access-review-choice.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中執行我的 Azure AD 角色的存取權檢閱](pim-how-to-perform-security-review.md)
