---
title: 如何使用 Azure 入口網站來管理使用者指派的受控識別 (MSI)
description: 如何建立、列出和刪除使用者指派的受控識別逐步說明。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2018
ms.locfileid: "42145533"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>使用 Azure 入口網站建立、列出和刪除使用者指派的身分識別

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

在 Azure Active Directory 中，「受控服務識別」會提供受控身分識別給 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您會了解如何使用 Azure P入口網站建立、列出和刪除使用者指派的身分識別。

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本文中的管理作業，您的帳戶需要下列角色指派：
    - [受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可建立、讀取 (列出)、更新和刪除使用者指派的身份識別。
    - [受控識別操作員](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可讀取 (列出) 使用者指派身分識別的屬性。

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控身分識別

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以建立使用者指派的受控識別。
2. 在 [搜尋] 方塊中，輸入*受控識別*，然後在 [服務] 下方按一下 [受控識別]。
3. 按一下 [新增]，並在 [建立使用者指派的受控識別] 窗格底下的下列欄位中輸入值：
   - **資源名稱**： 這是使用者指派的受控識別名稱，例如 UAI1。
   - **訂用帳戶**： 選擇訂用帳戶，以在其下方建立使用者指派的受控識別
   - **資源群組**： 建立新資源群組來包含使用者指派的身分識別，或選擇 [使用現有項目] 在現有的資源群組中建立使用者指派的受控識別。
   - **位置**：選擇位置來部署使用者指派的受控識別，例如**美國西部**。
4. 按一下頁面底部的 [新增] 。

![建立使用者指派的受控身分識別](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>列出使用者指派的身分識別

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以列出使用者指派的受控識別。
2. 在 [搜尋] 方塊中，輸入「受控識別」，然後在 [服務] 下方按一下 [受控識別]。
3. 此時會針對訂用帳戶傳回使用者指派的受控識別清單。  若要查看使用者指派的詳細資料，可以按一下其名稱。

![列出使用者指派的受控識別](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>刪除使用者指派的身分識別

1. 使用與 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com)，以刪除使用者指派的受控識別。
2. 選取的使用者指派的身分識別，然後按一下 [刪除]。
3. 在確認方塊中選擇 [是]。

![刪除使用者指派的受控識別](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)