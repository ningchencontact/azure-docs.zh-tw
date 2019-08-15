---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 83f3def8f4972f35df25dc98f4b818e703801d2e
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949959"
---
## <a name="create-a-function"></a>建立函式

下列命令會建立名為 `MyHttpTrigger` 的 HTTP 觸發函式。

```command
func new --name MyHttpTrigger --template "HttpTrigger"
```

當命令執行時，您會看到如下輸出：

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
