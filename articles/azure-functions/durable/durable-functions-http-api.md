---
title: 長期函式中的 HTTP API - Azure
description: 了解如何在 Azure Functions 的「長期函式」延伸模組中實作 HTTP API。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 5bd977826f489ca8452432babe6126b8553450fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730702"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>長期函式中的 HTTP API (Azure Functions)

「長期工作」延伸模組會公開一組 HTTP API，可以用來執行下列工作：

* 擷取協調流程執行個體的狀態。
* 傳送事件給等待中協調流程執行個體。
* 終止執行中協調流程執行個體。

每個 HTTP API 都是 Webhook 作業，由「長期工作」擴充功能直接處理。 它們並非函式應用程式中任何函式的特定項目。

> [!NOTE]
> 這些作業也可以透過使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別上的執行個體管理 API 來直接叫用。 如需詳細資訊，請參閱[執行個體管理](durable-functions-instance-management.md)。

## <a name="http-api-url-discovery"></a>HTTP API URL 探索

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別會公開 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API，它可以用來產生 HTTP 回應裝載，其中包含所有支援作業的連結。 以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

這些範例函式會產生下列 JSON 回應資料。 所有欄位的資料類型是 `string`。

| 欄位                   |描述                           |
|-------------------------|--------------------------------------|
| **`id`**                |協調流程執行個體的識別碼。 |
| **`statusQueryGetUri`** |協調流程執行個體的狀態 URL。 |
| **`sendEventPostUri`**  |協調流程執行個體的「引發事件」URL。 |
| **`terminatePostUri`**  |協調流程執行個體的「終止」URL。 |
| **`rewindPostUri`**     |協調流程執行個體的「倒轉」URL。 |

以下是範例回應：

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Webhook URL 的格式依據您執行的 Azure Functions 主機版本，可能會有所不同。 上述範例適用於 Azure Functions 2.x 主機。

## <a name="async-operation-tracking"></a>非同步作業追蹤

先前所述之 HTTP 回應的設計目的，是協助搭配長期函式實作長時間執行 HTTP 非同步 API。 有時候稱為「輪詢取用者模式」。 用戶端/伺服器流量運作方式如下：

1. 用戶端會發出 HTTP 要求，以啟動長時間執行處理序，例如協調器函式。
2. 目標 HTTP 觸發程序會傳回 HTTP 202 回應，具有 `Location` 標頭和 `statusQueryGetUri` 值。
3. 用戶端會輪詢 `Location` 標頭中的 URL。 它會繼續查看具有 `Location` 標頭的 HTTP 202 回應。
4. 當執行個體完成 (或失敗) 時，`Location` 標頭中的端點會傳回 HTTP 200。

此通訊協定可以協調長時間執行處理序與外部用戶端或服務，它們支援輪詢 HTTP 端點，並且遵循 `Location` 標頭。 基本部分已內建至長期函式 HTTP API。

> [!NOTE]
> 根據預設，[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) 提供的所有 HTTP 型動作皆支援標準的非同步作業模式。 這項功能可以嵌入長期執行長期函式，作為 Logic Apps 工作流程的一部分。 Logic Apps 支援非同步 HTTP 模式的詳細資訊，可以在 [Azure Logic Apps 工作流程動作和觸發程序文件](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)中找到。

## <a name="http-api-reference"></a>HTTP API 參考

延伸模組實作的所有 HTTP API 會採用下列參數。 所有參數的資料類型是 `string`。

| 參數        | 參數類型  | 描述 |
|------------------|-----------------|-------------|
| **`taskHub`**    | 查詢字串    | [工作中樞](durable-functions-task-hubs.md)的名稱。 如果未指定，則會假設為目前函式應用程式的工作中樞名稱。 |
| **`connection`** | 查詢字串    | 儲存體帳戶之連接字串的**名稱**。 如果未指定，則會假設為函式應用程式的預設連接字串。 |
| **`systemKey`**  | 查詢字串    | 叫用 API 所需的授權金鑰。 |

`systemKey` 是由 Azure Functions 主機自動產生的授權金鑰。 它特別授與「長期工作」延伸模組 API 的存取權，並且可以用與[其他授權金鑰](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)相同的方式來管理。 探索 `systemKey` 值的最簡單方式是使用先前所述的 `CreateCheckStatusResponse` API。

接下來的幾個章節會涵蓋延伸模組支援的特定 HTTP API，並且提供如何使用的範例。

### <a name="get-instance-status"></a>取得執行個體狀態

取得指定協調流程執行個體的狀態。

#### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                   | 參數類型  | 描述 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 協調流程執行個體的識別碼。 |
| **`showInput`**         | 查詢字串    | 選擇性參數。 如果设置为 `false`，则函数输入不会包含在响应有效负载中。|
| **`showHistory`**       | 查詢字串    | 選擇性參數。 如果設定為 `true`，則協調流程執行歷程記錄將會包含在回應承載中。|
| **`showHistoryOutput`** | 查詢字串    | 選擇性參數。 如果设置为 `true`，函数输出将包含在业务流程执行历史记录中。|
| **`createdTimeFrom`**   | 查詢字串    | 選擇性參數。 指定后，将筛选在给定 ISO8601 时间戳当时或之后创建的返回实例列表。|
| **`createdTimeTo`**     | 查詢字串    | 選擇性參數。 指定后，将筛选在给定 ISO8601 时间戳当时或之前创建的返回实例列表。|
| **`runtimeStatus`**     | 查詢字串    | 選擇性參數。 指定時，會根據所傳回執行個體的執行階段狀態來篩選所傳回執行個體的清單。 若要查看可能的執行階段狀態值清單，請參閱[查詢執行個體](durable-functions-instance-management.md)主題。 |

#### <a name="response"></a>Response

可以傳回幾個可能的狀態字碼值。

* **HTTP 200 (確定)**：指定的執行個體處於已完成狀態。
* **HTTP 202 (已接受)**：指定的執行個體正在進行中。
* **HTTP 400 (不正確的要求)**：指定的執行個體失敗或終止。
* **HTTP 404 (找不到)**：指定的執行個體不存在或尚未開始執行。
* **HTTP 500 (內部伺服器錯誤)**：指定的執行個體失敗且發生未處理的例外狀況。

**HTTP 200** 和 **HTTP 202** 案例的回應承載是 JSON 物件，具有下列欄位：

| 欄位                 | 資料類型 | 描述 |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | 執行個體的執行階段狀態。 值包括 [執行中]、[擱置]、[失敗]、[已取消]、[終止]、[已完成]。 |
| **`input`**           | JSON      | JSON 資料，用來初始化執行個體。 這個欄位為 `null`，如果 `showInput` 查詢字串參數設定為 `false`。|
| **`customStatus`**    | JSON      | 用於自訂協調流程狀態的 JSON 資料。 如果未設定，欄位會是 `null`。 |
| **`output`**          | JSON      | 執行個體的 JSON 輸出。 如果執行個體不是已完成狀態，則這個欄位是 `null`。 |
| **`createdTime`**     | string    | 執行個體建立的時間。 使用 ISO 8601 延伸標記法。 |
| **`lastUpdatedTime`** | string    | 執行個體保存的時間。 使用 ISO 8601 延伸標記法。 |
| **`historyEvents`**   | JSON      | 包含協調流程執行歷程記錄的 JSON 陣列。 這個欄位為 `null`，除非 `showHistory` 查詢字串參數設定為 `true`。 |

以下是範例回應承載，其中包含協調流程執行歷程記錄和活動輸出 (針對可讀性格式化)：

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** 回應也包含**位置**回應標頭，它參考與先前所述 `statusQueryGetUri` 欄位相同的 URL。

### <a name="get-all-instances-status"></a>取得所有執行個體狀態

此外，可以通过从“获取实例状态”请求中删除 `instanceId`，来查询所有实例的状态。 在这种情况下，基本参数与“获取实例状态”相同。 也支持使用查询字符串参数进行筛选。

請記住一點，`connection` 和 `code` 是選擇性的。 如果您對函式使用匿名驗證，則不需要程式碼。
如果你不想要使用 AzureWebJobsStorage 应用设置中未定义的其他存储连接字符串，则可以安全地忽略连接查询字符串参数。

#### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                   | 參數類型  | 描述 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 協調流程執行個體的識別碼。 |
| **`showInput`**         | 查詢字串    | 選擇性參數。 如果设置为 `false`，则函数输入不会包含在响应有效负载中。|
| **`showHistory`**       | 查詢字串    | 選擇性參數。 如果設定為 `true`，則協調流程執行歷程記錄將會包含在回應承載中。|
| **`showHistoryOutput`** | 查詢字串    | 選擇性參數。 如果设置为 `true`，函数输出将包含在业务流程执行历史记录中。|
| **`createdTimeFrom`**   | 查詢字串    | 選擇性參數。 指定后，将筛选在给定 ISO8601 时间戳当时或之后创建的返回实例列表。|
| **`createdTimeTo`**     | 查詢字串    | 選擇性參數。 指定后，将筛选在给定 ISO8601 时间戳当时或之前创建的返回实例列表。|
| **`runtimeStatus`**     | 查詢字串    | 選擇性參數。 指定時，會根據所傳回執行個體的執行階段狀態來篩選所傳回執行個體的清單。 若要查看可能的執行階段狀態值清單，請參閱[查詢執行個體](durable-functions-instance-management.md)主題。 |
| **`top`**               | 查詢字串    | 選擇性參數。 如果指定，则会限制查询返回的实例数。 |

#### <a name="response"></a>Response

