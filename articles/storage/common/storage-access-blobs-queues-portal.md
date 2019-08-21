---
title: 使用 Azure 入口網站存取 blob 或佇列資料-Azure 儲存體
description: 當您使用 Azure 入口網站存取 blob 或佇列資料時, 入口網站會要求在幕後 Azure 儲存體。 這些 Azure 儲存體的要求可以使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰來進行驗證和授權。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abbd436e5d1c88c53af95fd8ba9add20fa67c8e4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640881"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>使用 Azure 入口網站來存取 blob 或佇列資料

當您使用[Azure 入口網站](https://portal.azure.com)存取 blob 或佇列資料時, 入口網站會要求在幕後 Azure 儲存體。 Azure 儲存體的要求可以使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰進行授權。 入口網站會指出您所使用的方法, 並可讓您在有適當許可權的情況下, 于兩者之間切換。  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>存取 blob 或佇列資料所需的許可權

根據您想要在 Azure 入口網站中授權存取 blob 或佇列資料的方式而定, 您將需要特定的許可權。 在大部分情況下, 會透過角色型存取控制 (RBAC) 來提供這些許可權。 如需 RBAC 的詳細資訊, 請參閱[什麼是角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)。

### <a name="account-access-key"></a>帳戶存取金鑰

若要使用帳戶存取金鑰來存取 blob 和佇列資料, 您必須擁有指派給您的 RBAC 角色, 其中包含 RBAC action **storageAccounts/listkeys/action**。 此 RBAC 角色可能是內建或自訂角色。 支援**Microsoft 儲存體/storageAccounts/listkeys/action**的內建角色包括:

- Azure Resource Manager[擁有](../../role-based-access-control/built-in-roles.md#owner)者角色
- Azure Resource Manager[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [儲存體帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

當您嘗試存取 Azure 入口網站中的 blob 或佇列資料時, 入口網站會先檢查您是否已獲指派具有**Microsoft storageAccounts/listkeys/action**的角色。 如果您已使用此動作指派角色, 則入口網站會使用帳戶金鑰來存取 blob 和佇列資料。 如果您尚未使用此動作指派角色, 則入口網站會嘗試使用您的 Azure AD 帳戶來存取資料。

> [!NOTE]
> 傳統訂用帳戶管理員角色服務管理員和共同管理員包含對等的 Azure Resource Manager[擁有](../../role-based-access-control/built-in-roles.md#owner)者角色。 「**擁有**者」角色包含所有動作, 包括**storageAccounts/listkeys/action**, 因此具有其中一個系統管理角色的使用者也可以使用帳戶金鑰來存取 blob 和佇列資料。 如需詳細資訊, 請參閱[傳統訂用帳戶管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="azure-ad-account"></a>Azure AD 帳戶

若要使用您的 Azure AD 帳戶從 Azure 入口網站存取 blob 或佇列資料, 下列兩個語句都必須為 true:

- 您已獲指派「Azure Resource Manager[讀取](../../role-based-access-control/built-in-roles.md#reader)者」角色, 其範圍至少限於儲存體帳戶層級或更高的等級。 「**讀取**者」角色會授與最受限制的許可權, 但也可接受另一個授與存取儲存體帳戶管理資源的 Azure Resource Manager 角色。
- 您已獲指派可存取 blob 或佇列資料的內建或自訂角色。

需要**讀取器**角色指派或另一個 Azure Resource Manager 角色指派, 才能讓使用者在 Azure 入口網站中查看和流覽儲存體帳戶管理資源。 授與 blob 或佇列資料存取權的 RBAC 角色並不會授與對儲存體帳戶管理資源的存取權。 若要在入口網站中存取 blob 或佇列資料, 使用者需要有許可權才能流覽儲存體帳戶資源。 如需這項需求的詳細資訊, 請參閱[指派入口網站存取的讀者角色](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支援 blob 或佇列資料存取的內建角色包括:

- [儲存體 Blob 資料擁有](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)者:適用于 Azure Data Lake Storage Gen2 的 POSIX 存取控制。
- [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):Blob 的讀取/寫入/刪除許可權。
- [儲存體 Blob 資料讀取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):Blob 的唯讀許可權。
- [儲存體佇列資料參與者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor):佇列的讀取/寫入/刪除許可權。
- [儲存體佇列資料讀取器](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader):佇列的唯讀許可權。
    
自訂角色可支援內建角色所提供之相同許可權的不同組合。 如需建立自訂 RBAC 角色的詳細資訊, 請參閱[適用于 azure 資源的自訂角色](../../role-based-access-control/custom-roles.md)和[瞭解 azure 資源的角色定義](../../role-based-access-control/role-definitions.md)。

> [!NOTE]
> 不支援列出具有傳統訂用帳戶管理員角色的佇列。 若要列出佇列, 使用者必須已指派給他們 Azure Resource Manager 的**讀者**角色、**儲存體佇列資料讀取**者角色或**儲存體佇列資料參與者**角色。

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>流覽至入口網站中的 blob 或佇列

若要在入口網站中查看 blob 或佇列資料, 請流覽至儲存體帳戶的 [**總覽**], 然後按一下 [ **blob** ] 或 [**佇列**] 的連結。 或者, 您可以流覽至功能表中的 [ **Blob 服務**] 和 [**佇列服務**] 區段。 

![流覽至 Azure 入口網站中的 blob 或佇列資料](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>判斷目前的驗證方法

當您流覽至容器或佇列時, Azure 入口網站會指出您目前使用的是帳戶存取金鑰, 還是您的 Azure AD 帳戶來進行驗證。

本節中的範例會示範如何存取容器和其 blob, 但入口網站會在您存取佇列和其訊息時顯示相同的訊息, 或列出佇列。

### <a name="account-access-key"></a>帳戶存取金鑰

如果您使用帳戶存取金鑰進行驗證, 您會在入口網站中看到指定為驗證方法的**存取金鑰**:

![目前正在使用帳戶金鑰存取容器資料](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

若要切換為使用 Azure AD 帳戶, 請按一下影像中反白顯示的連結。 如果您透過指派給您的 RBAC 角色擁有適當的許可權, 就能夠繼續進行。 不過, 如果您沒有正確的許可權, 您會看到類似下面的錯誤訊息:

![如果 Azure AD 帳戶不支援存取, 則會顯示錯誤](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

請注意, 如果您的 Azure AD 帳戶沒有許可權可以查看, 清單中就不會出現任何 blob。 按一下 [**切換至存取金鑰**] 連結, 以再次使用存取金鑰進行驗證。

### <a name="azure-ad-account"></a>Azure AD 帳戶

如果您使用 Azure AD 帳戶進行驗證, 您會在入口網站中看到**Azure AD 的使用者帳戶**指定為驗證方法:

![目前使用 Azure AD 帳戶存取容器資料](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

若要切換為使用帳戶存取金鑰, 請按一下影像中反白顯示的連結。 如果您有帳戶金鑰的存取權, 就可以繼續進行。 不過, 如果您沒有帳戶金鑰的存取權, 就會看到類似下列的錯誤訊息:

![如果您沒有帳戶金鑰的存取權, 就會顯示錯誤](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

請注意, 如果您沒有帳戶金鑰的存取權, 清單中就不會出現任何 blob。 按一下**切換以 Azure AD 使用者帳戶** 連結, 以再次使用您的 Azure AD 帳戶進行驗證。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Active Directory 來驗證 Azure blob 和佇列的存取權](storage-auth-aad.md)
- [在 Azure 入口網站中使用 RBAC 授與 Azure 容器和佇列的存取權](storage-auth-aad-rbac-portal.md)
- [透過 Azure CLI 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-cli.md)
- [透過 PowerShell 使用 RBAC 授與 Azure Blob 和佇列資料的存取權](storage-auth-aad-rbac-powershell.md)
