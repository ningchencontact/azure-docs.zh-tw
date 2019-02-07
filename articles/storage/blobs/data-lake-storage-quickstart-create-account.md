---
title: 建立 Azure Data Lake Storage Gen2 儲存體帳戶 | Microsoft Docs
description: 快速了解如何使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 建立具有 Data Lake Storage Gen2 存取權的新儲存體帳戶
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: b49c69a488402e593573e7f9e4fda81057519149
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732442"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-storage-account"></a>快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶

Azure Data Lake Storage Gen2 [支援階層命名空間服務](data-lake-storage-introduction.md)，其提供專門打造的原生目錄型檔案系統，可與 Hadoop 分散式檔案系統 (HDFS) 搭配運作。 透過 [ABFS 驅動程式](data-lake-storage-abfs-driver.md)即可從 HDFS 存取 Data Lake Storage Gen2 資料。

本快速入門會示範如何使用 [Azure 入口網站](https://portal.azure.com/)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 或透過 [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 建立帳戶。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。 

|           | 必要條件 |
|-----------|--------------|
|入口網站     | None         |
|PowerShell | 本快速入門需要 PowerShell 模組 Az.Storage **0.7** 版或更新版本。 請執行 `Get-Module -ListAvailable Az.Storage` 命令來了解您目前使用的版本。 如果您執行此命令之後，沒有顯示任何結果，或如果顯示 **0.7** 以外的其他版本，表示您的 powershell 模組必須升級。 請參閱本指南[升級您的 powershell 模組](#upgrade-your-powershell-module)一節。
|CLI        | 您可以登入 Azure，並且以下列兩種方式之一執行 Azure CLI 命令： <ul><li>您可以從 Azure 入口網站，在 Azure Cloud Shell 中執行 CLI 命令 </li><li>您可以安裝 CLI，並在本機執行 CLI 命令</li></ul>|

在命令列上工作時，您可以選擇執行 Azure Cloud Shell 或在本機安裝 CLI。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 按一下 Azure 入口網站右上方功能表上的 [Cloud Shell] 按鈕：

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

按鈕會啟動互動式殼層，可讓您用來執行本快速入門中的步驟：

[![顯示 Cloud Shell 視窗的螢幕擷取畫面](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本機安裝 CLI

您也可以在本機安裝及使用 Azure CLI。 此快速入門需要您執行 Azure CLI 2.0.38 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>建立啟用 Azure Data Lake Storage Gen2 的儲存體帳戶

在建立帳戶前，請先建立資源群組，以作為儲存體帳戶或其他所建立 Azure 資源的邏輯容器。 如果您想要清除本快速入門所建立的資源，只要刪除資源群組即可。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及與資源群組相關聯的任何其他資源。 如需關於資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。

> [!NOTE]
> 您必須建立 **StorageV2 (general-purpose V2)** 類型的新儲存體帳戶，才能利用 Data Lake Storage Gen2 功能。  

如需儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

為您的儲存體帳戶命名時，請記住這些規則：

- 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。
- 儲存體帳戶名稱必須在 Azure 中是獨一無二的。 任兩個儲存體帳戶名稱不得相同。

## <a name="create-an-account-using-the-azure-portal"></a>使用 Azure 入口網站建立帳戶

登入 [Azure 入口網站](https://portal.azure.com)。

### <a name="create-a-resource-group"></a>建立資源群組

若要在 Azure 入口網站中建立資源群組，請遵循下列步驟：

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組]。
2. 按一下 [新增] 按鈕，以新增新的資源群組。
3. 輸入新資源群組的名稱。
4. 選取要在其中建立新資源群組的訂用帳戶。
5. 選擇資源群組的位置。
6. 按一下 [ **建立** ] 按鈕。  

   ![顯示在 Azure 入口網站建立資源群組的螢幕擷取畫面](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>建立一般用途的 v2 儲存體帳戶

若要在 Azure 入口網站中建立一般用途 v2 儲存體帳戶，請遵循下列步驟：

> [!NOTE]
> 階層命名空間目前可在所有公用區域中使用。 其目前無法在主權雲端中使用。

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [所有服務]。 然後，向下捲動至 [儲存體]，然後選擇 [儲存體帳戶]。 在出現的 [儲存體帳戶] 視窗上，選擇 [新增]。
2. 選取您先前建立的 [訂用帳戶] 和 [資源群組]。
3. 輸入儲存體帳戶的名稱。
4. 將 [位置] 設定為 [美國西部 2]
5. 讓這些欄位設定保留預設值：[效能]、[帳戶種類]、[複寫] 及 [存取層]。
6. 選擇您要在其中建立儲存體帳戶的訂用帳戶。
7. 選取 [下一步: 進階] > 
8. 保留 [安全性] 和 [虛擬網路] 欄位的預設值。
9. 在 [Data Lake Storage Gen2 (預覽版)] 區段中，將 [階層式命名空間] 設定為 [啟用]。
10. 按一下 [檢閱及建立] 建立儲存體帳戶。

    ![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

系統現在會透過入口網站建立儲存體帳戶。

### <a name="clean-up-resources"></a>清除資源

若要使用 Azure 入口網站刪除資源群組：

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組] 以顯示資源群組的清單。
2. 找出要刪除的資源群組，以滑鼠右鍵按一下清單右側的 [更多] 按鈕 (**...**)。
3. 選取 [刪除資源群組] 並且確認。

## <a name="create-an-account-using-powershell"></a>使用 PowerShell 建立帳戶

安裝最新版的 [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) 模組。

然後升級您的 powershell 模組、登入您的 Azure 訂用帳戶、建立資源群組，然後建立儲存體帳戶。

### <a name="upgrade-your-powershell-module"></a>升級 PowerShell 模組

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 PowerShell 和 Data Lake Storage Gen2 互動，需要安裝模組 Az.Storage **0.7** 版或更新版本。

從以提高的權限開啟 PowerShell 工作階段作為開始。

安裝 Az.Storage 模組

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="log-in-to-your-azure-subscription"></a>登入 Azure 訂用帳戶

請使用 `Login-AzAccount` 命令並遵循畫面上的指示進行驗證。

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>建立資源群組

若要使用 PowerShell 建立新的資源群組，請使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令： 

> [!NOTE]
> 階層命名空間目前可在所有公用區域中使用。 其目前無法在主權雲端中使用。

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>建立一般用途的 v2 儲存體帳戶

若要從 PowerShell 建立具有本地備援儲存體 (LRS) 的一般用途 v2 儲存體帳戶，請使用 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 命令：

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>清除資源

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令： 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>使用 Azure CLI 建立帳戶

若要啟動 Azure Cloud Shell，請登入 [Azure 入口網站](https://portal.azure.com)。

若想要登入您的本機安裝 CLI，請執行登入命令：

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>新增 Azure Data Lake Gen 2 的 CLI 擴充功能

若要透過 CLI 與 Data Lake Storage Gen2 互動，必須在殼層中新增擴充功能。

若要這樣做，請使用 Cloud Shell 或本機殼層輸入以下命令：`az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>建立資源群組

若要使用 Azure CLI 建立新的資源群組，請使用 [az group create](/cli/azure/group) 命令。

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > 階層命名空間目前可在所有公用區域中使用。 其目前無法在主權雲端中使用。

### <a name="create-a-general-purpose-v2-storage-account"></a>建立一般用途的 v2 儲存體帳戶

若要從 Azure CLI 建立具有本地備援儲存體的一般用途 v2 儲存體帳戶，請使用 [az storage account create](/cli/azure/storage/account) 命令。

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>清除資源

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [az group delete](/cli/azure/group) 命令。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立具備 Data Lake Storage Gen2 功能的儲存體帳戶。 若要了解如何在您的儲存體帳戶中上傳和下載 Blob，請參閱以下主題。

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
