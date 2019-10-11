---
title: 適用於 Azure Functions 2.x 的 host.json 參考
description: Azure Functions host.json 檔案與 v2 執行階段的參考文件。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: b714559c6c009139da97c7d90425011fb3130ef8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263322"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>適用於 Azure Functions 2.x 的 host.json 參考  

> [!div class="op_single_selector" title1="選取您要使用的 Azure Functions 執行階段版本： "]
> * [第 1 版](functions-host-json-v1.md)
> * [第 2 版](functions-host-json.md)

*host.json* 中繼資料檔案所包含的全域設定選項會影響函式應用程式的所有函式。 本文列出 v2 執行階段可用的設定。  

> [!NOTE]
> 本文適用於 Azure Functions 2.x。  有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

其他函數應用程式設定選項的管理是在[應用程式設定](functions-app-settings.md)中進行。

有些 host.json 設定只有在本機執行時，才會在 [local.settings.json](functions-run-local.md#local-settings-file) 檔案中使用。

## <a name="sample-hostjson-file"></a>範例 host.json 檔案

下列範例 *host.json* 檔案已指定所有可能的選項。

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

本文的下列各節說明每個最上層屬性。 除非另有說明，否則全部都是選擇項目。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

此設定是 [logging](#logging) 的子系。

控制 [Application Insights 中的取樣功能](./functions-monitoring.md#configure-sampling)。

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> 記錄取樣可能會造成一些執行不會顯示在 Application Insights 監視器刀鋒視窗。

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|isEnabled|true|啟用或停用取樣。| 
|maxTelemetryItemsPerSecond|5|取樣的開始臨界值。| 
|EnableLiveMetrics |true|啟用即時計量集合。|
|EnableDependencyTracking|true|啟用相依性追蹤。|
|EnablePerformanceCountersCollection|true|啟用 Kudu 效能計數器集合。|

## <a name="cosmosdb"></a>cosmosDb

可在 [Cosmos DB 觸發程序和繫結](functions-bindings-cosmosdb-v2.md#host-json)中找到組態設定。

## <a name="durabletask"></a>durableTask

可在 [Durable Functions 的繫結](durable/durable-functions-bindings.md#host-json)中找到組態設定。

## <a name="eventhub"></a>eventHub

可在[事件中樞觸發程序和繫結](functions-bindings-event-hubs.md#host-json)中找到組態設定。 

## <a name="extensions"></a>擴充功能

傳回包含所有繫結特定設定 (例如 [http](#http) 和 [eventHub](#eventhub)) 之物件的屬性。

## <a name="functions"></a>functions

工作主機所執行的函式清單。 空陣列表示已執行所有函式。 預定只能在[本機執行](functions-run-local.md)時使用。 在 Azure 的函數應用程式中，您應該改為依照[如何停用 Azure Functions 中的函式](disable-function.md)中的步驟來停用特定函式，而不是使用此設定。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

指出所有函式的逾時持續期間。 它會遵循 timespan 字串格式。 在無伺服器的使用情況方案中，有效範圍是從 1 秒到 10 分鐘，而預設值是 5 分鐘。  
在專用（App Service）方案中，沒有整體限制，而且預設值取決於執行階段版本： 
+ 1\.x 版：預設值為*null*，表示沒有超時。   
+ 2\.x 版：預設值為30分鐘。 值為 `-1` 表示未系結的執行。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[主機健康情況監視器](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)的組態設定。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|enabled|true|指定是否已啟用此功能。 | 
|healthCheckInterval|10 秒|定期背景健康情況檢查之間的時間間隔。 | 
|healthCheckWindow|2 分鐘|與 `healthCheckThreshold` 設定搭配使用的滑動時間範圍。| 
|healthCheckThreshold|6|在主機回收起始之前，健康情況檢查可以失敗的最大次數。| 
|counterThreshold|0.80|系統會將效能計數器視為狀況不良的閾值。| 

## <a name="http"></a>http

可在 [HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)中找到組態設定。

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true
        }
    }
}
```


[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>logging

控制函數應用程式 (包括 Application Insights) 的記錄行為。

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|定義已啟用何種檔案記錄層級。  選項為 `never`、`always`、`debugOnly`。 |
|logLevel|n/a|為應用程式中的函式定義記錄類別篩選的物件。 2\.x 版會依循 ASP.NET Core 的記錄類別篩選配置。 這可讓您篩選特定函式的記錄。 如需詳細資訊，請參閱 ASP.NET Core 文件中的[記錄篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 |
|console|n/a| [console](#console)記錄設定。 |
|applicationInsights|n/a| [applicationInsights](#applicationinsights) 設定。 |

## <a name="console"></a>console

此設定是 [logging](#logging) 的子系。 它會在非處於偵錯模式時控制主控台記錄。

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|isEnabled|false|啟用或停用主控台記錄。| 

## <a name="queues"></a>queues

可在[儲存體佇列觸發程序和繫結](functions-bindings-storage-queue.md#host-json)中找到組態設定。  

## <a name="sendgrid"></a>sendGrid

可在 [SendGrid 觸發程序和繫結](functions-bindings-sendgrid.md#host-json)中找到組態設定。

## <a name="servicebus"></a>serviceBus

可在[服務匯流排觸發程序和繫結](functions-bindings-service-bus.md#host-json)中找到組態設定。

## <a name="singleton"></a>singleton

Singleton 鎖定行為的組態設定。 如需詳細資訊，請參閱[單一支援的 GitHub 問題](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|lockPeriod|00:00:15|取得函式層級鎖定的期間。 鎖定會自動更新。| 
|listenerLockPeriod|00:01:00|接聽程式鎖定所需的期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|啟動時無法取得接聽程式鎖定時，用於接聽程式鎖定復原的時間間隔。| 
|lockAcquisitionTimeout|00:01:00|執行階段將嘗試取得鎖定的時間量上限。| 
|lockAcquisitionPollingInterval|n/a|鎖定取得嘗試之間的間隔。| 

## <a name="version"></a>版本

目標為 v2 執行階段的函數應用程式必須要有 `"version": "2.0"` 版本字串。

## <a name="watchdirectories"></a>watchDirectories

應該監視其變更的一組[共用程式碼目錄](functions-reference-csharp.md#watched-directories)。  請確定，這些目錄中的程式碼變更時，函式會反映變更。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

受控相依性是一項預覽功能，目前僅支援以 PowerShell 為基礎的函式。 它可讓服務自動管理相依性。 當 enabled 屬性設定為 true 時，將會處理[.psd1](functions-reference-powershell.md#dependency-management)檔案。 發行任何次要版本時，將會更新相依性。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何更新 host.json 檔案](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [請參閱環境變數中的全域設定](functions-app-settings.md)
