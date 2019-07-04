---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173752"
---
## <a name="update-the-function"></a>更新函式

根據預設，此範本建立的函式在發出要求時需要函式金鑰。 為了更輕鬆地在 Azure 中測試函式，您需要更新函式以允許匿名存取。 您進行此變更的方法，視您的函式專案語言而有所不同。

### <a name="c"></a>C\#

開啟新函式的 MyHttpTrigger.cs 程式碼檔案，將函式定義中 **AuthorizationLevel** 屬性的值更新為 `Anonymous` 並儲存變更。

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

在文字編輯器中開啟新函式的 function.json 檔案、將 **bindings** 中的 **authLevel** 屬性更新為 `anonymous`，然後儲存變更。

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

您現在可以在 Azure 中呼叫該函式，而不需要提供函式金鑰。 在本機執行時一律不需要函式金鑰。