以下是回應承載範例，包括協調流程狀態 (針對可讀性格式化)：

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> 如果執行個體資料表中有大量資料列，則這項作業可能會耗用非常大量 Azure 儲存體 I/O。 在 [Durable Functions (Azure Functions) 中的效能和級別](durable-functions-perf-and-scale.md#instances-table)文件中可以找到執行個體資料表的更多詳細資料。
>

如果存在更多结果，则会在响应标头中返回继续标记。  標頭的名稱為 `x-ms-continuation-token`。

如果在下一个请求标头中设置了继续标记值，则可以获取下一页结果。 请求标头的此名称也是 `x-ms-continuation-token`。

### <a name="purge-single-instance-history"></a>清除单个实例的历史记录

删除指定业务流程实例的历史记录和相关项目。

#### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |

#### <a name="response"></a>Response

可以返回以下 HTTP 状态代码值。

* **HTTP 200 (確定)**：已成功清除实例历史记录。
* **HTTP 404 (找不到)**：指定的实例不存在。

值为 **HTTP 200** 时的响应有效负载是包含以下字段的 JSON 对象：

| 欄位                  | 資料類型 | 描述 |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | 删除的实例数。 对于单个实例，此值应始终为 `1`。 |

以下是範例回應裝載 (針對可讀性格式化)：

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>清除多个实例的历史记录

也可以通过删除“清除单个实例的历史记录”请求中的 `{instanceId}`，来删除任务中心内多个实例的历史记录和相关项目。 若要有选择地清除实例历史记录，请使用“获取所有实例状态”请求中所述的相同筛选器。

#### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位                 | 參數類型  | 描述 |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | 查詢字串    | 選擇性參數。 指定后，将筛选在给定 ISO8601 时间戳当时或之后创建的已清除实例列表。|
| **`createdTimeTo`**   | 查詢字串    | 選擇性參數。 指定后，将筛选在给定 ISO8601 时间戳当时或之前创建的已清除实例列表。|
| **`runtimeStatus`**   | 查詢字串    | 選擇性參數。 指定后，将根据运行时状态筛选已清除实例的列表。 若要查看可能的執行階段狀態值清單，請參閱[查詢執行個體](durable-functions-instance-management.md)主題。 |

如果未指定参数，则会清除任务中心内的所有实例。

> [!NOTE]
> 如果“实例”和/或“历史记录”表中包含许多的行，则此操作可能会导致很高的 Azure 存储 I/O 开销。 有关这些表的更多详细信息，请参阅 [Durable Functions (Azure Functions) 中的性能和缩放](durable-functions-perf-and-scale.md#instances-table)文档。

#### <a name="response"></a>Response

可以返回以下 HTTP 状态代码值。

* **HTTP 200 (確定)**：已成功清除实例历史记录。
* **HTTP 404 (找不到)**：找不到与筛选表达式匹配的实例。

值为 **HTTP 200** 时的响应有效负载是包含以下字段的 JSON 对象：

| 欄位                   | 資料類型 | 描述 |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | 删除的实例数。 |

以下是範例回應裝載 (針對可讀性格式化)：

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>引發事件

將事件通知訊息傳送至執行中協調流程執行個體。

#### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數：

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |
| **`eventName`**   | URL             | 目標協調流程執行個體等候之事件的名稱。 |
| **`{content}`**   | 要求內容 | JSON 格式的事件裝載。 |

#### <a name="response"></a>Response

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受)**：已接受引發的事件以進行處理。
* **HTTP 400 (不正確的要求)**：要求內容不是類型 `application/json` 或不是有效的 JSON。
* **HTTP 404 (找不到)**：找不到指定的執行個體。
* **HTTP 410 (不存在)**：指定的執行個體已完成或失敗，且無法再處理任何引發的事件。

以下是範例要求，它會將 JSON 字串 `"incr"` 傳送給等候名為**作業** 之事件的執行個體：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

此 API 的回應不包含任何內容。

### <a name="terminate-instance"></a>終止執行個體

終止執行中協調流程執行個體。

#### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數。

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |
| **`reason`**      | 查詢字串    | 選用。 終止協調流程執行個體的原因。 |

#### <a name="response"></a>Response

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受)**：已接受終止要求以進行處理。
* **HTTP 404 (找不到)**：找不到指定的執行個體。
* **HTTP 410 (不存在)**：指定的執行個體已完成或失敗。

以下是範例要求，它會終止執行中執行個體並且指定**錯誤**的原因：

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的回應不包含任何內容。

## <a name="rewind-instance-preview"></a>倒轉執行個體 (預覽)

藉由重新執行最近失敗的作業，將失敗的協調流程執行個體還原為執行中狀態。

### <a name="request"></a>要求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

此 API 的要求參數包含先前所述的預設集合，以及下列的唯一參數。

| 欄位             | 參數類型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 協調流程執行個體的識別碼。 |
| **`reason`**      | 查詢字串    | 選用。 倒轉協調流程執行個體的原因。 |

### <a name="response"></a>Response

可以傳回幾個可能的狀態字碼值。

* **HTTP 202 (已接受)**：已接受倒轉要求以進行處理。
* **HTTP 404 (找不到)**：找不到指定的執行個體。
* **HTTP 410 (不存在)**：指定的執行個體已完成或終止。

以下是範例要求，它會倒轉失敗的執行個體並且指定**修正**的原因：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的回應不包含任何內容。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理錯誤](durable-functions-error-handling.md)
