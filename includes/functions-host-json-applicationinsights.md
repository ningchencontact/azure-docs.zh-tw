---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3fd610512a87263eeb6599a24faef5ba23070823
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608112"
---
控制 [Application Insights 中的取樣功能](../articles/azure-functions/functions-monitoring.md#configure-sampling)。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|isEnabled|true|啟用或停用取樣。| 
|maxTelemetryItemsPerSecond|5|取樣的開始臨界值。| 