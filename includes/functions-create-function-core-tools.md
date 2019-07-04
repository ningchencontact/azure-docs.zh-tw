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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173758"
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
