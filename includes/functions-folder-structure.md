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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594549"
---
特定函式應用程式中所有函式的程式碼，都位於包含主機設定檔和一或多個子資料夾的根專案資料夾中。 每個子資料夾都包含個別函數的程式碼。 下列的表示法，顯示資料夾結構：

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

在版本 2.x 的 Functions 執行階段，函式應用程式中的所有函式必須共用相同的語言堆疊。  

[Host.json](../articles/azure-functions/functions-host-json.md)檔案包含執行階段特定組態，並在函數應用程式根資料夾中。 A *bin*資料夾包含封裝和其他函式應用程式所需的程式庫檔案。 請參閱函式應用程式專案以了解特定語言的需求：

* [C# 類別庫 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 指令碼 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 指令碼](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



