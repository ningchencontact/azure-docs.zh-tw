---
title: Azure Application Insights 中的相依性追蹤 | Microsoft Docs
description: 從您的內部部署或 Microsoft Azure web 應用程式使用 Application Insights 監視相依性呼叫。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d8ba5b19ad5d8f03203e9a028fbc5aec84e5ec06
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565377"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>追蹤 Azure Application Insights 中的相依性 

「相依性」  是由應用程式呼叫的外部元件。 這通常是使用 HTTP 呼叫的服務，或資料庫，或檔案系統。 [Application Insights](../../azure-monitor/app/app-insights-overview.md)測量的相依性呼叫持續時間是否其失敗，以及其他資訊等名稱的相依性，依此類推。 您可以調查特定的相依性呼叫，並將它們以要求和例外狀況相互關聯。

## <a name="automatically-tracked-dependencies"></a>自動追蹤相依性

Application Insights Sdk for.NET 和.NET Core 隨附`DependencyTrackingTelemetryModule`即會自動收集相依性遙測模組。 此相依性集合會自動啟用，如[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)並[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)應用程式，根據連結的官方文件進行設定。`DependencyTrackingTelemetryModule`當做[這](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)NuGet 套件，並使用其中一個 NuGet 套件時自動帶入`Microsoft.ApplicationInsights.Web`或`Microsoft.ApplicationInsights.AspNetCore`。

 `DependencyTrackingTelemetryModule` 目前，會自動追蹤下列相依性：

