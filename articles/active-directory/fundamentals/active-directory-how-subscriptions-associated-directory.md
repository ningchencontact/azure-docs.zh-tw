---
title: 將現有的 Azure 訂用帳戶新增至您的租用戶 - Azure Active Directory | Microsoft Docs
description: 以下相關指示說明如何將現有的 Azure 訂用帳戶新增至您的 Azure Active Directory 租用戶。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a64bad11f5b83ddd7f6d7236ffed4ff4a6e39c2c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561865"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租用戶

Azure 訂用帳戶與 Azure Active Directory (Azure AD) 有信任關係, 這表示訂用帳戶信任 Azure AD 來驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的 Azure AD 目錄，但每個訂用帳戶都只能信任單一目錄。

若您的訂用帳戶已過期，您將無法再存取與訂用帳戶關聯的所有其他資源。 不過，Azure AD Directory 依然存在於 Azure 中，可讓您使用不同的 Azure 訂用帳戶來關聯及管理目錄。

您的所有使用者都有單一的*主*目錄可供驗證。 不過，您的使用者也可以是其他目錄中的來賓。 您可以同時為 Azure AD 中的每個使用者使用主目錄與來賓目錄。

> [!Important]
> 當您將訂用帳戶與不同的目錄建立關聯時, 具有使用[角色型存取控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md)指派之角色的使用者將會失去其存取權。 傳統訂用帳戶管理員 (服務管理員和共同管理員) 也會失去存取權。
> 
> 此外, 將您的 Azure Kubernetes Service (AKS) 叢集移至不同的訂用帳戶, 或將叢集擁有的訂用帳戶移至新的租使用者, 會導致叢集因為遺失角色指派和服務主體許可權而失去功能。 如需 AKS 的詳細資訊, 請參閱[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/)。

## <a name="before-you-begin"></a>開始之前

您必須先執行下列工作，才能關聯或新增您的訂用帳戶：

1. 請參閱下列變更清單, 以及您可能會受到的影響:

    - 已使用 RBAC 指派角色的使用者將會失去其存取權
    - 服務管理員和共同管理員將會失去存取權
    - 如果您有任何金鑰保存庫, 它們將無法存取, 而且您必須在關聯之後加以修正
    - 如果您有任何資源的受控識別, 例如虛擬機器或 Logic Apps, 您必須在關聯之後重新啟用或重新建立它們
    - 如果您有已註冊的 Azure Stack, 您必須在關聯之後重新註冊它

1. 使用符合下列條件的帳戶登入：
    - 具有訂用帳戶的[擁有](../../role-based-access-control/built-in-roles.md#owner)者角色指派。 如需如何指派擁有者角色的詳細資訊, 請參閱[使用 RBAC 和 Azure 入口網站來管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-portal.md)。
    - 同時存在於與訂用帳戶關聯的目前目錄中，以及此後您要關聯訂用帳戶的新目錄中。 如需有關如何取得其他目錄存取權的詳細資訊，請參閱 [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者](../b2b/add-users-administrator.md)。

1. 確定您不是使用 Azure 雲端服務提供者 (CSP) 訂用帳戶 (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 內部訂用帳戶 (MS-AZR-0015P) 或 Microsoft Imagine 訂用帳戶 (MS-AZR-0144P)。
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>將現有的訂用帳戶關聯至您的 Azure AD 目錄

1. 在 [Azure 入口網站的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)登入並選取您要使用的訂用帳戶。

2. 按一下 [變更目錄]。

    ![已反白顯示 [變更目錄] 選項的 [訂用帳戶] 頁面](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 檢閱出現的任何警告，然後選取 [變更]。

    ![顯示要變更之目錄的 [變更目錄] 頁面](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    訂用帳戶的目錄會變更， 而且您會看到成功訊息。

    ![目錄變更的相關成功訊息](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. 使用**目錄切換**器來移至您的新目錄。 可能需要幾個小時的時間, 才能正確顯示所有專案。 如果它的時間太長, 請務必檢查已移動之訂用帳戶的**全域訂**用帳戶篩選, 以確定它不只是隱藏的。

    ![目錄切換器頁面, 包含範例資訊](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

變更訂用帳戶目錄是服務層級作業，因此不會影響訂用帳戶帳單擁有權。 帳戶系統管理員仍然可以從[帳戶中心](https://account.azure.com/subscriptions)變更服務系統管理員。 若要刪除原始目錄，您必須將訂用帳戶訂單擁有權傳輸到新的帳戶系統管理員。若要深入了解如何移轉帳單擁有權，請參閱[將 Azure 訂用帳戶的擁有權轉移轉至另一個帳戶](../../billing/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>張貼關聯步驟
將訂用帳戶與不同的目錄建立關聯之後, 您可能必須執行其他步驟, 才能繼續作業。

1. 如果您有任何金鑰保存庫, 您必須變更金鑰保存庫租使用者識別碼。 如需詳細資訊, 請參閱在[訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](../../key-vault/key-vault-subscription-move-fix.md)。

2. 如果您使用系統指派的受控識別來取得資源, 您必須重新啟用這些識別。 如果您使用的是使用者指派的受控識別, 則必須重新建立這些身分識別。 重新啟用或重建受控識別之後, 您必須重新建立指派給這些身分識別的許可權。 如需詳細資訊, 請參閱[什麼是適用于 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。

3. 如果您已使用此訂用帳戶註冊 Azure Stack, 則必須重新註冊。 如需詳細資訊, 請參閱[向 Azure 註冊 Azure Stack](/azure-stack/operator/azure-stack-registration)。



## <a name="next-steps"></a>後續步驟

- 若要建立新的 Azure AD 租用戶，請參閱[存取 Azure Active Directory 以建立新租用戶](active-directory-access-create-new-tenant.md)

- 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- 若要深入了解如何在 Azure AD 中指派角色，請參閱[如何使用 Azure Active Directory 指派目錄角色給使用者](active-directory-users-assign-role-azure-portal.md)
