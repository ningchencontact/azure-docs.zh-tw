---
title: 執行 Azure CLI 或 PowerShell 命令，在 Azure AD 身分識別來存取 blob 和佇列資料 |Microsoft Docs
description: Azure CLI 和 PowerShell 支援登入 Azure AD 身分識別，在 Azure 儲存體 blob 和佇列資料上執行命令。 存取權杖是針對工作階段提供，用來授權呼叫作業。 權限取決於指派給 Azure AD 身分識別的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d1fdafaaecd448fd09fc40cf5f6173ce600ac4f9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483200"
---
# <a name="use-an-azure-ad-identity-to-access-blob-and-queue-data-with-cli-or-powershell"></a>使用 CLI 或 PowerShell 使用 Azure AD 身分識別來存取 blob 和佇列資料

Azure 儲存體提供 Azure CLI 和 PowerShell 可讓您登入，並執行指令碼的命令，在 Azure Active Directory (Azure AD) 身分識別的延伸模組。 Azure AD 身分識別可以是使用者、群組或應用程式服務主體，也可以是 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 您可以指派權限來存取 blob 和佇列資料至 Azure AD 身分識別，透過角色型存取控制 (RBAC)。 如需有關 Azure 儲存體中的 RBAC 角色的詳細資訊，請參閱 < [Azure 儲存體資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。

當您登入 Azure CLI 或 PowerShell 的 Azure AD 身分識別時，會傳回該識別存取 Azure 儲存體的存取權杖。 然後 CLI 或 PowerShell 會自動使用該權杖，針對 Azure 儲存體對作業進行授權。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

## <a name="supported-operations"></a>支援的作業

對容器和佇列作業支援的延伸模組。 您可以呼叫哪些作業取決於與您登入 Azure CLI 或 PowerShell 的 Azure AD 身分識別授與的權限。 給予 Azure 儲存體容器或佇列的權限，是透過角色型存取控制 (RBAC) 指派的。 例如，如果將資料讀者角色指派給身分識別，則您可以執行讀取容器或佇列資料的指令碼命令。 如果將資料參與者角色指派給身分識別，則您可以執行讀取、寫入或刪除容器或佇列或其中所含資料的指令碼命令。 

如需容器或佇列上每個 Azure 儲存體作業所需權限的詳細資訊，請參閱[呼叫 REST 作業的權限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>呼叫 CLI 命令中使用 Azure AD 認證

Azure CLI 支援`--auth-mode`blob 和佇列資料作業的參數：

- 設定`--auth-mode`參數來`login`使用 Azure AD 安全性主體來登入。
- 將 `--auth-mode` 參數設定為舊版 `key` 值，以便在未針對帳戶提供驗證參數時，嘗試查詢帳戶金鑰。 

下列範例會示範如何建立一個容器，新的儲存體帳戶從 Azure CLI 中使用您的 Azure AD 認證。 請務必以您自己的值取代角括弧括住的預留位置值： 

1. 請確定您已安裝 Azure CLI 版本 2.0.46 或更新版本。 執行 `az --version` 來檢查您安裝的版本。

1. 執行`az login`並在瀏覽器視窗中進行驗證： 

    ```azurecli
    az login
    ```
    
1. 指定您想要的訂用帳戶。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 建立資源群組。 建立資源群組使用的儲存體帳戶[az 儲存體帳戶建立](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. 建立容器之前，先指派[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)給您自己的角色。 雖然您是帳戶擁有者，您需要明確的權限以執行資料作業的儲存體帳戶。 如需有關指派 RBAC 角色的詳細資訊，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘的時間傳播。
    
1. 呼叫[az 儲存體容器建立](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)命令搭配`--auth-mode`參數設定為`login`建立容器，使用您的 Azure AD 認證：

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

與 `--auth-mode` 參數相關聯的環境變數是 `AZURE_STORAGE_AUTH_MODE`。 您可以在環境變數，以避免將它加入至 Azure 儲存體資料作業每次呼叫中指定適當的值。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>呼叫 PowerShell 命令中使用 Azure AD 認證

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 登入，並執行後續的作業，對 Azure 儲存體使用 Azure AD 認證，建立參考儲存體帳戶的儲存體內容，並包括`-UseConnectedAccount`參數。

下列範例示範如何建立新的儲存體帳戶從 Azure PowerShell 中的容器，使用您的 Azure AD 認證。 請務必以您自己的值取代角括弧括住的預留位置值：

1. 使用您 Azure 訂用帳戶登入`Connect-AzAccount`命令，並遵循螢幕上指示執行，請輸入您的 Azure AD 認證： 

    ```powershell
    Connect-AzAccount
    ```
    
1. 建立 Azure 資源群組，藉由呼叫[新增 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 建立儲存體帳戶，藉由呼叫[新增 AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 取得儲存體帳戶內容，指定新的儲存體帳戶，藉由呼叫[新增 AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)。 在儲存體帳戶時，您可以參考的內容，而不是重複傳遞的認證。 包含`-UseConnectedAccount`參數來呼叫任何後續的資料作業，使用您的 Azure AD 認證：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 建立容器之前，先指派[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)給您自己的角色。 雖然您是帳戶擁有者，您需要明確的權限以執行資料作業的儲存體帳戶。 如需有關指派 RBAC 角色的詳細資訊，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘的時間傳播。

1. 建立容器，藉由呼叫[新增 AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)。 由於這個呼叫會使用在先前步驟中建立的內容，會建立容器，使用您的 Azure AD 認證。 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 儲存體的 RBAC 角色，請參閱[儲存體的資料，使用 RBAC 管理存取權限](storage-auth-aad-rbac.md)。
- 若要了解如何使用受管理的身分識別與 Azure 儲存體的 Azure 資源，請參閱[適用於 Azure 資源管理身分識別的驗證的存取權的 blob 和佇列與 Azure](storage-auth-aad-msi.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
