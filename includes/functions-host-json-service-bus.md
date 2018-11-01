---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133061"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|訊息幫浦應該起始之回呼的並行呼叫數上限。 Functions 執行階段預設會並行處理多個訊息。 若要指示執行階段一次只處理一個佇列或主題訊息，請將 `maxConcurrentCalls` 設定為 1。 | 
|prefetchCount|n/a|基礎 MessageReceiver 將使用的預設 PrefetchCount。| 
|autoRenewTimeout|00:05:00|將自動更新訊息鎖定的最大持續時間。| 
