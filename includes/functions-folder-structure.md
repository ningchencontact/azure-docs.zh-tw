---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ea7396117935c13698a8c6cc6ef4029cc82b90bc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279348"
---
特定函式應用程式中所有函式的程式碼，都位於包含主機設定檔和一或多個子資料夾的根專案資料夾中。 每個子資料夾都包含個別函式的程式碼。 資料夾結構會顯示在下列標記法中：

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

在2.x 版和更高版本的函式執行時間中，函數應用程式中的所有函式都必須共用相同的語言堆疊。  

[主機. json](../articles/azure-functions/functions-host-json.md)檔案包含執行時間專屬的設定，而且位於函式應用程式的根資料夾中。 *Bin*資料夾包含函數應用程式所需的套件和其他程式庫檔案。 請參閱函式應用程式專案以了解特定語言的需求：

* [C# 類別庫 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 指令碼 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 指令碼](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
