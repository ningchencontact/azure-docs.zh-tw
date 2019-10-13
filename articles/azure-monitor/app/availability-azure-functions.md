---
title: 使用 Azure Functions 建立及執行自訂可用性測試
description: 本檔將說明如何使用 TrackAvailability （）建立 Azure 函式，此函式會根據 TimerTrigger 函式中指定的設定定期執行。 這項測試的結果將會傳送至您的 Application Insights 資源，您可以在其中查詢可用性結果資料併發出警示。 自訂測試可讓您撰寫比使用入口網站 UI、監視 Azure VNET 內的應用程式、變更端點位址，或建立可用性測試（如果您的區域中未提供）時更複雜的可用性測試。
services: application-insights
documentationcenter: ''
author: morgangrobin
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: mogrobin
ms.openlocfilehash: 38a83169a7d1ffa03416f5947ada703bcba5017a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301369"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>使用 Azure Functions 建立及執行自訂可用性測試

本文將說明如何使用 TrackAvailability （）建立 Azure 函式，該函數會根據 TimerTrigger 函數中指定的設定定期執行。 這項測試的結果將會傳送至您的 Application Insights 資源，您可以在其中查詢可用性結果資料併發出警示。 這可讓您建立自訂的測試，就像您可以透過入口網站中的[可用性監視](../../azure-monitor/app/monitor-web-app-availability.md)來執行此動作。 自訂測試可讓您撰寫更複雜的可用性測試，而不是使用入口網站 UI、監視 Azure VNET 內的應用程式、變更端點位址，或建立可用性測試，即使您的區域無法使用這項功能。


## <a name="create-timer-triggered-function"></a>建立計時器觸發函式

- 如果您有 Application Insights 資源：
    - 根據預設，Azure Functions 會建立 Application Insights 資源，但如果您想要使用其中一個已建立的資源，則必須在建立期間指定它。
    - 請遵循如何[建立 Azure Functions 資源和計時器觸發函式](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)（在清除前停止）的指示，並使用下列選項。
        -  按一下 [Application Insights] 區段，然後選取 [**建立**]。

            ![ 使用您自己的 App Insights 資源建立 Azure Functions 應用程式](media/availability-azure-functions/create-function-app.png)

        - 按一下 [**選取現有的資源**]，然後輸入您的資源名稱。 選取**套用**

            ![選取現有的 Application Insights 資源](media/availability-azure-functions/app-insights-resource.png)

        - 選取 [建立]
- 如果您還沒有為計時器觸發的函式建立 Application Insights 資源：
    - 根據預設，當您建立 Azure Functions 應用程式時，它會為您建立 Application Insights 資源。
    - 請遵循如何[建立 Azure Functions 資源和計時器觸發](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function)函式（在清除前停止）的指示。

## <a name="sample-code"></a>範例程式碼

將下列程式碼複製到 .csx 檔案中（這會取代既有的程式碼）。 若要這麼做，請移至您的 Azure Functions 應用程式，並選取左側的計時器觸發程式函式。

![Azure 入口網站中的 Azure 函式 .csx](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> 針對您要使用的端點位址： `EndpointAddress= https://dc.services.visualstudio.com/v2/track`。 除非您的資源位於 Azure Government 或 Azure 中國之類的區域，否則請參閱這篇文章，以瞭解如何覆[寫預設端點](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification)，並為您的區域選取適當的遙測通道端點。

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

在右側的 [查看檔案] 底下，選取 [**新增**]。 使用下列設定呼叫新的 file **function。**

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![在右側選取 [新增]。 將檔案命名為 proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>檢查可用性

為確保所有專案都正常運作，您可以在 Application Insights 資源的 [可用性] 索引標籤中查看圖形。

![成功結果的 [可用性] 索引標籤](media/availability-azure-functions/availtab.png)

當您使用 Azure Functions 設定測試時，您會注意到，不同于在 [可用性] 索引標籤中使用 [**加入測試**]，您的測試名稱將不會出現，而且您將無法與其互動。 結果會視覺化，但您會看到摘要視圖，而不是您透過入口網站建立可用性測試時所取得的相同詳細觀點。

若要查看端對端交易詳細資料，請在 [深入探索] 下選取 [**成功**] 或 [**失敗**]，然後選取一個範例。 您也可以選取圖表上的資料點，以取得端對端交易詳細資料。

![選取簡易的可用性測試](media/availability-azure-functions/sample.png)

![端對端交易詳細資料](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>記錄中的查詢（分析）

您可以使用記錄（分析）來查看可用性結果、相依性等。 若要深入瞭解記錄，請造訪[記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。

![可用性結果](media/availability-azure-functions/availabilityresults.png)

![相依性](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>後續步驟

- [應用程式對應](../../azure-monitor/app/app-map.md)
- [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
