---
title: Application Insights 遙測相互關聯 | Microsoft Docs
description: Application Insights 遙測相互關聯
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 030259f7773435760c09afd25ca674b63bb1b3ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073247"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights 中的遙測相互關聯

在微服務的世界裡，每個邏輯作業都需要在服務的各種元件中完成工作。 這些元件中每一個都可由 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 個別監視。 Web 應用程式元件會與驗證提供者元件進行通訊以驗證使用者認證，並透過 API 元件取得視覺效果的資料。 API 元件可以向其他服務查詢資料，並使用快取提供者元件來通知計費元件關於這個呼叫的資訊。 Application Insights 支援分散式遙測相互關聯，這可供您用來偵測造成失敗或效能衰退的元件。

本文說明 Application Insights 所使用的資料模型，以關聯多個元件所傳送的遙測。 它涵蓋內容傳播技巧和通訊協定。 此外，還包括在不同的語言和平台上實作相互關聯概念。

## <a name="data-model-for-telemetry-correlation"></a>遙測相互關聯的資料模型

Application Insights 會定義分散遙測相互關聯的[資料模型](../../azure-monitor/app/data-model.md)。 為了將遙測與邏輯作業相關聯，每個遙測項目都有稱為 `operation_Id` 的內容欄位。 每個遙測項目會在分散式追蹤內共用這個識別碼。 因此，即使單一層中遺失遙測，您仍然可以將其他元件所回報的遙測相關聯。

分散式邏輯作業通常由一組較小的作業所組成，它們是由其中一個元件所處理的要求。 這些作業是由[要求遙測](../../azure-monitor/app/data-model-request-telemetry.md)所定義。 每個要求遙測都有自己的 `id`，用來作為其全域唯一識別。 與這個要求關聯的所有遙測 (例如追蹤和例外狀況) 都應該將 `operation_parentId` 設定為 `id` 要求的值。

每個連出作業 (例如對另一個元件的 HTTP 呼叫) 都會由[相依性遙測](../../azure-monitor/app/data-model-dependency-telemetry.md)代表。 相依性遙測也會定義它自己的全域唯一 `id`。 由這個相依性呼叫起始的要求遙測會使用 這個 `id` 作為其 `operation_parentId`。

您可以將 `operation_Id`、`operation_parentId` 和 `request.id` 與 `dependency.id` 搭配使用，來建置分散式邏輯作業的檢視。 這些欄位也會定義遙測呼叫的因果順序。

在微服務環境中，來自元件的追蹤可能會前往不同的儲存體項目。 每個元件在 Application Insights 中都可能有自己的檢測金鑰。 若要取得邏輯作業的遙測，Application Insights UX 會查詢每個儲存體項目資料。 當儲存體項目的數目很龐大時，您將需要有關下一個尋找位置的提示。 Application Insights 資料模型會定義兩個欄位來解決這個問題：`request.source` 和 `dependency.target`。 第一個欄位會識別起始相依性要求的元件，而第二個欄位會識別傳回相依性呼叫回應的元件。

## <a name="example"></a>範例

讓我們以名為 Stock Prices 的應用程式為例，它使用名為 `Stock` 的外部 API 來顯示目前的股市價格。 Stock Prices 應用程式有一個名為 `Stock page` 的頁面，用戶端網頁瀏覽器會使用 `GET /Home/Stock` 來開啟此頁面。 此應用程式會使用 `GET /api/stock/value` HTTP 呼叫來查詢 `Stock` API。

您可以執行查詢來分析產生的遙測︰

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

在結果中，請注意，所有遙測項目都共用 `operation_Id` 這個根。 從此頁面發出 Ajax 呼叫時，系統會將新的唯一識別碼 (`qJSXU`) 指派給相依性遙測，並使用 pageView 的識別碼作為 `operation_ParentId`。 接著，伺服器要求會使用 Ajax 識別碼作為 `operation_ParentId`。

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock 頁面                |              | STYz               | STYz         |
| 相依性 | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| 相依性 | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

向外部服務發出 `GET /api/stock/value` 呼叫時，您想要知道該伺服器的身分識別，以便適當地設定 `dependency.target` 欄位。 當外部服務不支援監視時，`target` 會設定為服務的主機名稱 (例如 `stock-prices-api.com`)。 不過，如果服務會藉由傳回預先定義的 HTTP 標頭來識別其本身，則 `target` 包含的服務識別可讓 Application Insights 透過向該服務查詢遙測來建置分散式追蹤。

