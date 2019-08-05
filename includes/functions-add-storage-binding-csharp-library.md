---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592787"
---
在 C# 類別庫專案中，繫結會被定義為函式方法上的繫結屬性。 系統接著會根據這些屬性自動產生 function.json  檔案。

開啟 HttpTrigger.cs  專案檔案，並新增下列 `using` 陳述式：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

將下列參數加入 `Run` 方法定義：

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` 參數是 `ICollector<T>` 類型，其代表會在函式完成時寫入輸出繫結的訊息集合。 在此情況下，輸出是名為 `outqueue` 的儲存體佇列。 儲存體帳戶的連接字串是由 `StorageAccountAttribute` 設定。 此屬性表示包含儲存體帳戶連接字串的設定，並可以被套用至類別、方法或參數層級。 在此情況下，您可以省略 `StorageAccountAttribute`，因為您已經正在使用預設的儲存體帳戶。

Run 方法定義現在應該如下所示︰  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
