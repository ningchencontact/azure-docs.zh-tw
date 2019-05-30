---
title: Azure Application Insights 中的遙測通道 |Microsoft Docs
description: 如何自訂 Azure Application Insights Sdk 中的遙測通道.NET/.NET core。
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255815"
---
# <a name="telemetrychannel-in-application-insights"></a>在 Application Insights 中的 TelemetryChannel

TelemetryChannel 是不可或缺的一部分[Azure Application Insights Sdk](../../azure-monitor/app/app-insights-overview.md)。 它會管理緩衝處理及傳輸遙測資料至 Application Insights 服務。 .NET 和.NET Core Sdk 的版本有兩個內建 TelemetryChannels-`InMemoryChannel`和`ServerTelemetryChannel`。 本文說明每個通道，在 詳細資料，包括使用者可以如何自訂通道的行為。

## <a name="what-is-a-telemetrychannel"></a>什麼是 TelemetryChannel？

`TelemetryChannel` 負責緩衝處理，並將遙測項目傳送至 Application Insights 服務儲存的查詢和分析位置。 它是任何實作介面的類別 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

`Send(ITelemetry item)` TelemetryChannel 方法稱為畢竟`TelemetryInitializer`s 和`TelemetryProcessor`稱為 s。 這表示，來卸除任何項目`TelemetryProcessor`不會到達通道。 `Send()` 不會不通常立即將傳送的項目到後端。 他們通常緩衝在記憶體中，以進行有效率的傳輸批次方式傳送。

[LiveMetrics](live-stream.md)也有提供遙測資料的即時資料流的自訂通道。 此通道是獨立的一般遙測通道，以及本文件不適用於所使用的通道`LiveMetrics`。

## <a name="built-in-telemetrychannels"></a>內建 TelemetryChannels

Application Insights.NET/.NET Core SDK 隨附兩個內建的通道：

