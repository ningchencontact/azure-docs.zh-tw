---
title: 管理在 Azure 入口網站中的儲存體帳戶設定 - Azure 儲存體 | Microsoft Docs
description: 了解如何管理 Azure 入口網站中的儲存體帳戶設定，包括設定存取控制設定、重新產生帳戶存取金鑰、變更存取層，或修改帳戶使用的複寫類型。 也了解如何刪除入口網站中的儲存體帳戶。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 60104496006e790887dd9c4b3e4c3196e0ef6444
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671364"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>管理在 Azure 入口網站中的儲存體帳戶設定

[Azure 入口網站](https://portal.azure.com)提供儲存體帳戶的各種不同設定。 本文會說明部分設定和使用方式。

## <a name="access-control"></a>存取控制

Azure 儲存體支援透過角色型存取控制（RBAC）的 Azure Active Directory 對 Blob 儲存體和佇列儲存體進行授權。 如需有關使用 Azure AD 進行授權的詳細資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](storage-auth-aad.md)。

對於使用者、群組、服務主體和受管理的身分識別，Azure 入口網站中的**存取控制**設定提供簡易的方式來指派 RBAC 角色。 如需指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 管理 blob 和佇列資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="tags"></a>Tags

Azure 儲存體支援 Azure Resource Manager 標記，可自訂分類並組合管理 Azure 資源。 您可以將標記套用至您的儲存體帳戶，以邏輯方式在您的訂用帳戶中分組這些帳戶。

儲存體帳戶的標記名稱上限為 128 個字元，且標記值上限為 256 個字元。

如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](../../azure-resource-manager/resource-group-using-tags.md)。

## <a name="access-keys"></a>存取金鑰

您建立儲存體帳戶時，Azure 會產生兩個 512 位元儲存體帳戶存取金鑰。 這些金鑰可用來透過共用金鑰授權儲存體帳戶的存取。 您可以旋轉和重新產生金鑰，不會造成應用程式中斷，Microsoft 建議您定期這麼做。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>查看帳戶金鑰和連接字串

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>重新產生存取金鑰

Microsoft 建議您定期重新產生存取金鑰，以協助保護您的儲存體帳戶安全無虞。 由於指派兩個存取金鑰，因此您可以旋轉金鑰。 旋轉金鑰時，確定您的應用程式會在整個程序維持 Azure 儲存體的存取權。 

> [!WARNING]
> 重新產生存取金鑰會影響相依於儲存體帳戶金鑰的應用程式或 Azure 服務。 使用帳戶金鑰來存取儲存體帳戶的任何用戶端必須更新並使用新的金鑰，包括媒體服務、雲端、桌面和行動應用程式，以及 Azure 儲存體的圖形化使用者介面應用程式，例如 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

請遵循此程序來旋轉您的儲存體帳戶金鑰：

1. 更新您的應用程式碼中的連接字串，以使用次要金鑰。
2. 重新產生儲存體帳戶的主要存取金鑰。 在 Azure 入口網站中的 [存取金鑰] 刀鋒視窗上，按一下 [重新產生 Key1]，然後按一下 [是] 確認您要重新產生新的金鑰。
3. 更新程式碼中的連接字串，以參考新的主要存取金鑰。
4. 以同樣的方式重新產生次要存取金鑰。

## <a name="account-configuration"></a>帳戶設定

建立儲存體帳戶之後，您可以修改其組態。 例如，您可以變更複寫資料的方式，也可以將帳戶存取層從「經常性存取層」變更為「非經常性存取層」。 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶，然後尋找並按一下 [設定] 之下的 [組態] 以檢視和/或變更帳戶組態。

變更儲存體帳戶設定可能會導致成本增加。 如需詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/) 頁面。

## <a name="delete-a-storage-account"></a>刪除儲存體帳戶

若要移除不再使用的儲存體帳戶，請在 [Azure 入口網站](https://portal.azure.com)中瀏覽至儲存體帳戶，然後按一下 [刪除]。 刪除儲存體帳戶會刪除整個帳戶，包括帳戶中的所有資料。

> [!WARNING]
> 您無法還原已刪除的儲存體帳戶，也無法擷取刪除之前所包含的任何內容。 請務必先備份您想要儲存的任何資料，再刪除帳戶。 這也適用於帳戶中的任何資源 - 一旦刪除 Blob、資料表、佇列或檔案，就是永久刪除。
> 

如果您嘗試刪除與 Azure 虛擬機器相關聯的儲存體帳戶，您可能會收到儲存體帳戶仍在使用中的相關錯誤。 如需此錯誤的疑難排解協助，請參閱[針對刪除儲存體帳戶時的錯誤進行疑難排解](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)。

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體帳戶概觀](storage-account-overview.md)
- [建立儲存體帳戶](storage-quickstart-create-account.md)
