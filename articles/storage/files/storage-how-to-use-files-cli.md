---
title: 使用 Azure CLI 管理 Azure 檔案共用的快速入門
description: 使用此快速入門了解如何使用 Azure CLI 來管理 Azure 檔案服務。
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 236a4e4f79e6da89154e1e52bb9f45daf3a54d59
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632038"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>快速入門：使用 Azure CLI 來建立及管理 Azure 檔案共用
本指南會逐步說明透過 Azure CLI 來使用 [Azure 檔案共用](storage-files-introduction.md)的基本概念。 Azure 檔案共用與其他檔案共用類似，但它儲存在雲端中，並且由 Azure 平台支援。 Azure 檔案共用支援業界標準 SMB 通訊協定，並可在多個機器、應用程式及執行個體上啟用檔案共用。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您決定在本機安裝及使用 Azure CLI，您必須執行 2.0.4 或更新版本的 Azure CLI，以便進行本文中的步驟。 執行 **az --version** 可找出 Azure CLI 版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

Azure CLI 命令預設會傳回 JavaScript 物件標記法 (JSON)。 JSON 是從 REST API 傳送和接收訊息的標準方式。 為了方便處理 JSON 回應，本文中有些範例會在 Azure CLI 命令上使用「查詢」參數。 這個參數會使用 [JMESPath 查詢語言](http://jmespath.org/) 來剖析 JSON。 若要進一步了解如何依照 JMESPath 查詢語言來使用 Azure CLI 命令的結果，請參閱 [JMESPath 教學課程](http://jmespath.org/tutorial.html)。

## <a name="sign-in-to-azure"></a>登入 Azure
如果您在本機使用 Azure CLI，請開啟提示字元並登入 Azure (如果您尚未這麼做)。

```bash 
az login
```

## <a name="create-a-resource-group"></a>建立資源群組
資源群組是一種邏輯容器，您會在其中部署與管理 Azure 資源。 如果您還沒有 Azure 資源群組，您可以使用 [az group create](/cli/azure/group#create) 命令建立一個。 

下列範例會在 [美國東部] 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶
儲存體帳戶是一種儲存體共用集區，可以在其中部署 Azure 檔案共用或其他儲存體資源 (例如 Blob 或佇列)。 儲存體帳戶可以包含無限多個檔案共用。 共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。

下列範例會使用 [az storage account create](/cli/azure/storage/account#create) 命令建立名為 mystorageaccount\<隨機數字\> 的儲存體帳戶，然後在變數 `$STORAGEACCT` 中放入該儲存體帳戶的名稱。 儲存體帳戶名稱必須是唯一的。 使用 `$RANDOM` 將數字附加至儲存體帳戶名稱，讓其成為唯一名稱。 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰
儲存體帳戶金鑰可控制儲存體帳戶中的資源存取。 當您建立儲存體帳戶時，系統會自動建立金鑰。 您可以使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 命令取得儲存體帳戶的儲存體帳戶金鑰： 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
現在，您可以建立第一個 Azure 檔案共用。 使用 [az storage share create](/cli/azure/storage/share#create) 命令來建立檔案共用。 此範例會建立名為 myshare 的 Azure 檔案共用： 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

共用名稱只能包含小寫字母、數字和單一連字號 (但開頭不可以是連字號)。 如需有關為檔案共用與檔案命名的完整詳細資料，請參閱[命名和參考共用、目錄、檔案及中繼資料](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="use-your-azure-file-share"></a>使用您的 Azure 檔案共用
Azure 檔案服務提供兩個在 Azure 檔案共用中使用檔案和資料夾的方法：業界標準[伺服器訊息區 (SMB) 通訊協定](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)和[檔案 REST 通訊協定](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)。 

若要使用 SMB 掛接檔案共用，請根據您的作業系統參閱下列文件：
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>透過檔案 REST 通訊協定使用 Azure 檔案共用 
您可以直接使用 File REST 通訊協定 (也就是自己製作 REST HTTP 呼叫)，但最常見的 File REST 通訊協定使用方式是使用 Azure CLI、[Azure PowerShell 模組](storage-how-to-use-files-powershell.md) 或「Azure儲存體 SDK」，這些都能以您選擇的指令碼/程式設計語言為 File REST 通訊協定提供良好的包裝函式。  

我們預期人們使用 Azure 檔案服務時，都會想透過 SMB 通訊協定來與 Azure 檔案共用搭配使用，因為這可讓他們使用預期能使用的現有應用程式和工具，但使用檔案 REST API 比使用 SMB 好的原因有很多個，例如：

- 您要從 Azure Bash Cloud Shell (無法透過 SMB 掛接檔案共用) 瀏覽檔案共用。
- 您需要從無法掛接 SMB 共用的用戶端執行指令碼或應用程式，例如未將連接埠 445 解除封鎖的內部部署用戶端。
- 您要利用無伺服器資源，例如 [Azure Functions](../../azure-functions/functions-overview.md)。 

下列範例示範如何搭配 File REST 通訊協定使用 Azure CLI，來操作 Azure 檔案共用。 

### <a name="create-a-directory"></a>建立目錄
若要在 Azure 檔案共用的根目錄建立名為 *myDirectory* 的新目錄，請使用 [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) 命令：

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>上傳檔案
若要示範如何使用 [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) 命令上傳檔案，請先在 Cloud Shell 暫存磁碟上建立要上傳的檔案。 在下列範例中，您將建立此檔案並將其上傳：

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

如果您要在本機執行 Azure CLI，請將 `~/clouddrive` 替換為存在於您的機器上的路徑。

上傳檔案之後，您可以使用 [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) 命令來確認檔案已上傳至 Azure 檔案共用：

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>下載檔案
您可以使用 [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) 命令，將您上傳的檔案複本下載至 Cloud Shell 暫存磁碟：

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>複製檔案
在不同的檔案共用間複製檔案，或者將檔案在共用與 Azure Blob 儲存體容器間來回複製，都是很常見的工作。 若要示範這項功能，請建立新的共用。 請使用 [az storage file copy](/cli/azure/storage/file/copy) 命令，將已上傳的檔案複製到這個新的共用： 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

現在，當您列出新共用中的檔案時，您應該會看到您所複製的檔案：

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

雖然在 Azure 檔案共用與 Azure Blob 儲存體容器間的移動檔案時使用 `az storage file copy start` 命令很方便，但建議您只在移動的檔案較大時才使用 AzCopy。 (即指移動的檔案數目和大小較大。)深入了解 [適用於 Linux 的 AzCopy](../common/storage-use-azcopy-linux.md) 和[適用於 Windows 的 AzCopy](../common/storage-use-azcopy.md)。 AzCopy 必須安裝在本機上。 AzCopy 無法在 Cloud Shell 中使用。 

## <a name="create-and-manage-share-snapshots"></a>建立及管理共用快照集
可使用 Azure 檔案共用來執行的另一項實用工作是建立共用快照集。 快照集會保留 Azure 檔案共用的時間點複本。 共用快照集類似於您可能已經很熟悉的一些作業系統技術：

- Linux 系統的[邏輯磁碟區管理員 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照集
- macOS 的 [Apple 檔案系統 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照集
- Windows 檔案系統的[磁碟區陰影複製服務 (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)，例如 NTFS 和 ReFS。您可以使用 [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) 命令建立共用快照集：

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>瀏覽共用快照集內容
您可以將我們在 `$SNAPSHOT` 變數中擷取的共用快照集時間戳記傳遞至 `az storage file list` 命令，以瀏覽共用快照集的內容：

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>列出共用快照集
若要檢視您為共用建立的快照集清單，請使用下列命令：

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>從共用快照集還原
您可以使用您先前使用的 `az storage file copy start` 命令來還原檔案。 首先，刪除您上傳的 SampleUpload.txt 檔案，以便您可以從快照集將其還原：

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')
 URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT
 # Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>刪除共用快照集
您可以使用 [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) 命令來刪除共用快照集。 使用將 `$SNAPSHOT` 參考納入 `--snapshot` 參數的變數：

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>清除資源
完成作業後，您可以使用 [`az group delete`](/cli/azure/group#delete) 命令來移除資源群組和所有相關資源： 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

或者，您也可以個別移除資源。
- 移除您為本文建立的 Azure 檔案共用：

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- 移除儲存體帳戶本身。 (這會完全移除您建立的 Azure 檔案共用和其他可能已建立的儲存體資源，例如 Azure Blob 儲存體容器。)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [什麼是 Azure 檔案服務？](storage-files-introduction.md)
