---
title: Privileged Identity Management - Azure 資源角色的核准工作流程 | Microsoft Docs
description: 說明如何核准 Azure 資源的工作流程程序。
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
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - 資源角色 - 核准

系統管理員可以使用 PIM 中的 Azure 資源角色核准工作流程，藉由要求取得核准後才能啟用角色指派，以進一步保護或限制重要資源的存取。 資源階層概念是 Azure 資源角色所獨有的特性。 此階層能夠繼承從父資源物件向下到父容器內所有從屬子資源的角色指派。 

例如，資源管理員 Bob 使用 PIM 將 Alice 指派為 Contoso 訂用帳戶中擁有者角色的合格成員。 透過這個指派，Alice 也會成為 Contoso 訂用帳戶內所有資源群組容器和訂用帳戶每個資源群組所包含所有資源 (例如虛擬機器) 的合格擁有者。 讓我們假設 Contoso 訂用帳戶中有三個資源群組，分別是 Fabrikam 測試、Fabrikam 開發和 Fabrikam 生產。每個資源群組都包含單一虛擬機器。

資源的每個角色都會設定 PIM 設定，而且與指派不同的是，這些設定不會繼承並僅適用於資源角色。 [深入了解合格指派和資源可見度。](pim-resource-roles-eligible-visibility.md)

使用上述範例，Bob 使用 PIM 要求 Contoso 訂用帳戶擁有者角色的所有成員必須要求核准才能啟用。 為了保護「Fabrikam 生產」資源群組中所包含的資源，Bob 要求此資源的擁有者角色成員也必須取得核准。 「Fabrikam 測試」和「Fabrikam 開發」的擁有者角色不需要取得核准就能啟用。

當 Alice 要求啟用其 Contoso 訂用帳戶擁有者角色時，在其成為有效角色之前，核准者必須核准或拒絕其要求。 此外，如果 Alice 決定[將其啟用範圍侷限](pim-resource-roles-activate-your-roles.md#just-enough-administration)在「Fabrikam 生產」資源群組，核准者也必須核准或拒絕此要求。 不過，如果 Alice 決定將其啟用範圍侷限在「Fabrikam 測試」和/或「Fabrikam 開發」，則不需要核准。

角色成員不一定都需要進行核准工作流程。 假設您的組織雇用數名約聘夥伴，協助開發將在 Azure 訂用帳戶中執行的應用程式。 您是資源管理員，而且希望員工不需核准就具有合格的存取權，但約聘夥伴必須要求核准。 若要只針對約聘夥伴設定核准工作流程，您可以建立自訂角色再賦予其與員工所獲派角色相同的權限，並要求要取得核准才能啟用該自訂角色。 [深入了解自訂角色](pim-resource-roles-custom-role-policy.md)。

請遵循下列步驟來設定核准工作流程，並指定可以核准或拒絕要求的人員。

## <a name="require-approval-to-activate"></a>需要核准才可啟用

巡覽至 Azure 入口網站中的 PIM，並從清單中選取資源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

從左側導覽功能表選取 [角色設定]。

搜尋並選取角色，然後按一下 [編輯] 修改設定。

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

在 [啟用] 區段中，核取 [需要核准才可啟用] 方塊。

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>指定核准者

按一下 [選取核准者] 以開啟選取畫面。

>[!NOTE]
>您必須至少選取一個要更新設定的使用者或群組。 沒有任何預設核准者。

資源管理員可以在核准者清單中新增任何使用者和群組的組合。 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>要求啟用核准

要求核准不會影響成員必須遵循的啟用程序。 [檢閱角色啟用步驟](pim-resource-roles-activate-your-roles.md)。

如果成員以前要求啟用需要核准的角色，但該角色現已不再需要用到，成員可取消其在 PIM 中的要求。

若要取消，請巡覽至 PIM 並選取 [我的要求]。 找到要求，並按一下 [取消]。

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>核准或拒絕要求

若要核准或拒絕要求，您必須是核准者清單的成員。 在 PIM 中，從左側導覽功能表的索引標籤選取 [核准要求]，並找到要求。

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

選取要求，提供做出此決定的理由，然後選取核准或拒絕，以解決要求。

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>工作流程通知

- 當有角色要求正在等待他們檢閱時，核准者清單中的所有成員都會收到電子郵件通知。 電子郵件通知內有可供管理員核准或拒絕要求的直接連結。
- 清單內第一個核准或拒絕的成員會解決要求。 
- 當核准者回應要求時，核准者清單的所有成員都會得知該動作。 
- 已核准的成員變成有效角色時，資源管理員會收到通知。 

>[!Note]
>當資源管理員認為獲得核准的成員不應該成為有效角色時，則可在 PIM 中移除已生效的角色指派。 雖然資源管理員必須是核准者清單的成員才會收到待核准要求的通知，但他們可以藉由檢視 PIM 中的待核准要求，來檢視和取消所有使用者的待核准要求。 

## <a name="next-steps"></a>後續步驟

[將 PIM 設定套用至唯一的使用者群組](pim-resource-roles-custom-role-policy.md)
