---
title: 發佈至 Azure 事件方格 (預覽) 的 Durable Functions
description: 了解如何針對 Durable Functions 設定自動 Azure 事件方格發佈。
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 768af2e89d6523f50bd9fcc3d13cc84b711cc6f0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547467"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>發佈至 Azure 事件方格 (預覽) 的 Durable Functions

本文說明如何設定 Durable Functions，將協調流程生命週期事件 (例如已建立、已完成和失敗) 發佈至自訂 [Azure 事件方格主題](https://docs.microsoft.com/azure/event-grid/overview)。

以下是適合使用此功能的一些案例：

* **DevOps 案例，例如藍色/綠色部署**：您可能想要知道是否有任何工作正在執行中，然後再實行[並存部署策略](durable-functions-versioning.md#side-by-side-deployments)。

* **進階監視和診斷支援**：您可以在已針對查詢最佳化的外部存放區 (例如 SQL 資料庫或 CosmosDB) 中，追蹤協調流程狀態資訊。

* **長時間執行的背景活動**：如果您使用 Durable Functions 進行長時間執行的背景活動，這項功能可協助您知道目前的狀態。

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>必要條件

* 在您的 Durable Functions 專案中安裝[DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 。
* 安裝 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md)。
* 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 或使用 [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>建立自訂事件方格主題

建立事件方格主題，以從 Durable Functions 傳送事件。 下列指示說明如何使用 Azure CLI 建立主題。 如需如何使用 PowerShell 或 Azure 入口網站執行此作業的相關資訊，請參閱下列文章：

* [事件方格快速入門：建立自訂事件 - PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [事件方格快速入門：建立自訂事件 - Azure 入口網站](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>建立資源群組

使用 `az group create` 命令建立資源群組。 Azure 事件方格目前不支援所有區域。 如需有關支援哪些區域的詳細資訊，請參閱[Azure 事件方格總覽](../../event-grid/overview.md)。

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>建立自訂主題

事件方格主題會提供您張貼事件的使用者定義端點。 以主題的唯一名稱取代 `<topic_name>`。 主題名稱必須是唯一的，因為它會變成 DNS 項目。

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>取得端點和金鑰

取得主題的端點。 使用您所選的名稱取代 `<topic_name>`。

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

取得主題金鑰。 使用您所選的名稱取代 `<topic_name>`。

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

您現在可以將事件傳送至主題。

## <a name="configure-azure-event-grid-publishing"></a>設定 Azure 事件方格發佈

在 Durable Functions 專案中，尋找 `host.json` 檔案。

在 `durableTask` 屬性中新增 `eventGridTopicEndpoint` 和 `eventGridKeySettingName`。

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

可能的 Azure 事件方格設定屬性可以在[host. json 檔](../functions-host-json.md#durabletask)中找到。 設定 `host.json` 檔案之後，您的函數應用程式會將生命週期事件傳送至事件方格主題。 這適用于您在本機和 Azure 中執行函數應用程式的情況。

在函式應用程式和 `local.settings.json` 中設定主題索引鍵的應用程式設定。 以下 JSON 是本機偵錯的 `local.settings.json` 範例。 以主題索引鍵取代 `<topic_key>`。  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

確定[儲存體模擬器](../../storage/common/storage-use-emulator.md)運作正常。 在執行之前，最好先執行 `AzureStorageEmulator.exe clear all` 命令。

## <a name="create-functions-that-listen-for-events"></a>建立可接聽事件的函式

建立函式應用程式。 最好是在與事件方格主題相同的區域中找到它。

### <a name="create-an-event-grid-trigger-function"></a>建立事件方格觸發程式函式

建立可接收生命週期事件的函式。 選取 [自訂函式]。

![選取 [建立自訂函式]。](./media/durable-functions-event-publishing/functions-portal.png)

選擇事件方格觸發程序，然後選取 `C#`。

![選取事件方格觸發程序。](./media/durable-functions-event-publishing/eventgrid-trigger.png)

輸入函式名稱，然後選取 `Create`。

![選取事件方格觸發程序。](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

隨即建立具有下列程式碼的函式：

#### <a name="precompiled-c"></a>先行編譯 C#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C# 指令碼

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

選取 `Add Event Grid Subscription`。 此作業會為您所建立的事件方格主題新增事件方格訂用帳戶。 如需詳細資訊，請參閱 [Azure 事件方格概念](https://docs.microsoft.com/azure/event-grid/concepts)。

![選取事件方格觸發程序連結。](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

針對 [主題類型] 選取 `Event Grid Topics`。 選取您為事件方格主題所建立的資源群組。 然後選取事件方格主題的實例。 按 `Create`。

![建立事件格線訂用帳戶。](./media/durable-functions-event-publishing/eventsubscription.png)

您現在已經準備好接收生命週期事件。

## <a name="create-durable-functions-to-send-the-events"></a>建立 Durable Functions 以傳送事件

在您的 Durable Functions 專案中，開始在本機電腦上進行偵錯。  下列程式碼與 Durable Functions 的範本程式碼相同。 您已經在本機電腦上設定 `host.json` 和 `local.settings.json`。

### <a name="precompiled-c"></a>先行編譯 C#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> 先前的程式碼適用于 Durable Functions 2.x。 針對 Durable Functions 1.x，您必須使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`、`OrchestrationClient` 屬性，而不是 `DurableClient` 屬性，而且您必須使用 `DurableOrchestrationClient` 參數類型，而不是 `IDurableOrchestrationClient`。 如需版本之間差異的詳細資訊，請參閱[Durable Functions 版本](durable-functions-versions.md)一文。

如果您使用 Postman 或您的瀏覽器呼叫 `Sample_HttpStart`，Durable Function 就會開始傳送生命週期事件。 端點通常是用於本機偵錯的 `http://localhost:7071/api/Sample_HttpStart` 。

請參閱您在 Azure 快速入門中建立的函式所提供的記錄。

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>事件結構描述

下列清單說明生命週期事件結構描述︰

* **`id`** ：事件方格事件的唯一識別碼。
* **`subject`** ：事件主體的路徑。 `durable/orchestrator/{orchestrationRuntimeStatus}`答案中所述步驟，工作帳戶即會啟用。 `{orchestrationRuntimeStatus}` 會是 `Running`、`Completed`、`Failed` 和 `Terminated`。  
* **`data`** ： Durable Functions 特定參數。
  * **`hubName`** ： [TaskHub](durable-functions-task-hubs.md)名稱。
  * **`functionName`** ：協調器函數名稱。
  * **`instanceId`** ： Durable Functions instanceId。
  * **`reason`** ：與追蹤事件相關聯的其他資料。 如需詳細資訊，請參閱 [Durable Functions 中的診斷 (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** ：協調流程執行時間狀態。 執行中、已完成、失敗、已取消。
* **`eventType`** ： "orchestratorEvent"
* **`eventTime`** ：事件時間（UTC）。
* **`dataVersion`** ：生命週期事件架構的版本。
* **`metadataVersion`** ：中繼資料的版本。
* **`topic`** ：事件方格主題資源。

## <a name="how-to-test-locally"></a>本機測試方式

若要在本機進行測試，請參閱[Azure 函數事件方格觸發程式本機的調試](../functions-debug-event-grid-trigger-local.md)程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的執行個體管理](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的版本控制](durable-functions-versioning.md)
