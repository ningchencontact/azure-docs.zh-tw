---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d62da82b4a4dd35532dd8776a9111689db469201
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448373"
---
## <a name="register-extensions"></a>註冊延伸模組

除了 HTTP 和計時器觸發程序函式繫結在執行階段版本 2.x 會實作為延伸模組套件。 在版本中的 Azure Functions 執行階段 2.x，您必須明確註冊您的函式中使用的繫結類型的擴充功能。 此狀況的例外是 HTTP 繫結和計時器觸發程序，不需要延伸模組。

您可以選擇安裝個別的繫結延伸模組，或者您可以加入 host.json 專案檔案的延伸模組套件組合參考。 延伸模組套件組合中移除的機會便封裝相容性問題時使用多個繫結類型。 它是建議的方法，以註冊繫結延伸模組。 延伸模組套件組合也就不需要安裝.NET Core 2.x SDK。 

### <a name="extension-bundles"></a>延伸模組套件組合

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

若要進一步了解，請參閱[註冊 Azure Functions 繫結延伸模組](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 您應該先 functions.json 檔案中加入繫結加入 host.json 延伸模組套件組合。

### <a name="register-individual-extensions"></a>註冊個別的擴充功能

如果您需要安裝擴充功能不在組合中，您可以手動註冊特定的繫結的個別擴充功能封裝。 

> [!NOTE]
> 若要使用手動註冊擴充功能`func extensions install`，您必須有.NET Core 2.x 安裝 SDK。

在更新 function.json  檔案以包含函式所需的所有繫結後，請在專案資料夾中執行下列命令。

```bash
func extensions install
```

此命令會讀取 function.json  檔案，查看您需要哪些套件，加以安裝，然後重建擴充專案。 它會在目前版本中新增任何新繫結，但不會更新現有的繫結。 在安裝新的繫結時，使用 `--force` 選項將現有繫結更新為最新版本。