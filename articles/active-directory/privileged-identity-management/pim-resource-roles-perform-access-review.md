---
title: 如何在適用於 Azure 資源的 Privileged Identity Management 中執行存取權檢閱 | Microsoft Docs
description: 本文件說明如何在適用於 Azure 資源的 PIM 中執行存取權檢閱。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Privileged Identity Management - 資源角色 - 執行存取權檢閱
適用於 Azure 資源的 Privileged Identity Management 簡化了企業管理以特殊權限身分存取 Azure AD 中資源的方式。 

如果您已被指派系統管理角色，貴組織的特殊權限角色管理員可能會要求您定期確認您仍需要該角色來執行作業。 您可能會收到包含連結的電子郵件，或請直接移至 [Azure 入口網站](https://portal.azure.com)。 請遵循本文中的步驟，執行獲指派角色的自我檢閱。

如果您是對存取權檢閱感興趣的特殊權限角色管理員，請在 [如何開始存取權檢閱](pim-resource-roles-start-access-review.md)中取得更多詳細資訊。

## <a name="add-the-privileged-identity-management-application"></a>加入 Privileged Identity Management 應用程式
您可以使用 [Azure 入口網站](https://portal.azure.com/) 中的 Azure AD Privileged Identity Management (PIM) 應用程式來執行檢閱。  如果入口網站中沒有 Azure AD Privileged Identity Management 應用程式，請遵循這些步驟操作。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 Azure 入口網站右上角的使用者名稱，然後選取您要操作的目錄。
3. 選取 [所有服務] 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]。
4. 選取 [釘選到儀表板]，然後按一下 [建立]。 Privileged Identity Management 應用程式隨即開啟。

## <a name="approve-or-deny-access"></a>核准或拒絕存取
當您核准或拒絕存取權時，只是在告訴檢閱者您是否仍然使用此角色。 如果您想要繼續擔任此角色，請選擇 [核准]，如果您不再需要此存取權，則請選擇 [拒絕]。 您的狀態不會立即變更，必須等到檢閱者套用結果之後才會變更。
請依照下列步驟來尋找並完成存取權檢閱︰
1. 巡覽至 Azure AD Privileged Identity Management 應用程式。
2. 選取 [檢閱存取權] 刀鋒視窗。

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. 選取您想要完成的檢閱。 
4. 選擇 [核准] 或 [拒絕]。 您可能需要在 [提供原因]  文字方塊中提供您的決定原因。

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
