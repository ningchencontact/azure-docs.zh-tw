---
title: Privileged Identity Management 中適用於 Azure 資源角色的核准工作流程 | Microsoft Docs
description: 說明適用於 Azure 資源的核准工作流程程序。
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
ms.openlocfilehash: de15a02e706ec7f7b4cff0af303ea30fc87b8f34
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233762"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management 中適用於 Azure 資源角色的核准工作流程

系統管理員可以使用 Privileged Identity Management (PIM) 中適用於 Azure 資源角色的核准工作流程，進一步保護或限制對重要資源的存取。 也就是說，系統管理員需要核准，才能啟用角色指派。 

資源階層的概念是 Azure 資源角色獨有的。 此階層能夠繼承從父資源物件向下到父容器內所有子資源的角色指派。 

例如：資源系統管理員 Bob 使用 PIM，將 Alice 指派為 Contoso 訂用帳戶中擁有者角色的合格成員。 透過此指派，Alice 就是 Contoso 訂用帳戶內所有資源群組容器的合格擁有者。 Alice 也是訂用帳戶每個資源群組內所有資源 (例如虛擬機器) 的合格擁有者。 

讓我們假設 Contoso 訂用帳戶中有三個資源群組：Fabrikam Test、Fabrikam Dev 和 Fabrikam Prod。每個資源群組都包含單一虛擬機器。

資源的每個角色都會設定 PIM 設定。 與指派不同的是，這些設定不會繼承且僅適用於資源角色。 [深入了解合格指派和資源可見度](pim-resource-roles-eligible-visibility.md)。

繼續上述範例：Bob 使用 PIM，要求 Contoso 訂用帳戶擁有者角色的所有成員都需要核准才能啟用。 為了協助保護 Fabrikam Prod 資源群組中的資源，Bob 也會要求此資源的擁有者角色成員必須取得核准。 Fabrikam Test 和 Fabrikam Dev 中的擁有者角色不需要取得核准就能啟用。

當 Alice 要求啟用其 Contoso 訂用帳戶擁有者角色時，核准者必須先核准或拒絕她的要求，之後她才能成為有效角色。 如果 Alice 決定[將其啟用範圍侷限](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices)在 Fabrikam Prod 資源群組，核准者也必須核准或拒絕此要求。 不過，如果 Alice 決定將其啟用範圍侷限在Fabrikam Test 和/或 Fabrikam Dev，則不需要核准。

角色成員不一定都需要進行核准工作流程。 假設您的組織雇用數名約聘夥伴，協助開發將在 Azure 訂用帳戶中執行的應用程式。 身為資源系統管理員，您希望員工不需核准就具有合格的存取權，但約聘夥伴必須要求核准。 若要只針對約聘夥伴設定核准工作流程，您可以建立自訂角色，再賦予其與員工所獲派角色相同的權限。 您需要核准，才可啟用該自訂角色。 [深入了解自訂角色](pim-resource-roles-custom-role-policy.md)。

若要設定核准工作流程並指定可以核准或拒絕要求的人員，請使用下列程序。

## <a name="require-approval-to-activate"></a>需要核准才可啟用

1. 瀏覽到 Azure 入口網站中的 PIM，然後從清單中選取資源。

   ![含有所選取資源的 [Azure 資源] 窗格](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. 從左窗格中，選取 [角色設定]。

3. 搜尋並選取角色，然後按一下 [編輯] 以修改設定。

   ![適用於操作員角色的 [編輯] 按鈕](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. 在 [啟用] 區段中，選取 [需要核准才可啟用] 核取方塊。

   ![適用於角色設定的 [啟用] 區段](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>指定核准者

按一下 [選取核准者] 以開啟 [請選取使用者或群組] 窗格。

>[!NOTE]
>您必須至少選取一個要更新設定的使用者或群組。 沒有任何預設核准者。

資源管理員可以在核准者清單中新增任何使用者和群組的組合。 

![已選取使用者的 [請選取使用者或群組] 窗格](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>要求啟用核准

要求核准不會影響成員必須遵循的啟用程序。 [檢閱角色啟用步驟](pim-resource-roles-activate-your-roles.md)。

如果成員曾要求啟用需要核准的角色，但已不再需要該角色，該成員可取消其在 PIM 中的要求。

若要取消，瀏覽至 PIM，然後選取 [我的要求]。 找到該要求，然後選取 [取消]。

![[我的要求] 窗格](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>核准或拒絕要求

若要核准或拒絕要求，您必須是核准者清單的成員。 

1. 在 PIM 中，從左側功能表的索引標籤選取 [核准要求]，並找到該要求。

   ![[核准要求] 窗格](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. 選取要求、提供做出此決定的理由，然後選取 [核准] 或 [拒絕]。 接著就會解決該要求。

   ![含有詳細資訊的已選取要求](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>工作流程通知

以下是一些關於工作流程通知的事項：

- 當有角色要求正在等待他們檢閱時，核准者清單中的所有成員都會收到電子郵件通知。 電子郵件通知包含可供核准者核准或拒絕要求的直接連結。
- 清單內第一個核准或拒絕的成員會解決要求。 
- 當核准者回應要求時，核准者清單的所有成員都會得知該動作。 
- 已核准的成員變成有效角色時，資源管理員會收到通知。 

>[!Note]
>當資源系統管理員認為獲得核准的成員不應該成為有效角色時，可在 PIM 中移除作用中角色指派。 雖然資源系統管理員必須是核准者清單的成員才會收到待核准要求的通知，但他們可以透過檢視 PIM 中的待核准要求，來檢視和取消所有使用者的待核准要求。 

## <a name="next-steps"></a>後續步驟

[將 PIM 設定套用至唯一的使用者群組](pim-resource-roles-custom-role-policy.md)
