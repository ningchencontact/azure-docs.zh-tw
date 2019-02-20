---
title: 新增或變更 Azure 訂用帳戶管理員 | Microsoft Docs
description: 說明如何使用角色型存取控制 (RBAC) 來新增或變更 Azure 訂用帳戶管理員。
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: banders
ms.openlocfilehash: d3bdcc267c55434a71c915f7d4e575d47522986b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110569"
---
# <a name="add-or-change-azure-subscription-administrators"></a>新增或變更 Azure 訂用帳戶系統管理員

若要管理對 Azure 資源的存取，您必須具有適當的系統管理員角色。 Azure 有一個稱為角色型存取控制 (RBAC) 的授權系統，其中含有可供您選擇的內建角色。 您可以在不同範圍 (例如管理群組、訂用帳戶或資源群組) 指派這些角色。

Microsoft 建議您使用 RBAC 來管理對資源的存取。 不過，如果您仍在使用傳統部署模型，則必須使用傳統訂用帳戶管理員角色。 如需詳細資訊，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/resource-manager-deployment-model.md)和 [Azure 傳統訂用帳戶管理員](../role-based-access-control/classic-administrators.md)。

本文說明如何在訂用帳戶範圍使用 RBAC 來新增或變更使用者的系統管理員角色。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>指派使用者做為訂用帳戶的系統管理員

若要讓使用者成為 Azure 訂用帳戶的系統管理員，請在訂用帳戶範圍為其指派[擁有者](../role-based-access-control/built-in-roles.md#owner)角色 (RBAC 角色)。 「擁有者」角色可授與使用者訂用帳戶中所有資源的完整存取權，包括將存取權委派給其他人的權限。 針對任何其他角色指派，這些步驟都相同。

1. 在 Azure 入口網站中，開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

1. 選擇您想要授與存取權的訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [角色指派] 索引標籤以檢視此訂用帳戶的所有角色指派。

    ![顯示角色指派的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. 按一下 [新增] > [新增角色指派]，以開啟 [新增角色指派] 窗格。

    如果您沒有指派角色的權限，此選項就會被停用。

1. 在 [角色] 下拉式清單中，選取 [擁有者] 角色。

1. 在 [選取] 清單中，選取使用者。 如果在清單中未看到使用者，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱與電子郵件地址。

    ![顯示已選取 [擁有者] 角色的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. 按一下 [儲存] 以指派角色。

    在幾分鐘之後，即會在訂用帳戶範圍將「擁有者」角色指派給使用者。

## <a name="next-steps"></a>後續步驟

* [什麼是角色型存取控制 (RBAC)？](../role-based-access-control/overview.md)
* [了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [操作說明：將 Azure 訂用帳戶關聯或新增至 Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory 中的系統管理員角色權限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
