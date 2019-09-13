---
title: Durable Functions Azure Functions 中的 HTTP 功能
description: 瞭解 Azure Functions 的 Durable Functions 延伸模組中的整合式 HTTP 功能。
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935790"
---
# <a name="http-features"></a>HTTP 功能

Durable Functions 有數個功能，可讓您輕鬆地將長期協調流程和實體納入 HTTP 工作流程中。 本文將詳述其中一些功能的詳細資料。

## <a name="exposing-http-apis"></a>公開 HTTP Api

您可以使用 HTTP 要求叫用和管理協調流程和實體。 Durable Functions 擴充功能會公開內建的 HTTP Api，也會提供 Api，以便與 HTTP 觸發的函式中的協調流程和實體互動。

### <a name="built-in-http-apis"></a>內建的 HTTP Api

Durable Functions 延伸模組會自動將一組 HTTP Api 新增至 Azure Functions 主機。 這些 Api 可讓您在不需撰寫任何程式碼的情況下，與協調流程和實體互動並加以管理。

支援下列內建 HTTP Api。

* [啟動新的協調流程](durable-functions-http-api.md#start-orchestration)
* [查詢協調流程實例](durable-functions-http-api.md#get-instance-status)
* [終止協調流程實例](durable-functions-http-api.md#terminate-instance)
* [將外來事件傳送至協調流程](durable-functions-http-api.md#raise-event)
* [清除協調流程歷程記錄](durable-functions-http-api.md#purge-single-instance-history)
* [將作業事件傳送至實體](durable-functions-http-api.md#signal-entity)
* [查詢實體的狀態](durable-functions-http-api.md#query-entity)

如需 Durable Functions 延伸模組所公開之所有內建 HTTP Api 的完整說明，請參閱[HTTP api](durable-functions-http-api.md)文章。

### <a name="http-api-url-discovery"></a>HTTP API URL 探索

[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結會公開可用來產生便利 HTTP 回應裝載的 api。 例如，它可以建立回應，其中包含特定協調流程實例的管理 Api 連結。 以下是範例 HTTP 觸發程式函式，示範如何將此 API 用於新的協調流程實例：

#### <a name="precompiled-c"></a>先行編譯 C#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C# 指令碼

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

使用上述的 HTTP 觸發程式函數啟動協調器函式，可以使用任何 HTTP 用戶端來完成。 下列捲曲命令會啟動名為`DoWork`的協調器函式。

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

以下是協調流程`abc123`的範例回應，其識別碼為（為了清楚起見，已移除一些詳細資料）：

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

上一個範例中的每個欄位都會對應至內建的HTTPapi。`~Uri` 這些 Api 可以用來管理目標協調流程實例。

> [!NOTE]
> Webhook URL 的格式依據您執行的 Azure Functions 主機版本，可能會有所不同。 上述範例適用於 Azure Functions 2.0 主機。

如需所有內建 HTTP Api 的說明，請參閱[HTTP API 參考](durable-functions-http-api.md)檔。

### <a name="async-operation-tracking"></a>非同步作業追蹤

先前所述之 HTTP 回應的設計目的，是協助搭配長期函式實作長時間執行 HTTP 非同步 API。 此模式有時稱為「*輪詢取用者模式*」。 用戶端/伺服器流量運作方式如下：

1. 用戶端會發出 HTTP 要求，以啟動長時間執行處理序，例如協調器函式。
2. 目標 HTTP 觸發程序會傳回 HTTP 202 回應，具有 `Location` 標頭和 `statusQueryGetUri` 值。
3. 用戶端會輪詢 `Location` 標頭中的 URL。 它會繼續查看具有 `Location` 標頭的 HTTP 202 回應。
4. 當執行個體完成 (或失敗) 時，`Location` 標頭中的端點會傳回 HTTP 200。

此通訊協定可以協調長時間執行處理序與外部用戶端或服務，它們支援輪詢 HTTP 端點，並且遵循 `Location` 標頭。 此模式的用戶端和伺服器都內建于 Durable Functions 的 HTTP Api。

> [!NOTE]
> 根據預設，[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) 提供的所有 HTTP 型動作皆支援標準的非同步作業模式。 這項功能可以嵌入長期執行長期函式，作為 Logic Apps 工作流程的一部分。 Logic Apps 支援非同步 HTTP 模式的詳細資訊，可以在 [Azure Logic Apps 工作流程動作和觸發程序文件](../../logic-apps/logic-apps-workflow-actions-triggers.md)中找到。

> [!NOTE]
> 您可以從任何函式類型（而不只是 HTTP 觸發的函數）來與協調流程互動。

如需有關如何使用用戶端 Api 來管理協調流程和實體的詳細資訊，請參閱[實例管理](durable-functions-instance-management.md)一文。

## <a name="consuming-http-apis"></a>使用 HTTP Api

協調器函數不允許直接執行 i/o，如協調器函式程式[代碼條件約束](durable-functions-code-constraints.md)中所述。 相反地，協調器函式通常會呼叫執行 i/o 作業的[活動](durable-functions-types-features-overview.md#activity-functions)函式。

從 Durable Functions 2.0 開始，協調流程就能夠使用[協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系結，以原生方式取用 HTTP api。

> [!NOTE]
> JavaScript 中尚未提供直接從協調器函式呼叫 HTTP 端點的功能。

下列範例程式碼示範使用C# `CallHttpAsync` .net API 發出輸出 HTTP 要求的協調器函式：

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

[呼叫 HTTP] 動作可讓您執行協調器函式中的下列專案：

* 直接從協調流程函式呼叫 HTTP Api （稍後會提到一些限制）。
* 自動支援用戶端 HTTP 202 狀態輪詢模式。
* 使用[Azure 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別向其他 azure 端點進行經授權的 HTTP 呼叫。

直接從協調器函式取用 HTTP Api 的功能，是為了方便特定一組常見的案例使用。 您可以使用活動功能自行執行所有這些功能。 在許多情況下，活動功能可提供更大的彈性。

### <a name="http-202-handling"></a>HTTP 202 處理

「呼叫 HTTP」 API 可以自動執行*輪詢取用者模式*的用戶端。 如果呼叫的 API 傳回具有`Location`標頭的 HTTP 202 回應，協調器函式會自動`Location`輪詢資源，直到發生非202回應為止。 非202回應會是傳回給協調器函式程式碼的回應。

> [!NOTE]
> 協調器函式也原本就支援伺服器端*輪詢取用者模式*，如[非同步作業追蹤](#async-operation-tracking)中所述。 這表示一個函式應用程式中的協調流程可以輕鬆地協調其他函數應用程式中的協調器函式。 這類似于[子協調流程](durable-functions-sub-orchestrations.md)概念，但支援跨應用程式通訊。 這特別適用于微服務樣式的應用程式開發。

### <a name="managed-identities"></a>受控識別

Durable Functions 原本就支援呼叫接受 Azure AD 權杖以進行授權的 Api。 這項支援使用[Azure 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別來取得這些權杖。

下列程式碼是 .NET 協調器函式的範例，它會使用 Azure Resource Manager[虛擬機器 REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)，進行已驗證的呼叫來重新開機虛擬機器。

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

在上述範例中， `tokenSource`參數已設定為取得[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)的 Azure AD token （以「資源 URI `https://management.core.windows.net`」識別）。 此範例假設目前的函式應用程式正在本機執行，或已部署為具有受控識別的 Azure Functions 應用程式。 本機身分識別或受控識別會假設有許可權可管理指定資源群組`myRG`中的 vm。

在執行時間，設定的權杖來源會自動傳回 OAuth 2.0 存取權杖，並將它做為持有人`Authorization`權杖新增至傳出要求的標頭。 此模型是透過手動將授權標頭新增至 HTTP 要求的改進，因為：

* 權杖重新整理會自動處理。 您不必擔心過期的權杖。
* 權杖永遠不會以持久的協調流程狀態儲存。
* 您不需要撰寫任何程式碼來處理權杖取得。

如需更完整的範例，請參閱[先行C#編譯的 "RestartVMs" 範例](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)。

受控識別不限於 Azure 資源管理。 受控識別可以用來存取任何接受 Azure AD 持有人權杖的 API，包括第一方 Azure 服務或協力廠商 web 應用程式。 協力廠商 web 應用程式甚至可以是另一個函數應用程式。 如需使用 Azure AD 來支援驗證的第一方 Azure 服務清單，請參閱[支援 Azure AD 驗證的 Azure 服務](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

### <a name="limitations"></a>限制

呼叫 HTTP Api 的內建支援是一項便利功能，不適合所有案例。 協調器函式和其回應所傳送的 HTTP 要求會序列化並保存為佇列訊息。 此佇列行為可確保 HTTP 呼叫在[協調流程重新執行時是可靠且安全的](durable-functions-orchestrations.md#reliability)。 不過，此佇列行為也表示：

* 相較于原生 HTTP 用戶端，每個 HTTP 要求都會牽涉到額外的延遲。
* 無法納入佇列訊息的大型要求或回應訊息可能會大幅降低協調流程效能。 可能的效能降低的原因是將訊息承載卸載至 blob 儲存體的額外負荷。
* 不支援串流、區塊和二進位承載。
* 自訂 HTTP 用戶端行為的能力有限。

如果任何這些限制可能會影響您的使用案例，請考慮改為使用活動函式和語言特定的 HTTP 用戶端程式庫來發出輸出 HTTP 呼叫。

> [!NOTE]
> 如果您是 .net 開發人員，您可能會想知道這項功能`DurableHttpRequest`為何`DurableHttpResponse`使用和類型，而不是內`HttpRequestMessage`建`HttpResponseMessage`的 .net 和。 這是刻意設計的選擇。 主要的原因是自訂類型有助於確保使用者不會對內部 HTTP 用戶端支援的行為做出不正確的假設。 持久性特定類型也可以簡化 API 設計，並更輕鬆地縮小特殊功能，例如[受控識別整合](#managed-identities)和[輪詢取用者模式](#http-202-handling)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解持久性實體](durable-functions-entities.md)