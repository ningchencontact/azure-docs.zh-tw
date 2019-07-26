---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444099"
---
## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

函式會使用 Azure 儲存體中的一般用途帳戶來維護函式的狀態和其他資訊。 在使用 [az storage account create](/cli/azure/storage/account) 命令所建立的資源群組中建立一般用途的儲存體帳戶。

在下列命令中，使用全域唯一儲存體帳戶名稱來替代您看見 `<storage_name>` 預留位置的地方。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
