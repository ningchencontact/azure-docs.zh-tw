---
title: 使用 Azure 入口網站來存取 blob 或佇列資料-Azure 儲存體
description: 當您存取 blob，或使用 Azure 入口網站的佇列資料，在入口網站提出要求到 Azure 儲存體在幕後。 這些要求至 Azure 儲存體可以進行驗證和授權使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00f34fa9a1932aebd467163e0ed7441c993387df
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153997"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>使用 Azure 入口網站來存取 blob 或佇列資料

當您存取 blob 或佇列資料使用[Azure 入口網站](https://portal.azure.com)，入口網站對 Azure 儲存體提出要求，在幕後。 這些要求至 Azure 儲存體可以進行驗證和授權使用您的 Azure AD 帳戶或儲存體帳戶存取金鑰。 在入口網站會指出哪一種驗證方法，您使用，並可讓您在兩者之間切換，如果您有適當的權限。  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>存取 blob 或佇列資料所需的權限

根據您要如何驗證存取 Azure 入口網站中的 blob 或佇列資料，您將需要特定權限。 在大部分情況下，這些權限是透過角色型存取控制 (RBAC) 提供。 如需有關 RBAC 的詳細資訊，請參閱 <<c0> [ 什麼是角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)。

### <a name="account-access-key"></a>帳戶存取金鑰

若要存取 blob 和佇列資料的帳戶存取金鑰，您必須包含 RBAC 動作的 RBAC 角色指派給您**Microsoft.Storage/storageAccounts/listkeys/action**。 內建或自訂角色，可能是此 RBAC 角色。 支援的內建角色**Microsoft.Storage/storageAccounts/listkeys/action**包括：

- Azure Resource Manager[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure Resource Manager[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [儲存體帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

當您嘗試存取 Azure 入口網站中的 blob 或佇列資料時，在入口網站會先檢查是否您已獲指派的角色**Microsoft.Storage/storageAccounts/listkeys/action**。 如果您已獲指派的角色與此動作，在入口網站會使用帳戶金鑰來存取 blob 和佇列資料。 如果您有尚未指派的角色與此動作，接著在入口網站會嘗試使用您的 Azure AD 帳戶來存取資料。

> [!NOTE]
> 服務管理員和共同管理員，才會進行傳統訂用帳戶系統管理員角色包含的 Azure Resource Manager 中的對等[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色。 **擁有者**角色包含的所有動作，包括**Microsoft.Storage/storageAccounts/listkeys/action**，因此具有其中一個系統管理角色的使用者也可以存取 blob 和佇列資料帳戶金鑰。 如需詳細資訊，請參閱 <<c0> [ 傳統訂用帳戶系統管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="azure-ad-account"></a>Azure AD 帳戶

若要使用您的 Azure AD 帳戶在 Azure 入口網站中存取 blob 或佇列的資料，這兩個下列陳述式必須是適用於您程式碼：

- 您已獲指派新的 Azure Resource Manager[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色，且至少已設定領域的儲存體帳戶層級或更高版本。 **讀取器**角色會授與的限制最嚴格的權限，但另一個 Azure Resource Manager 角色，授與儲存體帳戶管理資源的存取權也可以接受。
- 請提供 blob 或佇列的資料存取的內建或自訂角色，您已獲指派。

**讀取器**角色指派或另一個 Azure Resource Manager 角色指派是必要的使用者可以檢視並瀏覽 Azure 入口網站中的儲存體帳戶管理資源。 授與 blob 或佇列資料的存取權的 RBAC 角色不會授與儲存體帳戶管理資源的存取權。 若要存取入口網站中的 blob 或佇列資料，使用者必須瀏覽儲存體帳戶資源的權限。 如需有關這項需求的詳細資訊，請參閱 <<c0> [ 指派 「 讀者 」 角色存取入口網站](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支援的 blob 或佇列資料存取的內建角色包括：

- [儲存體 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner):針對 Azure Data Lake 儲存體 Gen2 的 POSIX 存取控制。
- [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):Blob 的讀取/寫入/刪除權限。
- [儲存體 Blob 資料讀者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):Blob 的唯讀權限。
- [儲存體佇列資料參與者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor):佇列的讀取/寫入/刪除權限。
- [儲存體佇列資料讀者](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader):佇列的唯讀權限。
    
自訂角色可支援不同的內建角色所提供的相同權限的組合。 如需建立自訂的 RBAC 角色的詳細資訊，請參閱[適用於 Azure 資源的自訂角色](../../role-based-access-control/custom-roles.md)並[了解適用於 Azure 資源的角色定義](../../role-based-access-control/role-definitions.md)。

> [!NOTE]
> 無法列出傳統訂用帳戶系統管理員角色的佇列。 若要列出排入佇列，使用者必須已指派給他們的 Azure Resource Manager**讀取器**角色，**儲存體佇列資料讀者**角色，或**儲存體佇列資料參與者**角色。

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>瀏覽至 blob 或佇列，在入口網站

若要在入口網站中檢視 blob 或佇列的資料，請瀏覽至**概觀**儲存體帳戶，然後按一下連結，如**Blob**或是**佇列**。 或者您可以導覽至**Blob 服務**並**佇列服務**功能表中的區段。 

![瀏覽至 Azure 入口網站中的 blob 或佇列資料](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>判斷目前的驗證方法

當您瀏覽至容器或佇列時，在 Azure 入口網站會指出是否您目前使用的帳戶存取金鑰或您的 Azure AD 帳戶進行驗證。

在本節中的範例會示範存取容器和 blob，但存取佇列和其訊息，或列出佇列時，入口網站會顯示相同的訊息。

### <a name="account-access-key"></a>帳戶存取金鑰

如果您要使用的帳戶存取金鑰進行驗證，您會看到**便捷鍵**指定為入口網站中的驗證方法：

![目前的帳戶金鑰存取容器資料](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

若要切換到使用 Azure AD 帳戶，請按一下圖中反白顯示的連結。 如果您有適當的權限，透過 RBAC 角色指派給您，您將能夠繼續進行。 不過，如果您沒有正確的權限，您會看到一則錯誤訊息如下所示：

![顯示是否 Azure AD 帳戶不支援存取錯誤](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

請注意，是否您的 Azure AD 帳戶沒有檢視權限清單中會出現任何 blob。 按一下 **切換至 存取金鑰**連結来使用的存取金鑰，再次進行驗證。

### <a name="azure-ad-account"></a>Azure AD 帳戶

如果您要使用您的 Azure AD 帳戶進行驗證，您會看到**Azure AD 使用者帳戶**指定為入口網站中的驗證方法：

![目前正在存取與 Azure AD 帳戶的容器資料](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

若要切換為使用帳戶存取金鑰，請按一下圖中反白顯示的連結。 如果您有權限的帳戶金鑰，然後您將能夠繼續進行。 不過，如果您沒有帳戶金鑰的存取權，您會看到一則錯誤訊息如下所示：

![如果您沒有帳戶金鑰的存取權所示的錯誤](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

請注意，是否您沒有帳戶金鑰的存取權沒有任何 blob 將會出現在清單中。 按一下 **切換至 Azure AD 使用者帳戶**連結来使用您的 Azure AD 帳戶，再次進行驗證。

## <a name="next-steps"></a>後續步驟

- [驗證存取 Azure blob 和佇列使用 Azure Active Directory](storage-auth-aad.md)
- [Azure 容器和佇列使用 RBAC 在 Azure 入口網站中的授與存取權](storage-auth-aad-rbac-portal.md)
- [授與存取權與使用 Azure CLI 的 RBAC 的 Azure blob 和佇列資料](storage-auth-aad-rbac-cli.md)
- [使用 RBAC，使用 PowerShell 的 Azure blob 和佇列資料的授與存取權](storage-auth-aad-rbac-powershell.md)
