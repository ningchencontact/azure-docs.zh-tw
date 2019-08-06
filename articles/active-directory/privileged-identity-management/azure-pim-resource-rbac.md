---
title: PIM-Azure Active Directory 中的 Azure 資源角色的檢視活動和稽核記錄 |Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中檢視 Azure 資源角色的活動和稽核記錄。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476505"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>在 PIM 中檢視 Azure 資源角色的活動和稽核記錄

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，檢視組織內 Azure 資源角色的活動、啟用及稽核記錄。 適用範圍包括訂用帳戶、資源群組甚至是虛擬機器。 所有在 Azure 入口網站內利用 Azure 角色型存取控制 (RBAC) 功能的資源，都可以運用 PIM 中的安全性和生命週期管理功能。

## <a name="view-activity-and-activations"></a>檢視活動和啟用

若要查看各種資源中特定使用者採取了哪些動作，您可以檢視與指定之啟用期間相關聯的 Azure 資源活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要檢視其活動及啟用的資源。

1. 按一下 [角色] 或 [成員]。

1. 按一下某個使用者。

    您會依日期看到使用者在 Azure 資源中所採取動作的圖形化檢視。 它也會顯示同一段時間週期內最新的角色啟用。

    ![資源活動摘要 和 角色啟用的使用者詳細資料](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 按一下特定的角色啟用可查看詳細資料，以及該使用者作用中時所發生的對應 Azure 資源活動。

    ![選取的角色啟用及顯示依日期的活動詳細資料](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>匯出具有子系的角色指派

您的合規性需求可能會要求您必須提供完整的角色指派清單給稽核員。 PIM 可讓您查詢特定資源的角色指派，其中包含所有子資源的角色指派。 之前系統管理員很難取得訂用帳戶的角色指派完整清單，而且他們必須針對每個特定資源匯出角色指派。 您可以使用 PIM 查詢訂用帳戶中所有作用中和合格的角色指派，包括所有的資源群組和資源的角色指派。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要匯出其角色指派的資源，例如訂用帳戶。

1. 按一下 [成員]。

1. 按一下 [匯出] 來開啟 [匯出成員資格] 窗格。

    ![匯出至匯出的所有成員的成員資格 窗格](media/azure-pim-resource-rbac/export-membership.png)

1. 按一下 [匯出所有成員]，匯出 CSV 檔案中的所有角色指派。

    ![匯出為 Excel 中顯示的 CSV 檔案中的角色指派](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

資源稽核可讓您檢視資源的所有角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要檢視其稽核記錄的資源。

1. 按一下 [資源稽核]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![資源稽核篩選器清單](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 在 [稽核類型] 中，選取 [啟動 (已指派 + 已啟動)]。

    ![資源稽核清單來啟用稽核類型篩選](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在 [動作] 底下，按一下使用者的 [(活動)] 以查看該使用者在 Azure 資源中的活動詳細資料。

    ![在特定動作的使用者活動詳細資料](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要檢視其稽核記錄的資源。

1. 按一下 [我的稽核]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![目前使用者的 [稽核] 清單](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
- [在 PIM 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
- [檢視稽核記錄的 Azure AD PIM 中的角色](pim-how-to-use-audit-log.md)
