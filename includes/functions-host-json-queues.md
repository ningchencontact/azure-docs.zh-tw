---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7139b88ee9ef137ca28484538262b8bb2fe804db
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134127"
---
```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|屬性  |預設值 | 說明 |
|---------|---------|---------| 
|maxPollingInterval|60000|佇列輪詢之間的間隔上限 (毫秒)。| 
|visibilityTimeout|0|處理訊息失敗時，重試之間的時間間隔。| 
|batchSize|16|Functions 執行階段會同時擷取，並以平行方式處理的佇列訊息數目。 當要處理的數目減少到 `newBatchThreshold` 時，執行階段就會取得另一個批次，並開始處理那些訊息。 因此，每個函式並行處理之訊息的上限為 `batchSize` 加上 `newBatchThreshold`。 這項限制個別套用至每個佇列觸發的函式。 <br><br>如果您需要避免平行執行在單一佇列上收到的訊息，可以將 `batchSize` 設定為 1。 不過，只要您的函式應用程式在單一虛擬機器 (VM) 上執行，這項設定就只會將並行排除。 如果函式應用程式相應放大為多個 VM，則每個 VM 可以執行每個佇列觸發之函式的一個執行個體。<br><br>最大值 `batchSize` 為 32。 | 
|maxDequeueCount|5|將訊息移至有害佇列之前，嘗試處理訊息的次數。| 
|newBatchThreshold|batchSize/2|每當要同時處理的訊息數目下降至這個數字時，執行階段就會擷取另一個批次。| 



