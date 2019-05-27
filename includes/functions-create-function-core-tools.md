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
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132087"
---
## <a name="create-a-function"></a>建立函式

下列命令會建立名為 `MyHttpTrigger` 的 HTTP 觸發函式。

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

當命令執行時，您會看到如下輸出：

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