|相依性 |詳細資料|
|---------------|-------|
|Http/Https | 本機或遠端 http/https 呼叫 |
|WCF 呼叫| 只會自動追蹤如果會使用以 Http 為基礎的繫結。|
|SQL | 使用呼叫`SqlClient`。 請參閱[這](##advanced-sql-tracking-to-get-full-sql-query)擷取 SQL 查詢。  |
|[Azure 儲存體 （Blob、 資料表、 佇列）](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 儲存體用戶端所進行的呼叫。 |
|[事件中樞用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 版和更新版本。 |
|[ServiceBus 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 版和更新版本。 |
|Azure Cosmos DB | 只會自動追蹤如果使用 HTTP/HTTPS。 Application Insights 不會擷取 TCP 模式。 |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>設定自動追蹤主控台應用程式中的相依性

若要自動追蹤相依性從.NET/.NET Core 主控台應用程式，安裝 Nuget 套件`Microsoft.ApplicationInsights.DependencyCollector`，並初始化`DependencyTrackingTelemetryModule`，如下所示：

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>自動的相依性監視工作？

相依性會自動收集使用其中一種下列技術：

* 使用選取的方法周圍的位元組程式碼檢測。 (InstrumentationEngine 從 StatusMonitor 或 Azure Web 應用程式擴充功能)
* EventSource 回呼
* DiagnosticSource 回呼 （在最新的.NET/.NET Core Sdk)

## <a name="manually-tracking-dependencies"></a>手動追蹤相依項目

以下是一些範例相依性，不會自動收集，並因此需要手動追蹤。

* 只有在使用 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) 的情況下，才會自動追蹤 Azure Cosmos DB。 Application Insights 不會擷取 TCP 模式。
* Redis

SDK 不會自動收集這些相依性，您可以追蹤其使用手動[TrackDependency API](api-custom-events-metrics.md#trackdependency)所用的自動標準集合模組。

例如，如果您建置程式碼的組件不是您自己撰寫的，您可以計算對組件的所有呼叫，以找出它佔回應時間的比例。 若要在 Application Insights 中的相依性圖表中顯示此資料，請使用 `TrackDependency`傳送。

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

或者，`TelemetryClient`提供擴充方法`StartOperation`並`StopOperation`可用來以手動方式追蹤相依性，如所示[這裡](custom-operations-tracking.md#outgoing-dependencies-tracking)

如果您想要關閉標準的相依性追蹤模組，移除中 DependencyTrackingTelemetryModule 的參考[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ASP.NET 應用程式。 針對 ASP.NET Core 應用程式，請遵循指示[此處](asp-net-core.md#configuring-or-removing-default-telemetrymodules)。

## <a name="tracking-ajax-calls-from-web-pages"></a>追蹤來自網頁的 AJAX 呼叫

Web 網頁的 Application Insights JavaScript SDK 會自動收集 AJAX 呼叫，做為相依性所述[此處](javascript.md#ajax-performance)。 本文件著重於從伺服器元件的相依性。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>進階 SQL 追蹤來取得完整的 SQL 查詢

針對 SQL 呼叫，伺服器和資料庫的名稱一律收集並儲存為名稱的收集`DependencyTelemetry`。 沒有額外的欄位稱為 「 資料 」，其中可以包含完整的 SQL 查詢文字。

對於 ASP.NET Core 應用程式中，沒有任何額外的步驟，才能取得完整的 SQL 查詢。

ASP.NET 應用程式，完整的 SQL 查詢會收集的位元組程式碼檢測，而這需要檢測引擎。 其他平台特定，如下所述，不需要步驟。

| 平台 | 若要取得完整的 SQL 查詢所需的步驟 |
| --- | --- |
| Azure Web 應用程式 |在您 web 應用程式控制台[開啟 [Application Insights] 刀鋒視窗](../../azure-monitor/app/azure-web-apps.md)並啟用 SQL 命令，在.NET 底下 |
| IIS 伺服器 （Azure VM、 內部，等等。） | [您的應用程式執行所在的伺服器上安裝狀態監視器](../../azure-monitor/app/monitor-performance-live-website-now.md)並重新啟動 IIS。
| Azure 雲端服務 | 新增[安裝 StatusMonitor 的啟動工作](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 您的應用程式應該會在建置階段上的架到 application Insights SDK 所安裝的 NuGet 套件[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)或[ASP.NET Core 應用程式](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | 不支援

在上述所有情況下，驗證該檢測引擎的正確方式是正確安裝是藉由驗證的 SDK 版本收集`DependencyTelemetry`是 'rddp'。 'rdddsd' 或 'rddf' 表示相依性會收集透過 DiagnosticSource 或 EventSource 的回呼，並因此將不會擷取完整的 SQL 查詢。

## <a name="where-to-find-dependency-data"></a>哪裡可以找到相依性資料

* [應用程式對應](app-map.md)會以視覺化方式顯示您應用程式與相鄰元件之間的相依性。
* [交易診斷](transaction-diagnostics.md)顯示整合、 相互關聯的伺服器資料。
* [瀏覽器索引標籤](javascript.md#ajax-performance)顯示來自使用者的瀏覽器的 AJAX 呼叫。
* 從速度緩慢或失敗的要求逐一點選以檢查其相依性呼叫。
* [分析](#logs-analytics)可用來查詢相依性資料。

## <a name="diagnosis"></a> 診斷速度緩慢的要求

每個要求事件是相關聯的相依性呼叫、 例外狀況，以及其他應用程式處理要求時所追蹤的事件。 因此如果某些要求執行錯誤，您可以找出是否因為相依性回應變慢。

### <a name="tracing-from-requests-to-dependencies"></a>進行從要求到相依性的追蹤

開啟**效能**索引標籤，然後瀏覽至**相依性**旁邊作業頂端索引標籤。

按一下 **相依性名稱**整體底下。 選取相依性之後該相依性的持續時間的分佈的圖形會顯示在右邊。

![在效能 索引標籤上按一下 相依性 索引標籤，然後在圖表中的相依性名稱上方](./media/asp-net-dependencies/2-perf-dependencies.png)

按一下藍色**範例**右下角，然後按一下 [範例，了解端對端交易詳細資料] 按鈕。

![按一下以查看端對端交易詳細資料範例](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>剖析您的即時網站

不清楚時間花在哪裡嗎？ [Application Insights profiler](../../azure-monitor/app/profiler.md)追蹤 HTTP 呼叫您的即時網站，並顯示在您花費時間最長的程式碼中的函式。

## <a name="failed-requests"></a>失敗的要求

失敗的要求可能也會與失敗的相依性呼叫關聯。

我們可以前往**失敗**左側索引標籤，然後按一下**相依性**頂端索引標籤。

![按一下失敗要求的圖表](./media/asp-net-dependencies/4-fail.png)

這裡您將能夠看到失敗的相依性計數。 若要取得更多詳細的失敗嘗試按一下下表中的相依性名稱的項目。 您可以按一下藍色**相依性**右下方，以取得端對端交易詳細資料 按鈕。

## <a name="logs-analytics"></a>記錄檔 （分析）

您可以在 [Kusto 查詢語言](/azure/kusto/query/)中追蹤相依性。 以下是一些範例。

* 尋找任何失敗的相依性呼叫：

``` Kusto

    dependencies | where success != "True" | take 10
```

* 尋找 AJAX 呼叫︰

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* 尋找與要求關聯的相依性呼叫：

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 尋找與頁面檢視關聯的 AJAX 呼叫：

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*如何執行自動的相依性收集器報告失敗的相依性呼叫嗎？*

* 失敗的相依性呼叫將會具有 「 成功 」 欄位設定為 False。 `DependencyTrackingTelemetryModule` 不會報告`ExceptionTelemetry`。 相依性的完整資料模型會描述[此處](data-model-dependency-telemetry.md)。

## <a name="open-source-sdk"></a>開放原始碼 SDK
每個 Application Insights SDK 中，例如相依性集合模組也是開放原始碼。 讀取和貢獻程式碼，或回報問題[官方 GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-dotnet-server)。

## <a name="next-steps"></a>後續步驟

* [例外狀況](../../azure-monitor/app/asp-net-exceptions.md)
* [使用者和頁面資料](../../azure-monitor/app/javascript.md)
* [Availability](../../azure-monitor/app/monitor-web-app-availability.md)
