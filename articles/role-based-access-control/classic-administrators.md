---
title: Azure 傳統訂用帳戶系統管理員 | Microsoft Docs
description: 說明如何新增或變更 Azure 共同管理員與服務管理員，以及如何檢視帳戶管理員。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f5049a5a829ce8e94b62409c9d3b1439e8431fb1
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56101294"
---
# <a name="azure-classic-subscription-administrators"></a>Azure 傳統訂用帳戶管理員

Microsoft 建議您使用角色型存取控制 (RBAC) 管理資源存取權。 如果您仍在使用傳統部署模型，則必須使用傳統訂用帳戶系統管理員角色：服務管理員及共同管理員。 如需詳細資訊，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/resource-manager-deployment-model.md)。

本文說明如何新增或變更共同管理員與服務管理員，以及如何檢視帳戶管理員。

## <a name="add-a-co-administrator"></a>新增共同管理員

> [!TIP]
> 如果使用者需要管理 Azure 傳統部署，請新增共同管理員即可。 建議您所有其他用途都使用 RBAC。

1. 以服務管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

    共同管理員只能在訂用帳戶範圍內指派。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [傳統管理員] 索引標籤。

    ![開啟傳統系統管理員的螢幕擷取畫面](./media/classic-administrators/classic-administrators.png)

1. 按一下 [新增]  >  [新增共同管理員] 以開啟 [新增共同管理員] 窗格。

    如果 [新增共同管理員] 選項已停用，則表示您沒有權限。

1. 選擇要新增的使用者，然後按一下 [新增]。

    ![新增共同管理員的螢幕擷取畫面](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>將來賓使用者新增為共同管理員

獲指派「共同管理員」角色的[來賓使用者](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md)可能會發現與具備「共同管理員」角色的成員使用者相比有些差異。 請考慮下列狀況：

- 具有 Azure AD 公司或學校帳戶的使用者 A 是 Azure 訂用帳戶的服務管理員。
- 使用者 B 具有 Microsoft 帳戶。
- 使用者 A 將「共同管理員」角色指派給使用者 B。
- 使用者 B 可以執行幾乎所有操作，但無法在 Azure AD 目錄中註冊應用程式或查閱使用者。

您會預期使用者可以管理所有項目。 此差異的原因在於 Microsoft 帳戶是以來賓使用者而不是成員使用者身分新增至訂用帳戶。 在 Azure AD 中，來賓使用者與成員使用者具有不同的預設權限。 例如，成員使用者能夠在 Azure AD 中讀取其他使用者，來賓使用者不能。 成員使用者能夠在 Azure AD 中註冊新的服務主體，來賓使用者不能。 如果來賓使用者需要能夠執行這些工作，有一個可能的解決方案，就是指派來賓使用者所需的特定 Azure AD 系統管理員角色。 例如，在先前的案例中，您可以指派[目錄讀者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)角色以讀取其他使用者，以及指派[應用程式開發人員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer)角色，以便能夠建立服務主體。 如需有關成員使用者和來賓使用者及其權限的詳細資訊，請參閱 [Azure Active Directory 中的預設使用者權限是什麼？](../active-directory/fundamentals/users-default-permissions.md)

請注意，[適用於 Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)與 [Azure AD 系統管理員角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md)不同。 內建的角色不會授與任何 Azure AD 存取權。 如需詳細資訊，請參閱[了解各種不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="remove-a-co-administrator"></a>移除共同管理員

1. 以服務管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [傳統管理員] 索引標籤。

1. 在要移除的共同管理員旁邊新增核取記號。

1. 按一下 [移除] 。

1. 在出現的訊息方塊中，按一下 [確定]。

    ![移除共同管理員的螢幕擷取畫面](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>變更服務管理員

只有帳戶管理員可以變更訂用帳戶的服務管理員。 根據預設，註冊 Azure 訂用帳戶時系統會將服務管理員與帳戶管理員設為同一人。 具有帳戶管理員角色的使用者無法存取 Azure 入口網站。 具有服務管理員角色的使用者可完整存取 Azure 入口網站。 如果帳戶管理員和服務管理員是相同的使用者，並且您將服務管理員變更為不同的使用者，則帳戶管理員將失去 Azure 入口網站的存取權限。 不過，帳戶管理員可隨時使用帳戶中心，將自己變更回服務管理員。

有兩種方式可以變更服務管理員。 您可以在 **Azure 入口網站**或是**帳戶中心**中進行變更。

### <a name="azure-portal"></a>Azure 入口網站

1. 請參閱[變更服務管理員的限制](#limits)，確定您的情況是否受支援。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

1. 按一下 [內容] 。

    ![顯示帳戶系統管理員的螢幕擷取畫面](./media/classic-administrators/account-admin.png)

1. 在頂端，按一下 [服務管理] 以開啟 [服務管理員] 窗格。

    如果 [服務管理] 按鈕已停用，表示您沒有權限。 只有帳戶管理員的使用者可以變更訂用帳戶的服務管理員。

1. 選取新的服務管理員，然後按一下 [儲存]。

### <a name="account-center"></a>帳戶中心

1. 請參閱[變更服務管理員的限制](#limits)，確定您的情況是否受支援。

1. 以帳戶管理員身分登入[帳戶中心](https://account.windowsazure.com/subscriptions)。

1. 按一下訂用帳戶。

1. 按一下右側的 [編輯訂用帳戶詳細資料] 。

    ![顯示帳戶中心裡 [編輯訂用帳戶] 按鈕的螢幕擷取畫面](./media/classic-administrators/editsub.png)

1. 在 [服務管理員  ] 方塊中，輸入新的服務管理員的電子郵件地址。

    ![顯示應變更服務管理員電子郵件之方塊的螢幕擷取畫面](./media/classic-administrators/change-service-admin.png)

1. 按一下核取記號以儲存變更。

### <a name="limitations-for-changing-the-service-administrator"></a>變更服務管理員的限制

每個訂用帳戶都與 Azure AD 目錄相關聯。 若要尋找與訂用帳戶相關聯的目錄，請在 Azure 入口網站中開啟 [訂用帳戶]，然後選取訂用帳戶以查看目錄。

如果您以公司或學校帳戶登入，可以將組織中的其他帳戶新增為服務管理員。 例如，abby@contoso.com 可以將 bob@contoso.com 新增為服務管理員，但若想新增 john@notcontoso.com 為服務管理員，則 contoso.com 目錄中必須要有 john@notcontoso.com 才能這麼做。 以公司或學校帳戶登入的使用者可以繼續將 Microsoft 帳戶使用者新增為服務管理員。

  | 登入方法 | 要將 Microsoft 帳戶使用者新增為服務管理員嗎？ | 要將同一組織中的公司或學校帳戶新增為服務管理員嗎？ | 要將不同組織中的公司或學校帳戶新增為服務管理員嗎？ |
  | --- | --- | --- | --- |
  |  Microsoft 帳戶 |yes |否 |否 |
  |  公司帳戶或學校帳戶 |yes |是 |否 |

## <a name="view-the-account-administrator"></a>檢視帳戶管理員

帳戶管理員就是一開始註冊 Azure 訂用帳戶的使用者，也是負責訂用帳戶的計費擁有者。 若要變更訂用帳戶的帳戶管理員，請參閱[將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../billing/billing-subscription-transfer.md)。

請遵循這些步驟以檢視帳戶管理員。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，並選取訂用帳戶。

1. 按一下 [內容] 。

    該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員] 方塊中。

    ![顯示帳戶系統管理員的螢幕擷取畫面](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>後續步驟

* [了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)
