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
ms.openlocfilehash: a0972beff48e07b6ce8afdcec10581300f59ed41
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786993"
---
# <a name="use-an-azure-ad-identity-to-access-blob-and-queue-data-with-cli-or-powershell"></a>使用 CLI 或 PowerShell 使用 Azure AD 身分識別來存取 blob 和佇列資料

Azure 存储为 Azure CLI 和 PowerShell 提供相应的扩展，使用户可登录并在 Azure Active Directory (Azure AD) 标识下运行脚本命令。 Azure AD 身分識別可以是使用者、群組或應用程式服務主體，也可以是 [Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 您可以指派權限來存取 blob 和佇列資料至 Azure AD 身分識別，透過角色型存取控制 (RBAC)。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[通过 RBAC 管理 Azure 存储数据访问权限](storage-auth-aad-rbac.md)。

使用 Azure AD 标识登录 Azure CLI 或 PowerShell 时，会返回一个用于访问该标识下 Azure 存储的访问令牌。 然後 CLI 或 PowerShell 會自動使用該權杖，針對 Azure 儲存體對作業進行授權。 針對支援的作業，您不需要再使用命令傳遞帳戶金鑰或 SAS 權杖。

## <a name="supported-operations"></a>支援的作業

扩展支持针对容器和队列的操作。 可调用的操作取决于向 Azure AD 标识授予的权限，此类权限用于登录 Azure CLI 或 PowerShell。 給予 Azure 儲存體容器或佇列的權限，是透過角色型存取控制 (RBAC) 指派的。 例如，如果將資料讀者角色指派給身分識別，則您可以執行讀取容器或佇列資料的指令碼命令。 如果將資料參與者角色指派給身分識別，則您可以執行讀取、寫入或刪除容器或佇列或其中所含資料的指令碼命令。 

如需容器或佇列上每個 Azure 儲存體作業所需權限的詳細資訊，請參閱[呼叫 REST 作業的權限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>使用 Azure AD 凭据调用 CLI 命令

Azure CLI 支援`--auth-mode`blob 和佇列資料作業的參數：

- 将 `--auth-mode` 参数设置为 `login`，以使用 Azure AD 安全主体登录。
- 將 `--auth-mode` 參數設定為舊版 `key` 值，以便在未針對帳戶提供驗證參數時，嘗試查詢帳戶金鑰。 

以下示例演示如何在 Azure CLI 中使用 Azure AD 凭据，在新的存储帐户中创建一个容器。 请务必将尖括号中的占位符值替换为你自己的值： 

1. 确保已安装 Azure CLI 2.0.46 或更高版本。 執行 `az --version` 來檢查您安裝的版本。

1. 运行 `az login` 并在浏览器窗口中进行身份验证： 

    ```azurecli
    az login
    ```
    
1. 指定所需的订阅。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 建立資源群組。 使用 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 在该资源组中创建存储帐户： 

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
    
1. 创建容器之前，请向自己分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使你是帐户所有者，也需要显式权限才能针对存储帐户执行数据操作。 如需有關指派 RBAC 角色的詳細資訊，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > 传播 RBAC 角色分配可能需要花费几分钟时间。
    
1. 在将 `--auth-mode` 参数设置为 `login` 的情况下，调用 [az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) 命令以使用 Azure AD 凭据创建容器：

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

與 `--auth-mode` 參數相關聯的環境變數是 `AZURE_STORAGE_AUTH_MODE`。 可在环境变量中指定相应的值，以免每次调用 Azure 存储数据操作都要包含该值。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 凭据调用 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 登录并使用 Azure AD 凭据针对 Azure 存储运行后续操作，请创建一个存储上下文用于引用存储帐户，并包含 `-UseConnectedAccount` 参数。

以下示例演示如何在 Azure PowerShell 中使用 Azure AD 凭据，在新的存储帐户中创建一个容器。 请务必将尖括号中的占位符值替换为你自己的值：

1. 使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后遵照屏幕上的指示输入 Azure AD 凭据： 

    ```powershell
    Connect-AzAccount
    ```
    
1. 调用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 调用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建存储帐户。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 调用 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 获取用于指定新存储帐户的存储帐户上下文。 对存储帐户执行操作时，可以引用上下文而不是重复传入凭据。 包含 `-UseConnectedAccount` 参数，以使用 Azure AD 凭据调用任何后续数据操作：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 创建容器之前，请向自己分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使你是帐户所有者，也需要显式权限才能针对存储帐户执行数据操作。 如需有關指派 RBAC 角色的詳細資訊，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > 传播 RBAC 角色分配可能需要花费几分钟时间。

1. 调用 [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) 创建容器。 由于此调用使用在前面步骤中创建的上下文，因此将使用你的 Azure AD 凭据创建容器。 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>後續步驟

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何使用受管理的身分識別與 Azure 儲存體的 Azure 資源，請參閱[適用於 Azure 資源管理身分識別的驗證的存取權的 blob 和佇列與 Azure](storage-auth-aad-msi.md)。
- 若要深入了解如何從儲存體應用程式內授權容器和佇列的存取權，請參閱[使用 Azure AD 與儲存體應用程式](storage-auth-aad-app.md)。