## <a name="correlation-headers"></a>相互關聯標頭

我們正在進行[相互關聯 HTTP 通訊協定](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)的 RFC 提案。 此提案會定義兩個標頭︰

- `Request-Id`:裝載呼叫的全域唯一識別碼。
- `Correlation-Context`:裝載分散式追蹤屬性的名稱值組集合。

標準也定義了兩個用於產生 `Request-Id` 的結構描述：平面和階層。 使用平面結構描述時，會為 `Correlation-Context` 集合定義已知的 `Id` 金鑰。

Application Insights 會定義相互關聯 HTTP 通訊協定的[延伸](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)。 它會使用 `Request-Context` 名稱值組，來傳播立即呼叫端或被呼叫端所使用的屬性集合。 Application Insights SDK 會使用此標頭來設定 `dependency.target` 和 `request.source` 欄位。

### <a name="w3c-distributed-tracing"></a>W3C 分散式追蹤

我們即將轉換為 [W3C 分散式追蹤格式](https://w3c.github.io/trace-context/)。 其定義：

- `traceparent`:裝載全域唯一的作業識別碼和唯一的呼叫識別碼。
- `tracestate`:裝載追蹤系統特定的內容。

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>啟用傳統 ASP.NET 應用程式的 W3C 分散式追蹤支援

`Microsoft.ApplicationInsights.Web` 和 `Microsoft.ApplicationInsights.DependencyCollector` 套件從 2.8.0-beta1 版開始，有提供此功能。
此功能預設為停用。 若要啟用它，請變更 `ApplicationInsights.config`：

- 在 `RequestTrackingTelemetryModule` 底下，新增值設定為 `true` 的 `EnableW3CHeadersExtraction` 元素。
- 在 `DependencyTrackingTelemetryModule` 底下，新增值設定為 `true` 的 `EnableW3CHeadersInjection` 元素。

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>啟用 ASP.NET Core 應用程式的 W3C 分散式追蹤支援

此功能位於 `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-beta1 版和 `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1 版中。
此功能預設為停用。 若要啟用它，請將 `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` 設定為 `true`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>啟用 Java 應用程式的 W3C 分散式追蹤支援

- **連入設定**

  - 針對 Java EE 應用程式，將下列內容新增至 ApplicationInsights.xml 內的 `<TelemetryModules>` 標記：

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - 針對 Spring Boot 應用程式，新增下列屬性：

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **連出設定**

  將下列程式碼新增至 AI-Agent.xml：

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > 預設會啟用回溯相容性模式，而 `enableW3CBackCompat` 參數則為選擇性。 請只在您想要關閉回溯相容性功能時，才使用它。
  >
  > 理想的情況是，當所有服務都已更新至支援 W3C 通訊協定的新版 SDK 時，您會關閉此功能。 強烈建議您儘快移轉至這些較新版的 SDK。

> [!IMPORTANT]
> 請確定連入設定與連出設定完全相同。

## <a name="opentracing-and-application-insights"></a>OpenTracing 與 Application Insights

[OpenTracing 資料模型規格](https://opentracing.io/)與 Application Insights 資料模型的對應方式如下：

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`、 `PageView`                 | `span.kind = server` 的 `Span`                  |
| `Dependency`                          | `span.kind = client` 的 `Span`                  |
| `Request` 和 `Dependency` 的 `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | 類型 `ChildOf` 的 `Reference` (父代範圍)   |

如需詳細資訊，請參閱 [Application Insights 遙測資料模型](../../azure-monitor/app/data-model.md)。 

如需 OpenTracing 概念的定義，請參閱 OpenTracing [規格](https://github.com/opentracing/specification/blob/master/specification.md)和 [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)。

## <a name="telemetry-correlation-in-net"></a>.NET 中的遙測相互關聯

.NET 隨著時間的進展，定義了數種相互關聯遙測和診斷記錄的方式：

- `System.Diagnostics.CorrelationManager` 可讓您追蹤 [LogicalOperationStack 和 ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)。 
- `System.Diagnostics.Tracing.EventSource` 與 Windows 事件追蹤 (ETW) 會定義 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 方法。
- `ILogger` 會使用[記錄範圍](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)。 
- Windows Communication Foundation (WCF) 與 HTTP 連接起了「目前」的內容傳播。

不過，這些方法並未促成自動分散式追蹤支援。 `DiagnosticSource` 是一種支援自動跨電腦相互關聯的方法。 .NET 程式庫支援 'DiagnosticSource'，並允許透過傳輸 (例如 HTTP) 自動跨電腦傳播相互關聯內容。

`DiagnosticSource` 中的[活動指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)說明了追蹤活動的基本概念。

ASP.NET Core 2.0 支援擷取 HTTP 標頭和啟動新的活動。

`System.Net.HttpClient` 從 4.1.0 版開始，可支援自動插入相互關聯 HTTP 標頭及追蹤 HTTP 呼叫作為活動。

針對傳統 ASP.NET，有一個新的 HTTP 模組 [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)。 此模組會使用 `DiagnosticSource` 來實作遙測相互關聯。 它會根據連入要求標頭來啟動活動。 它也會將來自不同要求處理階段的遙測相互關聯，即使每個 Internet Information Services (IIS) 處理階段是在不同受控執行緒上執行時也一樣。

Application Insights SDK 從 2.4.0-beta1 版開始，會使用 `DiagnosticSource` 和 `Activity` 來收集遙測，並將它與目前活動建立關聯。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK 中的遙測相互關聯

[Application Insights SDK for Java](../../azure-monitor/app/java-get-started.md) 從 2.0.0 版開始，便支援自動將遙測相互關聯。 它會為在要求範圍內發出的所有遙測 (例如追蹤、例外狀況及自訂事件)，自動填入 `operation_id`。 如果已設定 [Java SDK 代理程式](../../azure-monitor/app/java-agent.md)，它也會負責透過 HTTP 傳播服務對服務呼叫的相互關聯標頭 (如先前所述)。

> [!NOTE]
> 相互關聯功能僅支援透過 Apache HTTPClient 發出的呼叫。 如果您使用 Spring RestTemplate 或 Feign，這兩者實際上都可以與 Apache HTTPClient 搭配使用。

目前不支援跨訊息技術 (例如 Kafka、RabbitMQ、Azure 服務匯流排) 自動傳播內容。 不過，您可以使用 `trackDependency` 和 `trackRequest` API 來手動撰寫這類案例的程式碼。 在這些 API 中，相依性遙測代表由產生者排入佇列的訊息，而要求則代表取用者所處理的訊息。 在此情況下，`operation_id` 和 `operation_parentId` 應該同時在訊息的屬性中傳播。

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>非同步 Java 應用程式中的遙測相互關聯

若要將非同步的 Spring Boot 應用程式中的遙測相互關聯，請遵循[這](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications)相關文章。 它提供指導方針進行檢測的 Spring [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) ，以及[ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)。 


<a name="java-role-name"></a>
## <a name="role-name"></a>角色名稱

有時候，您可以自訂元件名稱在[應用程式對應](../../azure-monitor/app/app-map.md)中的顯示方式。 若要這樣做，您可以用下列其中一項動作手動設定 `cloud_RoleName`：

- 如果您是使用 Spring Boot 搭配 Application Insights Spring Boot 入門版，只需設定您在 application.properties 檔案中應用程式的自訂名稱。

  `spring.application.name=<name-of-app>`

  Spring Boot 入門版會自動將 `cloudRoleName` 指派給您為 `spring.application.name` 屬性輸入的值。

- 如果您使用 `WebRequestTrackingFilter`，則 `WebAppNameContextInitializer` 會自動設定應用程式名稱。 將下列新增至組態檔 (ApplicationInsights.xml)：

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- 如果您使用雲端內容類別：

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>後續步驟

- 撰寫[自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)。
- 深入了解為其他 SDK [設定 cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name)。
- 在 Application Insights 上將微服務的所有元件上線。 查看[支援的平台](../../azure-monitor/app/platforms.md)。
- 參閱[資料模型](../../azure-monitor/app/data-model.md)以了解 Application Insights 類型。
- 了解如何[擴充和篩選遙測](../../azure-monitor/app/api-filtering-sampling.md)。
- 檢閱 [Application Insights 設定參考](configuration-with-applicationinsights-config.md)。
