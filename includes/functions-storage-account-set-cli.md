---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839055"
---
開啟 local.settings.json 檔案並複製 `AzureWebJobsStorage` 的值，這是儲存體帳戶的連接字串。 請使用下列 Bash 命令，將 `AZURE_STORAGE_CONNECTION_STRING` 環境變數設為此連接字串：

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

在 `AZURE_STORAGE_CONNECTION_STRING` 環境變數中設定連接字串時，您將可直接存取您的儲存體帳戶，而不需要每次都提供驗證。