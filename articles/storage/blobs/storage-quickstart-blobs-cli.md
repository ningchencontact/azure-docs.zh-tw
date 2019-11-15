---
title: Azure 快速入門 - 使用 Azure CLI 在物件儲存體中建立 Blob | Microsoft Docs
description: 在本快速入門中，您將了解如何使用 Azure CLI 將 Blob 上傳至 Azure 儲存體、下載 Blob，以及列出容器中的 Blob。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747944"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>快速入門：使用 Azure CLI 上傳、下載及列出 Blob

Azure CLI 是管理 Azure 資源的 Azure 命令列體驗。 您可以在瀏覽器中使用它搭配 Azure Cloud Shell。 您也可以將它安裝在 macOS、Linux 或 Windows 上，並從命令列執行。 在本快速入門中，您會了解如何使用 Azure CLI 從 Azure Blob 儲存體上傳和下載資料。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 來判斷您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>建立容器

Blob 一律會上傳到容器中。 您可以組織 Blob 群組，方式如同在電腦的資料夾中組織檔案。

使用 [az storage container create](/cli/azure/storage/container) 命令，建立用於儲存 Blob 的容器。

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>上傳 Blob

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 本快速入門中的範例示範如何使用區塊 Blob。

首先，建立一個要上傳至區塊 Blob 的檔案。 如果您使用的是 Azure Cloud Shell，請使用下列命令建立檔案：

```bash
vi helloworld
```

當檔案開啟時，請按 **insert**。 輸入 *Hello world*，然後按 **Esc**。接著，輸入 *:x*，然後按 **Enter**。

在此範例中，您要將 Blob 上傳到使用 [az storage blob upload](/cli/azure/storage/blob) 命令最後一個步驟所建立的容器。 您不需要指定檔案路徑，因為檔案是在根目錄建立的：

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

如果 Blob 不存在，此作業會建立 Blob，若已存在，則會加以覆寫。 請上傳您所需的檔案數量，再繼續進行。

若要同時上傳多個檔案，您可以使用 [az storage blob upload-batch](/cli/azure/storage/blob) 命令。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [az storage blob list](/cli/azure/storage/blob) 命令列出容器中的 Blob。

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>下載 Blob

使用 [az storage blob download](/cli/azure/storage/blob) 命令下載稍早所上傳的 Blob。

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>使用 AzCopy 進行資料轉送

[AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 公用程式是適用於 Azure 儲存體的高效能可編寫指令碼資料轉送的另一個選項。 您可以使用 AzCopy 在 Blob、檔案和表格儲存體之間傳輸資料。

下列範例會使用 AzCopy，將名為 *myfile.txt* 的檔案上傳至 *sample-container* 容器。 請記得以您自己的值取代角括號中的預留位置值：

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包括在本快速入門中所建立的儲存體帳戶)，請使用 [az group delete](/cli/azure/group) 命令刪除資源群組。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已經了解如何在本機檔案系統和 Azure Blob 儲存體中的容器之間傳輸檔案。 若要深入了解在 Azure 儲存體中使用 Blob，請繼續進行使用 Azure Blob 儲存體的教學課程。

> [!div class="nextstepaction"]
> [操作說明：使用 Azure CLI 的 Blob 儲存體作業](storage-how-to-use-blobs-cli.md)
