---
title: Azure Application Insights 中的遙測取樣 | Microsoft Docs
description: 如何讓遙測量保持在控制下。
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: d88de2bf660165022b39aaa0321ff5c62ea81cd3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231840"
---
# <a name="sampling-in-application-insights"></a>Application Insights 中的取樣

取樣是 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 中的一個功能。 若要既能減少遙測流量和儲存空間，又能保有應用程式資料在統計上的正確分析，便建議使用此方法。 篩選器會選取相關的項目，以便您在進行診斷調查時瀏覽各個項目。
當入口網站中呈現度量計數時，它們會來重新標準化考慮到取樣。 如此一來，最小化對統計資料產生任何影響。

取樣可減少流量與資料成本，而且可以協助您避免節流。

## <a name="in-brief"></a>簡單地說︰

* 取樣會保留「n」  筆記錄的其中 1 筆，並捨棄其餘部分。 比方說，它可能會保留一中五個事件，取樣率為 20%。 
* 在 ASP.NET 和 ASP.NET Core 軟體開發套件 (Sdk) 的所有最新版本的預設會啟用調適型取樣。
* 您也可以設定手動取樣。 這可以在 設定在入口網站*使用量和估計的成本頁面*，在 ASP.NET SDK 中的 ApplicationInsights.config 檔案中，在 ASP.NET Core SDK，透過程式碼，或在 Java SDK 的 applicationinsights.xml 檔案。
* 如果您記錄自訂事件和確保一組事件，可保留或一起捨棄，事件必須有相同的 OperationId 值。
* 每個記錄的 `itemCount` 屬性中都會回報取樣除數 *n*，此屬性在 [搜尋] 中會出現在「要求計數」或「事件計數」等易記名稱之下。 `itemCount==1`當取樣不在作業中。
* 如果您要撰寫分析查詢，請 [考慮到取樣](../../azure-monitor/log-query/aggregations.md)。 特別是，您應該使用 `summarize sum(itemCount)`，而非只計算記錄。

## <a name="types-of-sampling"></a>取樣類型

有三個替代的取樣方法：

* **調適型取樣**自動調整的從 ASP.NET/ASP.NET 核心應用程式中 SDK 傳送的遙測量。 這是從 ASP.NET Web SDK v 2.0.0-beta3 及更新版本和 Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 及更新版本的預設取樣。  調適型取樣目前僅供 ASP.NET 伺服器端遙測使用。

* **固定取樣率**減少的從您 ASP.NET 或 ASP.NET Core 或 Java 的伺服器和使用者的瀏覽器傳送的遙測量。 而比例則由您設定。 用戶端和伺服器會同步處理它們的取樣，讓您可以在 [搜尋] 終於相關的頁面檢視和要求之間瀏覽。

* **擷取取樣**在 Azure 入口網站中運作。 它會根據您設定的取樣比例，捨棄來自您應用程式的一些遙測。 這不會減少從應用程式傳送的遙測流量，但可協助您讓流量不要超過每月配額。 擷取取樣的主要優點是，您還可以設定的取樣率，而不必重新部署您的應用程式。 擷取取樣的運作方式都一致的所有伺服器和用戶端。

如果自適性或固定速率取樣正在作業中，則會停用擷取取樣。


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>ASP.NET/ASP.NET Core Web 應用程式中的調適性取樣

調適型取樣是適用於 Application Insights SDK for ASP.NET v 2.0.0-beta3 和更新版本，Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 和更新版本，而且預設會啟用。

調適性取樣會影響從您的 Web 伺服器應用程式傳送給 Application Insights 服務端點的遙測量。 磁碟區中指定的最大速率的流量，讓自動調整，並透過設定控制`MaxTelemetryItemsPerSecond`。 如果應用程式產生的遙測，很少，例如，只要磁碟區是下面項目進行偵錯時或由於低使用量，將不會取得取樣`MaxTelemetryItemsPerSecond`。 遙測資料增加而增加的磁碟區，以便達成目標磁碟區藉由調整取樣率。

