---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129087"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

`extensionBundle`提供下列屬性：

| 屬性 | 描述 |
| -------- | ----------- |
| id | Microsoft Azure 函式擴充功能配套的命名空間。 |
| 版本 | 要安裝的配套版本。 函數執行時間一律會挑選版本範圍或間隔所定義的最大允許版本。 上述版本值允許所有來自1.0.0 的套件組合版本，但不包括2.0.0。 如需詳細資訊，請參閱[指定版本範圍的間隔標記法](/nuget/reference/package-versioning#version-ranges-and-wildcards)。 |

配套版本隨著套件在配套中的增加而變更。 當配套中的套件以主要版本遞增時，就會發生主要版本變更。 套件組合中的主要版本變更通常會與函式執行時間的主要版本變更一致。  

預設配套所安裝的目前延伸模組集合會在此[副檔名 json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)檔案中列舉。
