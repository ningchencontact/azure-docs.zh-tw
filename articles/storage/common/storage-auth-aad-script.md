---
title: 執行 Azure CLI 或具有 Azure AD 認證的 PowerShell 命令, 以存取 blob 或佇列資料 |Microsoft Docs
description: Azure CLI 和 PowerShell 支援使用 Azure AD 認證進行登入, 以在 Azure 儲存體 blob 和佇列資料上執行命令。 存取權杖是針對工作階段提供，用來授權呼叫作業。 許可權取決於指派給 Azure AD 安全性主體的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e1c7f4531dee8673cc5b6dfe675e4c793144931e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671093"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>執行 Azure CLI 或具有 Azure AD 認證的 PowerShell 命令, 以存取 blob 或佇列資料

Azure 儲存體提供 Azure CLI 和 PowerShell 的延伸模組, 可讓您使用 Azure Active Directory (Azure AD) 認證來登入及執行指令碼命令。 當您使用 Azure AD 認證登入 Azure CLI 或 PowerShell 時, 會傳回 OAuth 2.0 存取權杖。 CLI 或 PowerShell 會自動使用該權杖來授權針對 Blob 或佇列儲存體進行的後續資料作業。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

您可以透過角色型存取控制 (RBAC), 將 blob 和佇列資料的許可權指派給 Azure AD 的安全性主體。 如需 Azure 儲存體中 RBAC 角色的詳細資訊, 請參閱[使用 rbac 來管理 Azure 儲存體資料的存取權限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支援的作業

支援容器和佇列上的作業的延伸模組。 您可能呼叫的作業取決於授與您用來登入 Azure CLI 或 PowerShell 之 Azure AD 安全性主體的許可權。 給予 Azure 儲存體容器或佇列的權限，是透過角色型存取控制 (RBAC) 指派的。 例如, 如果您被指派「 **Blob 資料讀取**者」角色, 則您可以執行從容器或佇列讀取資料的指令碼命令。 如果您已獲指派**Blob 資料參與者**角色, 則您可以執行讀取、寫入或刪除容器或佇列或其包含之資料的指令碼命令。 

如需有關容器或佇列上每個 Azure 儲存體作業所需許可權的詳細資訊, 請參閱[使用 OAuth 權杖呼叫儲存體作業](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>使用 Azure AD 認證呼叫 CLI 命令

Azure CLI 支援 blob `--auth-mode`和佇列資料作業的參數:

- 將參數設定為`login` , 以使用 Azure AD 安全性主體進行登入。 `--auth-mode`
- 將 `--auth-mode` 參數設定為舊版 `key` 值，以便在未針對帳戶提供驗證參數時，嘗試查詢帳戶金鑰。 

下列範例示範如何使用您的 Azure AD 認證, 從 Azure CLI 在新的儲存體帳戶中建立容器。 請記得使用您自己的值來取代角括弧中的預留位置值: 

1. 請確定您已安裝 Azure CLI 版2.0.46 版或更新版本。 執行 `az --version` 來檢查您安裝的版本。

1. 在`az login`瀏覽器視窗中執行和驗證: 

    ```azurecli
    az login
    ```

1. 指定您想要的訂用帳戶。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 建立資源群組。 使用[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)在該資源群組內建立儲存體帳戶:

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

1. 建立容器之前, 請先將[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色指派給自己。 即使您是帳戶擁有者, 您還是需要明確的許可權, 才能對儲存體帳戶執行資料作業。 如需指派 RBAC 角色的詳細資訊, 請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘的時間才能傳播。

1. 呼叫[az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)命令, 並`--auth-mode`將參數設定為`login` , 以使用您的 Azure AD 認證來建立容器:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

與 `--auth-mode` 參數相關聯的環境變數是 `AZURE_STORAGE_AUTH_MODE`。 您可以在環境變數中指定適當的值, 以避免在每次呼叫 Azure 儲存體資料作業時包含它。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 認證呼叫 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 來登入，並對使用 Azure AD 認證的 Azure 儲存體執行後續作業，請建立儲存體內容來參考儲存體帳戶，並包含 `-UseConnectedAccount` 參數。

下列範例示範如何使用您的 Azure AD 認證, 從 Azure PowerShell 在新的儲存體帳戶中建立容器。 請記得使用您自己的值來取代角括弧中的預留位置值:

1. 使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount)命令登入您的 Azure 帳戶：

    ```powershell
    Connect-AzAccount
    ```

    如需使用 PowerShell 登入 Azure 的詳細資訊，請參閱[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps)。

1. 藉由呼叫[remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)來建立 Azure 資源群組。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 藉由呼叫[new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount)來建立儲存體帳戶。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 藉由呼叫[AzStorageCoNtext](/powershell/module/az.storage/new-azstoragecontext), 取得指定新儲存體帳戶的儲存體帳戶內容。 在儲存體帳戶上作用時, 您可以參考內容, 而不是重複傳入認證。 `-UseConnectedAccount`包含參數, 以使用您的 Azure AD 認證來呼叫任何後續的資料作業:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 建立容器之前, 請先將[儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色指派給自己。 即使您是帳戶擁有者, 您還是需要明確的許可權, 才能對儲存體帳戶執行資料作業。 如需指派 RBAC 角色的詳細資訊, 請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure blob 和佇列資料的存取權](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色指派可能需要幾分鐘的時間才能傳播。

1. 藉由呼叫[new-azstoragecontainer](/powershell/module/az.storage/new-azstoragecontainer)來建立容器。 因為此呼叫會使用在先前步驟中建立的內容, 所以會使用您的 Azure AD 認證來建立容器。 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Azure 儲存體的 RBAC 角色, 請參閱[使用 Rbac 管理儲存體資料的存取權限](storage-auth-aad-rbac.md)。
- 若要瞭解如何使用適用于 Azure 資源的受控識別搭配 Azure 儲存體, 請參閱[使用 Azure 資源的 Azure Active Directory 和受控識別來驗證 blob 和佇列的存取權](storage-auth-aad-msi.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
