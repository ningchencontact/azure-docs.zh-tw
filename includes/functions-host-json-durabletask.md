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
ms.openlocfilehash: 92eb13165326f44432f09322ea97f3cee5ccec2b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251238"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的組態設定。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定，函式應用程式的預設工作中樞名稱是 **DurableFunctionsHub**。 如需詳細資訊，請參閱[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)。

|屬性  |預設值 | 說明 |
|---------|---------|---------|
|HubName|DurableFunctionsHub|替代[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)名稱可用來將多個 Durable Functions 應用程式彼此隔離，即使它們使用相同的儲存體後端。|
|ControlQueueBatchSize|32|要從控制佇列中一次提取的訊息數。|
|PartitionCount |4|控制佇列的資料分割計數。 必須是介於 1 到 16 之間的正整數。|
|ControlQueueVisibilityTimeout |5 分鐘|已從控制佇列中清除之訊息的可見度逾時。|
|WorkItemQueueVisibilityTimeout |5 分鐘|已從工作項目佇列中清除之訊息的可見度逾時。|
|MaxConcurrentActivityFunctions |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的活動函式數目上限。|
|MaxConcurrentOrchestratorFunctions |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的協調器函式數目上限。|
|AzureStorageConnectionStringName |AzureWebJobsStorage|具有 Azure 儲存體連接字串的應用程式設定名稱，而該連接字串用來管理基礎的 Azure 儲存體資源。|
|TraceInputsAndOutputs |false|此值指出是否要追蹤函式呼叫的輸入和輸出。 追蹤函式執行事件時的預設行為就是在函式呼叫的序列化輸入和輸出中包含位元組數目。 這可提供輸入和輸出的最基本資訊，而不會讓記錄變大，或者不小心在記錄中公開敏感性資訊。 將此屬性設定為 true，會導致預設函式記錄功能記錄函式輸入和輸出的整個內容。|
|LogReplayEvents|false|此值可指出是否要將協調流程重新執行事件寫入 Application Insights。|
|EventGridTopicEndpoint ||Azure 事件方格自訂主題端點的 URL。 若已設定這個屬性，協調流程生命週期通知事件就會發佈到此端點。 這個屬性支援應用程式設定解析。|
|EventGridKeySettingName ||應用程式設定的名稱，其中包含在 `EventGridTopicEndpoint` 用來向 Azure 事件方格自訂主題進行驗證的金鑰。|
|EventGridPublishRetryCount|0|如果發佈到 Event Grid 主題失敗，重試的次數。|
|EventGridPublishRetryInterval|5 分鐘|「事件方格」會以 *hh:mm:ss* 格式發佈重試間隔。|

上述許多屬性適用於將效能最佳化。 如需詳細資訊，請參閱[效能和級別](../articles/azure-functions/durable-functions-perf-and-scale.md)。