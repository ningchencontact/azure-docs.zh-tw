---
title: 建立 Azure 的進階檔案儲存體帳戶
description: 在本文中，您會學習如何建立 Azure 的進階檔案儲存體帳戶和進階檔案共用。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844512"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>如何建立 Azure 的進階檔案共用

FileStorage （預覽） 的儲存體帳戶類型代表 Azure 檔案服務，可讓您使用進階效能特性建立檔案共用的新層級。 這些檔案共用專為高效能和企業規模應用程式，提供一致的低延遲、 高 IOPS，以及高輸送量的共用。

這篇文章會示範如何建立這個新的帳戶類型使用[Azure 入口網站](https://portal.azure.com/)，Azure PowerShell 和 Azure CLI。

## <a name="prerequisites"></a>必要條件

若要存取 Azure 儲存體，您需要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>建立進階檔案共用使用 Azure 入口網站

### <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。

### <a name="create-a-filestorage-preview-storage-account"></a>建立 FileStorage （預覽） 的儲存體帳戶

現在您已準備好建立您的儲存體帳戶。

每個儲存體帳戶都必須屬於 Azure 資源群組。 資源群組是用來群組 Azure 服務的邏輯容器。 當您建立儲存體帳戶時，可以選擇建立新的資源群組，或使用現有的資源群組。 這篇文章會示範如何建立新的資源群組。

1. 在 Azure 入口網站中，選取**儲存體帳戶**左側功能表上。

    ![Azure 入口網站的主頁面上，選取儲存體帳戶](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 在出現的 [儲存體帳戶] 視窗上，選擇 [新增]。
1. 選取要在其中建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組] 欄位下方，選取 [新建]。 輸入新資源群組的名稱，如下圖所示。

1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
1. 選取儲存體帳戶的位置，或使用預設位置。
1. 針對**效能**選取**Premium**。
1. 選取 **帳戶種類**，然後選擇**FileStorage （預覽）**。
1. 離開**複寫**設為其預設值是**本地備援儲存體 (LRS)**。

    ![如何建立進階檔案儲存體帳戶](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. 選取 [檢閱 + 建立]，以檢閱您的儲存體帳戶設定並建立帳戶。
1. 選取 [建立] 。

一旦建立您的儲存體帳戶資源之後，瀏覽至它。

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

1. 在儲存體帳戶的左側功能表中，捲動到**檔案服務**區段，然後選取**檔案 （預覽）**。
1. 選取  **+ 檔案共用**來建立進階檔案共用。
1. 輸入的名稱和所需的配額為檔案共用，然後選取**建立**。

> [!NOTE]
> 佈建的共用大小由共用配額，佈建大小計費檔案共用，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)如需詳細資訊。

   ![建立進階檔案共用](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>清除資源

如果您想要清除在本文中建立的資源，您可以只刪除資源群組。 刪除資源群組也會刪除相關聯的儲存體帳戶，以及任何其他資源群組相關聯的資源。

## <a name="create-a-premium-file-share-using-powershell"></a>建立使用 PowerShell 的進階檔案共用

### <a name="create-an-account-using-powershell"></a>使用 PowerShell 建立帳戶

安裝最新版的 [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) 模組。

然後升級您的 powershell 模組、登入您的 Azure 訂用帳戶、建立資源群組，然後建立儲存體帳戶。

### <a name="upgrade-your-powershell-module"></a>升級 PowerShell 模組

若要使用 PowerShell 的進階檔案互動，您必須安裝最新的 Az.Storage 模組。

從以提高的權限開啟 PowerShell 工作階段作為開始。

安裝 Az.Storage 模組：

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶

請使用 `Login-AzAccount` 命令並遵循畫面上的指示進行驗證。

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>建立資源群組

若要使用 PowerShell 建立新的資源群組，請使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令：

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-preview-storage-account"></a>建立 filestorage （預覽） 的儲存體帳戶

若要從 PowerShell 建立 FileStorage （預覽） 的儲存體帳戶，請使用[新增 AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)命令：

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

已 FileStorage 帳戶之後，您可以建立進階檔案共用。 使用[新增 AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet 來建立一個。

> [!NOTE]
> 佈建的共用大小由共用配額，佈建大小計費檔案共用，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)如需詳細資訊。

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>清除資源

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令： 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>建立進階檔案共用使用 Azure CLI

若要啟動 Azure Cloud Shell，請登入 [Azure 入口網站](https://portal.azure.com)。

若想要登入您的本機安裝 CLI，請執行登入命令：

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>新增 Azure 進階檔案的 CLI 擴充功能

若要使用 CLI 的進階檔案的互動，您必須將延伸加入至您的殼層。

若要這樣做，請使用 Cloud Shell 或本機殼層輸入以下命令：`az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>建立資源群組

若要使用 Azure CLI 建立新的資源群組，請使用 [az group create](/cli/azure/group) 命令。

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>建立 FileStorage （預覽） 的儲存體帳戶

若要從 Azure CLI 建立 FileStorage （預覽） 的儲存體帳戶，請使用[az 儲存體帳戶建立](/cli/azure/storage/account)命令。

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰

儲存體帳戶金鑰可控制儲存體帳戶，在這篇文章中的資源的存取權，我們會使用金鑰，若要建立進階檔案共用。 當您建立儲存體帳戶時，系統會自動建立金鑰。 您可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令取得儲存體帳戶的儲存體帳戶金鑰：

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>建立進階檔案共用

已 FileStorage 帳戶之後，您可以建立進階檔案共用。 使用[az 儲存體共用建立](/cli/azure/storage/share)命令建立一個。

> [!NOTE]
> 佈建的共用大小由共用配額，佈建大小計費檔案共用，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)如需詳細資訊。

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>清除資源

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用 [az group delete](/cli/azure/group) 命令。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立進階檔案儲存體帳戶。 若要深入了解此帳戶提供的效能，繼續規劃指南的效能層一節。

> [!div class="nextstepaction"]
> [檔案共用的效能層級](storage-files-planning.md#file-share-performance-tiers)