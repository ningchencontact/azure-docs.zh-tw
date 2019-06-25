---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: da0cbab59d9c801419ac4b3704fee3275f337fd9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174133"
---
指定[計算 Application Insights 的計量](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator)時彙總多少函式引動過程。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|屬性 |預設值  | 描述 |
|---------|---------|---------| 
|batchSize|1000|要彙總的要求數目上限。| 
|flushTimeout|00:00:30|要彙總的最長期間。| 

達到兩個限制的第一個限制時，會彙總函式引動過程。