為了要讓遙測量達到目標，系統會捨棄部分已產生的遙測。 但就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。

度量計量 (例如要求率及例外狀況率) 會受到調整來補償取樣率，讓它們能在計量瀏覽器中顯示大致上正確的值。

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>設定 ASP.NET 應用程式的調適性取樣

[了解](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)需設定 ASP.NET Core 應用程式的調適型取樣。 

在 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 中，您可以調整 `AdaptiveSamplingTelemetryProcessor` 節點中的數個參數。 顯示的數字是預設值：

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    調整演算法對於 **每部伺服器主機**的目標速率。 如果 Web 應用程式在許多主機上執行，請減少此值，以保持在您的 Application Insights 入口網站的流量目標速率內。
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    在目前的遙測速率會重新評估間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    當取樣百分比值變更時，多久之後我們可以降低取樣百分比，以擷取較少的資料。
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    當取樣百分比值變更時，多久之後我們可以增加取樣百分比，以擷取較多的資料。
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    隨著取樣百分比改變，我們可以設定的最小值是多少。
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    隨著取樣百分比改變，我們可以設定的最大值是多少。
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    在計算移動平均時，指派給最新的值的權數。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    當應用程式剛開始時指派的值。 當您偵錯時，就不減少值。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    不要進行取樣的分號分隔類型清單。 可辨識的類型為：相依性、事件、例外狀況、頁面檢視、要求、追蹤。 會傳送所指定類型的所有執行個體；會針對未指定的類型進行取樣。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    要進行取樣的分號分隔類型清單。 可辨識的類型為：相依性、事件、例外狀況、頁面檢視、要求、追蹤。 會針對指定的類型進行取樣；將一律會傳輸其他類型的所有執行個體。


**若要關閉**調適性取樣，從 applicationinsights-config 移除在 AdaptiveSamplingTelemetryProcessor 節點。

### <a name="alternative-configure-adaptive-sampling-in-code"></a>替代方法：在程式碼中設定調適性取樣

除了在 .config 檔中設定取樣參數之外，您還可以透過程式設計的方式來設定這些值。

