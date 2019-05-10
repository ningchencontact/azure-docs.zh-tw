---
title: 建立儲存體帳戶 - Azure 儲存體
description: 在本做法文章中，您了解如何建立使用 Azure 入口網站、 Azure PowerShell 或 Azure CLI 的儲存體帳戶。 Azure 儲存體帳戶提供 Microsoft Azure 中唯一的命名空間，來儲存及存取您在 Azure 儲存體中建立的資料物件。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234165"
---
# <a name="create-a-storage-account"></a>建立儲存體帳戶

Azure 儲存體帳戶包含您所有的 Azure 儲存體資料物件：Blob、檔案、佇列、資料表和磁碟。 儲存體帳戶提供唯一的命名空間為您的 Azure 儲存體資料可存取的任何地方在世界各地透過 HTTP 或 HTTPS。 持久性和高可用性、 安全且可大幅擴充您的 Azure 儲存體帳戶中的資料。

在本做法文章中，您學會建立使用儲存體帳戶[Azure 入口網站](https://portal.azure.com/)， [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)， [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)，或有[Azure Resource Manager範本](../../azure-resource-manager/resource-group-overview.md)。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

無。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

本做法文章需要 Azure PowerShell 模組 Az 0.7 或更新版本。 執行 `Get-Module -ListAvailable Az` 來尋找您目前的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以登入 Azure，並且以下列兩種方式之一執行 Azure CLI 命令：

- 您可以在 Azure Cloud Shell 中執行 CLI 命令從 Azure 入口網站中。
- 可以安装 CLI 并在本地运行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 Azure CLI 已預先安裝和設定為使用與您的帳戶。 按一下  **Cloud Shell**在 Azure 入口網站的右上方區段中的功能表上的按鈕：

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

按鈕會啟動互動式殼層，您可以使用在本做法文章執行所述的步驟：

[![顯示 Cloud Shell 視窗的螢幕擷取畫面](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本機安裝 CLI

您也可以在本機安裝及使用 Azure CLI。 本做法文章中會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

# <a name="templatetabtemplate"></a>[範本](#tab/template)

無。

---

## <a name="sign-in-to-azure"></a>登入 Azure

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

登入 [Azure 入口網站](https://portal.azure.com)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示進行驗證。

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要啟動 Azure Cloud Shell，請登入[Azure 入口網站](https://portal.azure.com)。

若要登入您的本機安裝的 cli，執行[az 登入](/cli/azure/reference-index#az-login)命令：

```cli
az login
```

# <a name="templatetabtemplate"></a>[範本](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>建立儲存體帳戶

現在，您已經準備好建立儲存體帳戶。

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 這篇文章會示範如何建立新的資源群組。

**一般用途 v2** 儲存體帳戶提供所有 Azure 儲存體服務的存取權：Blob、檔案、佇列、資料表和磁碟。 此處概述的步驟會建立一般用途 v2 儲存體帳戶，但若要建立任何類型的儲存體帳戶的步驟很類似。

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

首先，透過 PowerShell，使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令建立新的資源群組：

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

如果您不確定要針對 `-Location` 參數指定哪一個區域，您可以使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令擷取訂用帳戶所支援的區域清單：

```powershell
Get-AzLocation | select Location
$location = "westus"
```

使用接下來，建立具有讀取權限異地備援儲存體 (RA-GRS) 的一般用途 v2 儲存體帳戶[新增 AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)命令。 請記住您的儲存體帳戶名稱必須是唯一的 Azure，因此將括號括住的預留位置值取代您自己唯一的值：

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

若要建立一般用途 v2 儲存體帳戶使用不同的複寫選項，以取代所需的值，在下表中**SkuName**參數。

|複寫選項  |SkuName 參數  |
|---------|---------|
|本機備援儲存體 (LRS)     |Standard_LRS         |
|區域備援儲存體 (ZRS)     |Standard_ZRS         |
|異地備援儲存體 (GRS)     |Standard_GRS         |
|讀取權限異地備援儲存體 (GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，透過 Azure CLI，使用 [az group create](/cli/azure/group#az_group_create) 命令來建立新的資源群組。

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

如果您不確定要針對 `--location` 參數指定哪一個區域，您可以使用 [az account list-locations](/cli/azure/account#az_account_list) 命令擷取訂用帳戶所支援的區域清單。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

使用接下來，建立具有讀取權限異地備援儲存體的一般用途 v2 儲存體帳戶[az 儲存體帳戶建立](/cli/azure/storage/account#az_storage_account_create)命令。 請記住您的儲存體帳戶名稱必須是唯一的 Azure，因此將括號括住的預留位置值取代您自己唯一的值：

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

若要建立一般用途 v2 儲存體帳戶使用不同的複寫選項，以取代所需的值，在下表中**sku**參數。

|複寫選項  |sku 參數  |
|---------|---------|
|本機備援儲存體 (LRS)     |Standard_LRS         |
|區域備援儲存體 (ZRS)     |Standard_ZRS         |
|異地備援儲存體 (GRS)     |Standard_GRS         |
|讀取權限異地備援儲存體 (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[範本](#tab/template)

您可以使用 Azure PowerShell 或 Azure CLI 來部署 Resource Manager 範本以建立儲存體帳戶。 本做法文章中使用的範本是來自[Azure Resource Manager 快速入門範本](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 若要執行指令碼，請選取 [試試看] 開啟 Azure Cloud shell。 若要貼上指令碼，請以滑鼠右鍵按一下 Shell，然後選取 [貼上]。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

若要了解如何建立範本，請參閱：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)。
- [儲存體帳戶範本參考](/azure/templates/microsoft.storage/allversions)。
- [其他儲存體帳戶範本範例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)。

---

如需可用複寫選項的詳細資訊，請參閱[儲存體複寫選項](storage-redundancy.md)。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除本做法文章中所建立的資源，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

若要使用 Azure 入口網站刪除資源群組：

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組] 以顯示資源群組的清單。
2. 找出要刪除的資源群組，以滑鼠右鍵按一下清單右側的 [更多] 按鈕 (**...**)。
3. 選取 [刪除資源群組] 並且確認。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令：

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [az group delete](/cli/azure/group#az_group_delete) 命令。

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[範本](#tab/template)

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 Azure PowerShell 或 Azure CLI。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>後續步驟

在本做法文章中，您已建立一般用途 v2 的標準儲存體帳戶。 若要了解如何上傳和下載 blob 從儲存體帳戶，繼續進行 Blob 儲存體快速入門的其中一個。

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

> [!div class="nextstepaction"]
> [使用 Azure 入口網站處理 Blob](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [使用 PowerShell 處理 Blob](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [使用 Azure CLI 處理 Blob](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[範本](#tab/template)

> [!div class="nextstepaction"]
> [使用 Azure 入口網站處理 Blob](../blobs/storage-quickstart-blobs-portal.md)

---
