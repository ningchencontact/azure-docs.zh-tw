---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2808264b4641bda49a53677ebe216a3b53b7d0d9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293764"
---
特定函式應用程式中所有函式的程式碼，都位於包含主機設定檔和一或多個子資料夾的根專案資料夾中。 每個子資料夾都包含個別函式的程式碼，如以下表示：

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

在 2.x 版的 Functions 執行階段中，函式應用程式中所有的應用程式必須共用相同的語言工作者處理序。  

[host.json](../articles/azure-functions/functions-host-json.md) 檔案包含一些執行階段特定的組態，並在函數應用程式的根資料夾中。 `bin` 資料夾包含函式應用程式需要的封裝和其他程式庫檔。 請參閱函式應用程式專案以了解特定語言的需求：

* [C# 類別庫 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 指令碼 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 指令碼](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



