---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173773"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|屬性  |預設值 | 描述 |
|---------|---------|---------| 
|maxBatchSize|64|每個接收迴圈接收到的事件計數上限。|
|prefetchCount|n/a|基礎 EventProcessorHost 將使用的預設 PrefetchCount。| 
|batchCheckpointFrequency|1|要在建立 EventHub 資料指標檢查點之前處理的事件批次數目。| 
