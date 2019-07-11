---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442260"
---
安裝繫結擴充功能最簡單的方式是啟用[擴充功能套件組合](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 套件組合已啟用，一組預先定義的擴充功能套件會自動安裝。

若要啟用擴充功能套件組合，請開啟 *host.json* 檔案，並更新其內容以符合下列程式碼：

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```