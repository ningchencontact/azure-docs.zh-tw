---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710520"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的組態設定。

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定，函式應用程式的預設工作中樞名稱是 **DurableFunctionsHub**。 如需詳細資訊，請參閱[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)。

|屬性  |預設值 | 描述 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|替代[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)名稱可用來彼此隔離多個 Durable Functions 應用程式，即使它們使用相同的儲存體後端。|
|controlQueueBatchSize|32|要從控制佇列中一次提取的訊息數。|
|partitionCount |4|控制佇列的資料分割計數。 必須是介於 1 到 16 之間的正整數。|
|controlQueueVisibilityTimeout |5 分鐘|已從控制佇列中清除之訊息的可見度逾時。|
|workItemQueueVisibilityTimeout |5 分鐘|已從工作項目佇列中清除之訊息的可見度逾時。|
|maxConcurrentActivityFunctions |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的活動函式數目上限。|
|maxConcurrentOrchestratorFunctions |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的協調器函式數目上限。|
|maxQueuePollingInterval|30 秒|最大的控制和工作项队列轮询时间间隔，采用 *hh:mm:ss* 格式。 值越高，可能导致的消息处理延迟也越高。 值越低，可能导致的存储成本会越高，因为存储事务数增高。|
|azureStorageConnectionStringName |AzureWebJobsStorage|具有 Azure 儲存體連接字串的應用程式設定名稱，而該連接字串用來管理基礎的 Azure 儲存體資源。|
|trackingStoreConnectionStringName||连接字符串的名称，用于“历史记录”和“实例”表。 如果未指定，则使用 `azureStorageConnectionStringName` 连接。|
|trackingStoreNamePrefix||指定 `trackingStoreConnectionStringName` 时用于“历史记录”和“实例”表的前缀。 如果未设置，则默认前缀值为 `DurableTask`。 如果 `trackingStoreConnectionStringName` 未指定，则“历史记录”和“实例”表会使用 `hubName` 值作为其前缀，`trackingStoreNamePrefix` 的任何设置都会被忽略。|
|traceInputsAndOutputs |false|此值指出是否要追蹤函式呼叫的輸入和輸出。 追蹤函式執行事件時的預設行為就是在函式呼叫的序列化輸入和輸出中包含位元組數目。 此行为提供的有关输入和输出情况的信息是最少的，不会导致日志膨胀，也不会无意中将敏感信息公开。 將此屬性設定為 true，會導致預設函式記錄功能記錄函式輸入和輸出的整個內容。|
|logReplayEvents|false|此值可指出是否要將協調流程重新執行事件寫入 Application Insights。|
|eventGridTopicEndpoint ||Azure 事件方格自訂主題端點的 URL。 當設定這個屬性時，就會將協調流程生命週期通知事件發佈到此端點。 這個屬性支援應用程式設定解析。|
|eventGridKeySettingName ||應用程式設定的名稱，其中包含在 `EventGridTopicEndpoint` 用來向 Azure 事件方格自訂主題進行驗證的金鑰。|
|eventGridPublishRetryCount|0|如果發佈到 Event Grid 主題失敗，重試的次數。|
|eventGridPublishRetryInterval|5 分鐘|「事件方格」會以 *hh:mm:ss* 格式發佈重試間隔。|
|eventGridPublishEventTypes||若要發佈至 Event Grid 的事件類型的清單。 如果未指定，就會發行所有事件類型。 允許的值包括`Started`， `Completed`， `Failed`， `Terminated`。|

许多此类设置用于优化性能。 如需詳細資訊，請參閱[效能和級別](../articles/azure-functions/durable-functions-perf-and-scale.md)。