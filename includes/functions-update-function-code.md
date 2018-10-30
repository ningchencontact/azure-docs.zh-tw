---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987957"
---
## <a name="update-the-function"></a>更新函式

根據預設，此範本建立的函式在發出要求時需要函式金鑰。 為了更輕鬆地在 Azure 中測試函式，您需要更新函式以允許匿名存取。 您進行此變更的方法，視您的函式專案語言而有所不同。

### <a name="c"></a>C\#

開啟新函式的 MyHttpTrigger.cs 程式碼檔案，將函式定義中 **AuthorizationLevel** 屬性的值更新為 `anonymous` 並儲存變更。

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

開啟新的函式的 function.json 檔案 (在文字編輯器中開啟)、將 **bindings.httpTrigger** 中的 **authLevel** 屬性更新為 `anonymous` 並儲存變更。

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