1. 移除所有`AdaptiveSamplingTelemetryProcessor`.config 檔案中的節點。
2. 您可以使用下列程式碼片段來設定調適型取樣。

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([深入了解遙測處理器](../../azure-monitor/app/api-filtering-sampling.md#filtering))。

您也可以個別調整每個遙測類型取樣率，或甚至可以從取樣完全排除特定類型。 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>設定 ASP.NET Core 應用程式的調適型取樣。

沒有任何`ApplicationInsights.Config`ASP.NET Core 應用程式，因此每個組態會透過程式碼。
所有的 ASP.NET Core 應用程式預設會啟用調適型取樣。 您可以停用或自訂取樣行為。

### <a name="turning-off-adaptive-sampling"></a>關閉調適性取樣

可以停用的方法中加入 Application Insights 服務時的預設取樣功能```ConfigureServices```，並使用```ApplicationInsightsServiceOptions```內`Startup.cs`檔案：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

上述程式碼將會停用取樣功能。 請遵循下列步驟使用更多自訂選項新增取樣。

### <a name="configure-sampling-settings"></a>進行取樣設定

如下所示，使用 ```TelemetryProcessorChainBuilder``` 的擴充方法自訂取樣行為。

> [!IMPORTANT]
> 如果您使用此方法來設定取樣，請務必對於 AddApplicationInsightsTelemetry() 使用 use aiOptions.EnableAdaptiveSampling = false; 設定。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

    var builder = configuration .TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**如果使用上述的方法來設定取樣，請務必使用```aiOptions.EnableAdaptiveSampling = false;```AddApplicationInsightsTelemetry() 設定。**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>ASP.NET、 ASP.NET Core 和 Java 網站的固定速率取樣

固定取樣率會減少從您的 Web 伺服器及網頁瀏覽器所傳送的流量。 但它會依照您設定的速率來降低遙測，這與調適性取樣不同。 它也會同步處理用戶端及伺服器取樣，讓相關項目能夠保留；舉例來說，當您在 [搜尋] 中查看頁面檢視時，可以尋找其相關要求。

像其他的取樣技巧，這也會保留相關項目。 對於每個 HTTP 要求事件，該要求及其相關事件會一併遭到捨棄或傳輸。

在計量瀏覽器中，速率 (例如要求及例外狀況數) 會乘以某個係數來補償取樣率，讓它們能大致上正確。

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>在 ASP.NET 中設定固定速率取樣

1. **停用調適性取樣**：在 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 中，將 `AdaptiveSamplingTelemetryProcessor` 節點移除或設成註解。

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **啟用固定取樣率模組。** 將此程式碼片段新增至 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)：
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>替代方法：在伺服器程式碼中啟用固定取樣率
    
    除了在 .config 檔中設定取樣參數之外，您還可以透過程式設計的方式來設定這些值。 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([深入了解遙測處理器](../../azure-monitor/app/api-filtering-sampling.md#filtering))。

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>在 ASP.NET Core 中設定固定速率取樣

1. **停用調適性取樣**：在方法中進行變更```ConfigureServices```，並使用```ApplicationInsightsServiceOptions```:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **啟用固定取樣率模組。** 在方法中進行變更```Configure```如以下程式碼片段中所示：

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>在 JAVA 中設定固定速率取樣 ###

1. 使用最新的 [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md) 下載及設定您的 Web 應用程式

2. 將下列程式碼片段加入 ApplicationInsights.xml 檔案以**啟用固定速率取樣模組**。

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. 您可以在 Processor 標籤 "FixedRateSamplingTelemetryProcessor" 中使用下列標籤，在取樣中包括 (Include) 或排除 (Exclude) 特定的遙測類型
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

可以包含在程式碼中或從取樣排除的遙測類型︰相依性、 事件、 例外狀況、 PageView、 要求和追蹤。

> [!NOTE]
> 針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。  目前取樣並不支援其他值。
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>擷取取樣

這種形式的取樣會在來自您 Web 伺服器、瀏覽器及裝置的遙測抵達 Application Insights 服務端點時開始執行。 雖然它不會減少來自您應用程式的遙測流量，但會減少 Application Insights 所處理及保留 (並收費) 的遙測量。

如果您的應用程式通常會超過每月配額，且您無法選擇使用任何一種 SDK 式的取樣類型，請使用這種類型的取樣。 

在 [使用量和估計成本] 頁面中設定取樣率：

![在 [應用程式概觀] 刀鋒視窗中，依序按一下 [設定]、[配額]、[範例]，然後選取某個取樣率，並按一下 [更新]。](./media/sampling/04.png)

就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。 度量計量 (例如要求率及例外狀況率) 會正確地保留。

遭到取樣捨棄的資料點將無法在任何 Application Insights 功能中使用，例如 [連續匯出](../../azure-monitor/app/export-telemetry.md)。

進行 SDK 自適性或固定速率取樣時，不執行擷取取樣。 當 ASP.NET/ASP.NET Core SDK 已啟用 Visual Studio 中或已啟用 Azure Web 應用程式擴充功能中，或使用狀態監視器，且已停用擷取取樣，則預設會啟用調適型取樣。 如果 SDK 取樣率小於 100%（也就是 項目會取樣） 則會忽略您設定擷取取樣率。

> [!WARNING]
> 圖格上顯示的值會指出您要為擷取取樣的值。 如果 SDK 取樣在運作中，這並不代表實際的取樣率。
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>具有 JavaScript 的網頁的取樣
您可以從任何伺服器設定固定取樣率的網頁。 

當您[設定 Application Insights 的網頁](../../azure-monitor/app/javascript.md)時，請修改您從 Application Insights 入口網站取得的 JavaScript 片段。 (在 ASP.NET 應用程式中，程式碼片段通常會出現在 _Layout.cshtml。)在檢測金鑰之前插入類似 `samplingPercentage: 10,` 的一行：

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。  目前取樣並不支援其他值。

如果您也在伺服器啟用固定比例取樣，用戶端和伺服器就會同步，讓您可以在 [搜尋] 中的相關頁面檢視與要求之間瀏覽。

## <a name="when-to-use-sampling"></a>何時使用取樣？

調適性取樣會自動啟用最新的.NET 和.NET Core Sdk 中。 無論您使用哪個版本的 SDK，都可以啟用擷取取樣，允許 Application Insights 對收集的資料取樣。

預設 Java SDK 中不會啟用任何取樣。 目前，它只支援固定速率取樣。 Java SDK 中不支援調適型取樣。

通常，對於大多數小型和中型的應用程式，您不需要取樣。 最有用的診斷資訊和最準確的統計資料會是透過收集所有使用者活動的資料取得。 

取樣的主要優點如下：

* 當您的應用程式在短時間間隔傳送非常高比率的遙測時，Application Insights 服務會將資料點卸除 (「節流」)。 
* 保持在定價層的資料點 [配額](../../azure-monitor/app/pricing.md) 內。 
* 若要從收集的遙測降低網路流量。 

### <a name="which-type-of-sampling-should-i-use"></a>我應該使用哪種類型的取樣？

**如果是下列情形，請使用擷取取樣：**

* 您的遙測經常會超出每月配額。
* 您使用不支援取樣的 SDK 版本，例如比 ASP.NET 版本 2 早的版本。
* 您收到太多的遙測資料從使用者的網頁瀏覽器。

**如果是下列情形，則使用固定取樣率：**

* 您使用 Application Insights SDK for ASP.NET Web 服務版本 2.0.0 或更新版本或 Java SDK v2.0.1 或更新版本，且
* 您想要同步處理用戶端與伺服器之間的取樣，因此，當您在 [搜尋](../../azure-monitor/app/diagnostic-search.md)中調查事件時，您可以在用戶端與伺服器的相關事件之間調查，例如頁面檢視和 HTTP 要求。
* 您對於您的應用程式的適當取樣百分比有信心。 應該夠高以取得精確的度量，但是低於超過價格配額和節流限制的取樣率。 

**使用調適性取樣：**

如果欲使用其他形式取樣但條件不適用，建議使用適應型取樣。 ASP.NET/ASP.NET 核心伺服器 SDK 中的預設會啟用此設定。 在達到一定的比率下限之前，不會減少流量，因此低使用率的網站不會受到影響。

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>如何得知取樣是否正在運作中？

若要找出實際的取樣率 (不論是否已套用)，請使用如下所示的 [分析查詢](../../azure-monitor/app/analytics.md) ︰

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

如果小於 100 的任何類型的 RetainedPercentage，該項目是所取樣。

**Application Insights 不會對取樣工作階段中，度量和效能計數器遙測類型，在上述任何取樣技巧。降低有效位數可以是這些遙測類型非常不想要為這些型別一律會從取樣排除**

## <a name="how-does-sampling-work"></a>取樣運作方式？

固定速率取樣是從 ASP.NET 版本 2.0.0 的 SDK 和 Java SDK 版本 2.0.1 及更新版本開始有的功能。 調適型取樣是 ASP.NET 版本 (從 2.0.0 及更新版本開始) 中 SDK 的一個功能。 擷取取樣是 Application Insights 服務的一項功能，而且可在 SDK 未執行取樣時運作。

採樣演算法會決定要捨棄哪些遙測項目，以及要保留哪些遙測項目 (其是否位於 SDK 或 Application Insights 服務中)。 取樣決策會根據數個規則，目標是要保留相關的資料點不變，在 Application Insights 中保有可採取動作而且即使有縮減資料集仍可靠的診斷經驗。 比方說，如果是失敗的要求，您的應用程式會傳送其他遙測項目 (例如從此要求記錄的例外狀況和追蹤)，取樣將不會分割此要求和其他遙測。 它會一起保留或卸除。 如此一來，當您在 Application Insights 中查看要求詳細資料時，您一律可以看到要求和其相關聯的遙測項目。 

取樣決策根據要求，這表示屬於特定作業的所有遙測項目是可加以保留或卸除作業識別碼。 沒有作業遙測項目識別碼集 （適用於使用任何 http 內容的非同步執行緒報告範例遙測項目） 取樣只會擷取每種類型的遙測項目的百分比表示。 .NET sdk 的 2.5.0-beta2 和 ASP.NET Core sdk 2.2.0-beta3 之前，取樣決策根據定義 「 使用者 」 的應用程式的使用者識別碼的雜湊 (也就是最常見的 web 應用程式)。 類型未定義的使用者 （例如 web 服務） 的應用程式的取樣決策根據要求的作業識別碼。

呈現遙測回來給您時，Application Insights 服務會以收集時使用的相同取樣百分比調整度量，來彌補遺漏的資料點。 因此，在 Application Insights 中查看遙測時，使用者會看到統計正確也非常接近實際數的近似值。

近似值的精確度絕大部分取決於設定的取樣百分比。 此外，對於處理大量使用者的通常類似要求的應用程式，其精確度會增加。 相反地，對於不處理大量負載的應用程式，就不需要取樣，因為這些應用程式通常可以傳送遙測同時保持在配額內，而不會因節流造成資料遺失。 

> [!WARNING]
> Application Insights 不會對計量和工作階段遙測類型進行取樣。 這些遙測類型非常不想要降低精確度。
> 

### <a name="adaptive-sampling"></a>調適性取樣

調適性取樣會新增元件，該元件會監視 SDK 的目前傳輸速率，並調整取樣百分比，以嘗試保持在目標最大速率。 調整會定期重新計算，並且根據外寄傳輸速率的移動平均。

## <a name="sampling-and-the-javascript-sdk"></a>取樣與 JavaScript SDK

用戶端 (JavaScript) SDK 與伺服器端 SDK 一同參與固定取樣率。 已檢測的頁面只會從伺服器端決定「納入取樣」的相同使用者傳送用戶端遙測。 此邏輯的設計是為了在用戶端和伺服器端之間保有使用者工作階段的完整性。 如此一來，您可以從 Application Insights 中的任何特定遙測項目找到這個使用者或工作階段的所有其他遙測項目。 

*我的用戶端和伺服器端遙測未顯示您上方描述的協調範例。*

* 請確認您在伺服器及用戶端上都已啟用固定取樣率。
* 確定 SDK 版本為 2.0 或更新版本。
* 請檢查您的用戶端和伺服器中設定相同的取樣百分比。

## <a name="frequently-asked-questions"></a>常見問題集

*什麼是 ASP.NET 和 ASP.NET Core SDK 的預設取樣行為？*

* 如果您使用其中一個上述 SDK 的最新版本，具有五個遙測項目，每秒的預設會啟用調適型取樣。
  有 2 個 AdaptiveSamplingTelemetryProcessors 加入預設的情況下，其中包括事件類型中的取樣，並另從取樣排除的事件類型。 此設定表示 SDK 會嘗試限制五個遙測項目之事件類型的遙測項目和所有其他類型的組合，藉此確保從其他遙測類型取樣事件是個別的五個遙測項目。 事件通常用於商務遙測，很可能應該不會受到診斷遙測的磁碟區。
  
  以下顯示產生的預設 ApplicationInsights.Config 檔案。 如所述，有兩個不同的 AdaptiveSamplingTelemetryProcessor 節點加入，一個不含事件類型和另一個包含它。 ASP.NET Core 中的程式碼中已啟用完全相同的預設行為。 使用文件的前一節的範例，若要變更此預設行為。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*可以遙測取樣一次以上嗎？*

* 沒有。 SamplingTelemetryProcessors 略過從取樣的考量，如果項目已經在取樣的項目。 也是如此，這不會套用取樣已取樣的 SDK 本身中的項目為擷取取樣。 '

*為什麼不取樣簡單的「收集每個遙測類型百分之 X」？*

* 雖然這個取樣方法會提供高層級的精確度的度量近似值，它會破壞相互關聯每個使用者、 工作階段，以及要求，也就是很重要的診斷資料的能力。 因此，對於「收集應用程式使用者百分之 X 的所有遙測項目」或「收集應用程式要求百分之 X 的所有遙測」邏輯，取樣的效果更佳。 對於不相關聯 （例如背景非同步處理） 的要求遙測項目，此後援是 「 收集百分之 X 的每個遙測類型的所有項目。 」 

*取樣百分比會隨著時間變更嗎？*

* 是的，調適性取樣會根據目前觀察到的遙測量，逐漸變更取樣百分比。

*如果我使用固定取樣率，如何知道哪個取樣百分比最適合我的應用程式？*

* 開始使用調適性取樣的其中一個方法，就是找出它選擇的取樣率 (請參閱上一個問題)，然後再切換為使用該取樣率的固定取樣率。 
  
    否則，您就必須猜測。 分析 Application Insights 中您目前的遙測使用量、觀察目前的節流，並估計所收集之遙測的量。 這三項輸入與所選定價層，可對您可能想要減少收集的遙測量提出建議。 不過，使用者數目的增加或遙測量的其他某些變化可能會讓您的評估失效。

*如果將取樣百分比設定成太低會發生什麼事？*

* 當 Application Insights 嘗試補償減少資料量縮減的資料視覺效果時，過度低的取樣百分比 (過度積極取樣) 會降低近似值的精確度。 此外，診斷經驗可能會有負面影響，因為可能會出取樣出某些不常失敗或緩慢的要求。

*如果將取樣百分比設定成太高會發生什麼事？*

* 設定太高的取樣百分比 (不夠積極) 會導致收集的遙測量減少不足。 您可能仍會遇到與節流相關的遙測資料遺失，而使用 Application Insights 的成本由於超額費可能高於您的計劃。

*我可以在何種平台上使用取樣？*

* 如果 SDK 未執行取樣，則擷取取樣會在任何遙測超過特定數量時自動運作。 此組態會運作，比方說，如果您使用舊版 ASP.NET SDK 或舊版 Java SDK(1.0.10 or before)。
* 如果您使用 ASP.NET SDK 版本 2.0.0 和更新版本或 ASP.NET CORE SDK 版本 2.2.0 版和更新版本 （裝載於 Azure 或您自己的伺服器上），您會得到調適性取樣預設情況下，但是上面所述，您可以切換到固定速率。 使用固定取樣率，瀏覽器 SDK 會自動同步至取樣相關的事件。 
* 如果您使用 Java SDK 版本 2.0.1 或更新版本中，您可以設定 ApplicationInsights.xml 來開啟固定速率取樣。 根據預設取樣功能為關閉。 使用固定取樣率，瀏覽器 SDK 會自動同步至取樣相關的事件。

*我一律想要看見特定罕見的事件。我要如何讓它們通過取樣模組？*

* 若要達到此目的，最好是撰寫自訂[TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering)，集合`SamplingPercentage`100 的遙測項目上要保留的如下所示。 這可確保所有的取樣技巧將會忽略這個項目，從任何取樣的考量。

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>後續步驟

* [篩選](../../azure-monitor/app/api-filtering-sampling.md) 可以對您的 SDK 所傳送的內容，提供更嚴格的控制。