* **InMemoryChannel** 
 `InMemoryChannel`是輕量級通道，緩衝在記憶體中的項目，直到傳送為止。 項目會在記憶體中緩衝處理和排清之後每隔 30 秒，或每當 500 個項目有經過緩衝處理。 因為它不會重試傳送的遙測，在失敗時，此通道會提供最少的可靠性保證。 此通道不會在磁碟上，保留項目，因此 （依正常程序或停用），會永久地在應用程式關閉時遺失任何未傳送的項目。 沒有`Flush()`此通道，可以用來強制排清記憶體中的遙測中的任何項目以同步方式所實作的方法。 這非常適合用於短期執行的應用程式和同步排清適合的位置。

    此通道隨附於`Microsoft.ApplicationInsights`nuget 封裝本身，並會進行任何其他的設定時，SDK 會使用預設通道。

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel`是更進階的通道，其中具有重試原則和本機磁碟上儲存資料的功能。 如果發生暫時性錯誤，此通道重試傳送的遙測。 這個通道也會使用本機磁碟儲存體磁碟上保留的項目，在網路中斷或高的遙測資料的磁碟區。 因為這些重試機制和本機磁碟儲存體中，此通道會被視為更可靠，而且建議針對所有實際執行案例。 此通道是預設值[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)並[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)應用程式，根據連結的官方文件設定。此通道最適合的長時間執行的處理序伺服器案例。 [ `Flush()` ](#which-channel-should-i-use)此通道所實作的方法不同步。

    此通道隨附為 NuGet 套件`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`，並使用其中一個 NuGet 套件時自動帶入`Microsoft.ApplicationInsights.Web`或`Microsoft.ApplicationInsights.AspNetCore`。

## <a name="configuring-telemetrychannel"></a>設定 TelemetryChannel

您可以設定所需設定遙測通道`TelemetryChannel`在使用`TelemetryConfiguration`。 Asp.Net 應用程式，設定牽涉到設定`TelemetryChannel`上`TelemetryConfiguration.Active`，或修改`ApplicationInsights.config`。 對於 ASP.NET Core 應用程式，設定牽涉到將所需的通道加入至相依性插入容器。

下圖顯示範例設定使用者的位置`StorageFolder`通道。 `StorageFolder` 只是可設定的設定。 描述組態設定的完整清單[在 [設定] 區段中](telemetry-channels.md#configurable-settings-in-channel)。

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>使用 ApplicationInsights.Config ASP.NET 應用程式的組態

下一節[ApplicationInsights.config](configuration-with-applicationinsights-config.md)顯示設有 ServerTelemetryChannel`StorageFolder`設定自訂的位置。

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 應用程式的程式碼中設定

下列程式碼設定與 ServerTelemetryChannel`StorageFolder`設定自訂的位置。 此程式碼應該將應用程式，通常是在中的 application_start （） 方法的開頭 `Global.aspx.cs`

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的程式碼中設定

修改`ConfigureServices`方法的`Startup.cs`類別，如下所示。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> 請務必請注意，設定的通道使用`TelemetryConfiguration.Active`不建議用於 ASP.NET Core 應用程式。

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>程式碼中設定 TelemetryChannel.NET/.NET Core 主控台應用程式

針對主控台應用程式，程式碼會是相同的.NET 和.NET Core，並如下所示。

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的作業詳細資料

`ServerTelemetryChannel`緩衝在記憶體中緩衝區的抵達的項目。 序列化、 壓縮，並儲存到`Transmission`之後每隔 30 秒，或當 500 個項目會進行緩衝處理的執行個體。 單一`Transmission`執行個體包含最多 500 個項目，而且會表示透過 Application Insights 服務的單一 https 呼叫所傳送的遙測批次。 根據預設，可以有最多 10 個`Transmission`s 傳送以平行方式。 如果遙測抵達更快的速度，或如果網路/Application Insights 後端是速度慢，則`Transmission`取得儲存到記憶體。 這個記憶體中傳輸緩衝區的預設容量是 5 MB。 一旦超過記憶體容量，`Transmission`會儲存在本機磁碟上最多 50 mb。 `Transmission`會儲存在本機磁碟上有網路問題時。 只有儲存在本機的磁碟中的項目存留應用程式當機，這會傳送一次啟動應用程式時。

## <a name="configurable-settings-in-channel"></a>頻道中可設定的設定

可設定的設定，每個通道的完整清單如下：

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

最常用設定`ServerTelemetryChannel`如下所示：

1. `MaxTransmissionBufferCapacity` -最大數量的記憶體，以位元組為單位，到緩衝區在記憶體中的傳輸通道所使用的詳細資訊。 一旦到達此容量時，會直接到本機磁碟儲存新的項目。 預設值是 5 MB。 設定較高的值較小的磁碟使用量，但它的潛在客戶請務必請注意，是否應用程式當機，在記憶體中的項目將會遺失。

2. `MaxTransmissionSenderCapacity` -最大數量`Transmission`，它會傳送至 Application Insights，在相同的時間。 預設值為 10，但它可以設定較高的數目。 這被建議產生大量遙測時，通常在執行負載測試和/或取樣關閉時。

3. `StorageFolder` 通道用來儲存至磁碟所需的項目-資料夾。 在 Windows，%localappdata%或 %temp%已使用，如果沒有明確設定。 在非 Windows 環境中，使用者**必須**設定有效的位置，如果沒有這些遙測資料不會儲存到本機磁碟。

## <a name="which-channel-should-i-use"></a>我應該使用哪個通道？

`ServerTelemetryChannel` 建議針對大部分的實際執行案例的長時間執行的應用程式。 `Flush()`方法實作`ServerTelemetryChannel`不是同步的與`Flush()`並不保證記憶體/磁碟從傳送所有擱置中的項目。 如果此通道會用於的案例，其中的應用程式即將關閉，則建議執行之後呼叫的一些延遲`Flush()`此通道上。 延遲所需的確切量不是可預測的因為這取決於因素，例如多少項目或`Transmissions`會在記憶體中，多少位於磁碟、 多少已傳送到受支援，而且如果通道是指數退避法案例的中間。 如果要同步的排清，則需要`InMemoryChannel`建議。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Application Insights 通道提供保證的遙測傳送] 或 [可能遺失遙測的案例是什麼？*

* 簡短的答案是沒有任何內建的通道提供的相關遙測傳送至後端的交易類型保證。 雖然`ServerTelemetryChannel`更進階的相較於`InMemoryChannel`可靠的遙測傳遞，也可以傳送遙測資料的最佳嘗試，以及遙測可能仍會遺失幾個案例中。 其中是遺失遙測的常見案例包括：

1. 每當應用程式當機，會遺失記憶體中的項目。
1. 在網路中斷或 Application Insights 後端的問題，遙測會儲存到本機磁碟。 不過，早於 24 小時內的項目都會被捨棄。 因此遙測的網路問題的擴充期間會遺失。
1. 將遙測資料儲存在 Windows 中的預設磁碟位置是 %localappdata%或 %temp%。 這些位置是通常是本機電腦。 如果應用程式移轉實體從一個位置之間，儲存在這個位置的任何遙測將會遺失。
1. 在 Azure Web 應用程式 (Windows)，預設的磁碟位置會是"D:\local\LocalAppData 」。 此位置不會持續保存，並抹除應用程式重新啟動中、 小數位數，依此類推，輸出導致遺失儲存在這些位置中的遙測。 使用者可以覆寫儲存到永續性的位置，例如 「 D:\home"，但這些持續性的位置下面會提供遠端存放區，並可能會很慢。

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*運作 ServerTelemetryChannel 非 Windows 系統中？*

* 正在 WindowsServer 套件/命名空間的名稱，此通道被支援在非 Windows 系統中發生下列例外狀況。 在非 Windows，通道不會預設建立的本機儲存體資料夾。 使用者必須建立本機儲存體資料夾，並設定要使用的通道。 一旦設定本機儲存體，則通道運作方式相同 Windows 和非 Windows 系統中。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*SDK 不會建立暫存本機儲存體？在儲存體加密的資料嗎？*

* SDK 會將遙測項目儲存在本機儲存體中，期間節流或網路問題的期間。 這項資料不會在本機加密。
適用於 Windows 系統，SDK，自動在 TEMP 或 APPDATA 目錄中，建立暫存的本機資料夾，並限制系統管理員 」 和 「 目前使用者的存取。
針對非 Windows，任何本機儲存體 sdk，會自動建立，並因此沒有資料會儲存在本機的預設值。 使用者可以自己，建立儲存體目錄，並設定要使用的通道。 在此情況下，使用者會負責確保此目錄安全的。
深入了解[資料保護和隱私權](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)。

## <a name="open-source-sdk"></a>開放原始碼 SDK
每個 Application Insights Sdk 中，例如通道也是開放原始碼。 讀取和貢獻程式碼，或回報問題[官方 GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-dotnet)。

## <a name="next-steps"></a>後續步驟

* [取樣](../../azure-monitor/app/sampling.md)
* [SDK 進行疑難排解](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
