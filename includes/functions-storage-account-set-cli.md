---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329604"
---
### <a name="set-the-storage-account-connection"></a>設定儲存體帳戶連線

開啟 local.settings.json 檔案並複製 `AzureWebJobsStorage` 的值，這是儲存體帳戶的連接字串。 請使用下列 Bash 命令，將 `AZURE_STORAGE_CONNECTION_STRING` 環境變數設為此連接字串：

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

在 `AZURE_STORAGE_CONNECTION_STRING` 環境變數中設定連接字串時，您將可直接存取您的儲存體帳戶，而不需要每次都提供驗證。