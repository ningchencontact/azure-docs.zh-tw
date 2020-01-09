---
title: 建立 Azure 檔案共用
titleSuffix: Azure Files
description: 如何使用 Azure 入口網站、PowerShell 或 Azure CLI 建立 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452882"
---
# <a name="create-a-file-share"></a>建立檔案共用
您可以使用 [Azure 入口網站](https://portal.azure.com/)、Azure 儲存體 PowerShell Cmdlet、Azure 儲存體用戶端程式庫或 Azure 儲存體 REST API，在 Azure 檔案儲存體中建立共用。 在本文中，您將學會如何：
- 使用 Azure 入口網站建立 Azure 檔案共用
- 使用 PowerShell 建立 Azure 檔案共用
- 使用 Azure CLI 建立 Azure 檔案共用

## <a name="prerequisites"></a>必要條件
若要建立 Azure 檔案共用，您可以使用已存在的儲存體帳戶，或[建立新的 azure 儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要使用 PowerShell 建立 Azure 檔案共用，您需要儲存體帳戶的帳戶金鑰和名稱。 如果您打算使用 PowerShell 或 CLI，您將需要儲存體帳戶金鑰。

> [!NOTE]
> 如果您想要建立大於 5 TiB 的檔案共用，請參閱[啟用大型檔案共用](storage-files-how-to-create-large-file-share.md)。

## <a name="create-a-file-share-through-the-azure-portal"></a>透過 Azure 入口網站建立檔案共用

1. 移至 Azure 入口網站上的 **儲存體帳戶** 窗格。
    ![儲存體帳戶 窗格](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. 選取 [ **+ 檔案共用**] 按鈕。
    ![[新增檔案共用] 按鈕](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. 輸入您的 [**名稱**] 和 [**配額**] 資訊。
    ![新檔案共用的名稱和配額](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. 查看您的新檔案共用。
    ![UI 中的新檔案共用](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. 上傳檔案。
    ![巡覽列中的 [上傳] 按鈕](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. 流覽至您的檔案共用，並管理您的目錄和檔案。
    檔案共用的 ![資料夾檢視](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>透過 PowerShell 建立檔案共用

下載並安裝 Azure PowerShell Cmdlet。 如需安裝點和安裝指示的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 。

> [!Note]  
> 我們建議您下載並安裝或升級至最新的 Azure PowerShell 模組。

1. 建立新的儲存體帳戶。
    儲存體帳戶是共用的儲存體集區，您可以在其中部署 Azure 檔案共用和儲存體資源（例如 blob 或佇列）。

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. 建立新的檔案共用。        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> 您的檔案共用名稱必須是全部小寫。 如需有關為檔案共用和檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案和中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="create-a-file-share-through-the-cli"></a>透過 CLI 建立檔案共用

1. 下載並安裝 Azure CLI。
    請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)並[開始使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。

1. 建立您要在其中建立共用之儲存體帳戶的連接字串。
    使用下列範例中的儲存體帳戶名稱和資源群組來取代 ```<storage-account>``` 和 ```<resource_group>``` ：

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. 建立檔案共用。
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>後續步驟

* [連線並在 Windows 上掛接檔案共用](storage-how-to-use-files-windows.md)
* [連線並在 Linux 上掛接檔案共用](storage-how-to-use-files-linux.md)
* [連線並在 macOS 上掛接檔案共用](storage-how-to-use-files-mac.md)

請參閱這些連結，以取得 Azure 檔案服務的詳細資訊：

* [儲存體檔案常見問題](storage-files-faq.md)
* [針對 Windows 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Linux 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
