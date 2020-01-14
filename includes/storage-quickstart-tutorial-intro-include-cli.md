---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467464"
---
## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

使用 [az storage account create](/cli/azure/storage/account) 命令建立一般用途的儲存體帳戶。 一般用途的儲存體帳戶可以用於所有四個服務：Blob、檔案、資料表和佇列。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>指定儲存體帳戶認證

對於本教學課程中的大部分命令，Azure CLI 需要您的儲存體帳戶認證。 儘管有幾個選項可以這麼做，其中一種最輕鬆的方式就是設定 `AZURE_STORAGE_ACCOUNT` 和 `AZURE_STORAGE_KEY` 環境變數。

> [!NOTE]
> 此文章說明如何使用 Bash 設定環境變數。 其他環境可能需要修改語法。

首先，使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令顯示您的儲存體帳戶金鑰。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

現在，請設定 `AZURE_STORAGE_ACCOUNT`和`AZURE_STORAGE_KEY` 環境變數。 您可以在 Bash 殼層中使用 `export` 命令來完成。 請記得以您自己的值取代角括號中的預留位置值：

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

如需有關如何使用 Azure 入口網站擷取帳戶存取金鑰的詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../articles/storage/common/storage-account-keys-manage.md)。