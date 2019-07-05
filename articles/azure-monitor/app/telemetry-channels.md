---
title: Azure Application Insights 中的遙測通道 |Microsoft Docs
description: 如何自訂遙測通道，在 Azure Application Insights Sdk for.NET 和.NET Core。
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
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561355"
---
# <a name="telemetry-channels-in-application-insights"></a>在 Application Insights 的遙測通道

遙測通道是不可或缺的一部分[Azure Application Insights Sdk](../../azure-monitor/app/app-insights-overview.md)。 他們管理緩衝處理及傳輸遙測資料至 Application Insights 服務。 .NET 和.NET Core Sdk 的版本有兩個內建遙測通道：`InMemoryChannel`和`ServerTelemetryChannel`。 本文說明每個通道，在 詳細資料，包括如何自訂通道的行為。

## <a name="what-are-telemetry-channels"></a>遙測通道有哪些？

遙測通道負責緩衝遙測項目，並傳送至 Application Insights 服務，它們用來儲存的查詢和分析。 遙測通道是實作任何類別都[ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)介面。

`Send(ITelemetry item)`之後所有遙測初始設定式會呼叫方法的遙測通道，稱為遙測處理器。 因此，任何遙測處理器來卸除的項目不會到達通道。 `Send()` 不通常將項目傳送至後端立即。 一般而言，它在記憶體中緩衝處理它們，並將它們以進行有效率的傳輸批次方式傳送。

[即時計量 Stream](live-stream.md)也有提供遙測資料的即時資料流的自訂通道。 此通道是獨立的一般遙測通道，以及這份文件不會套用到它。

## <a name="built-in-telemetry-channels"></a>內建遙測通道

Application Insights.NET 和.NET Core Sdk 隨附兩個內建的通道：

* `InMemoryChannel`:緩衝在記憶體中的項目，直到它們會被傳送的輕量級通道。 項目會在記憶體中緩衝處理和排清之後每隔 30 秒，或每當 500 個項目會進行緩衝處理。 因為它不會將遙測傳送重試失敗後，此通道會提供最少的可靠性保證。 這個通道也不會讓項目在磁碟上，因此會永久地在應用程式關閉時遺失任何未傳送的項目 (依正常程序與否)。 此通道會實作`Flush()`可以用來強制排清記憶體中的遙測中的任何項目同步的方法。 此通道非常適合用於短期執行的應用程式和同步排清適合的位置。

    此通道是較大的 Microsoft.ApplicationInsights NuGet 套件的一部分，SDK 會使用任何其他的設定時的預設通道。

* `ServerTelemetryChannel`:更進階的通道可重試原則和本機磁碟上儲存資料的功能。 如果發生暫時性錯誤，此通道重試傳送遙測。 這個通道也會使用本機磁碟儲存體磁碟上保留的項目，在網路中斷或高的遙測資料的磁碟區。 因為這些重試機制和本機磁碟儲存體中，此通道會被視為更可靠，而且建議針對所有實際執行案例。 此通道是預設值[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)並[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)所根據的官方文件中設定應用程式。 此通道最適合與長時間執行處理序伺服器案例。 [ `Flush()` ](#which-channel-should-i-use)未同步此通道所實作的方法。

    此通道 Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet 套件的形式隨附，以及當您使用 Microsoft.ApplicationInsights.Web 或 Microsoft.ApplicationInsights.AspNetCore NuGet 會自動取得封裝。

## <a name="configure-a-telemetry-channel"></a>設定遙測通道

您可以將它設定為使用中的遙測組態設定遙測通道。 ASP.NET 應用程式，設定牽涉到將遙測通道執行個體設定為`TelemetryConfiguration.Active`，或藉由修改`ApplicationInsights.config`。 對於 ASP.NET Core 應用程式，設定牽涉到將通道新增至相依性插入容器。

下列各節顯示的設定範例`StorageFolder`設定中各種應用程式類型的通道。 `StorageFolder` 只是可設定的設定。 組態設定的完整清單，請參閱[[設定] 區段](telemetry-channels.md#configurable-settings-in-channels)本文稍後。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>透過 ApplicationInsights.config 的 ASP.NET 應用程式的組態

下一節[ApplicationInsights.config](configuration-with-applicationinsights-config.md)示範`ServerTelemetryChannel`通道設有`StorageFolder`設定自訂位置：

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>在 ASP.NET 應用程式的程式碼中的組態

下列程式碼會設定具有 'ServerTelemetryChannel' 執行個體`StorageFolder`設定自訂的位置。 加入這個程式碼的應用程式時，開頭通常`Application_Start()`中的 Global.aspx.cs 方法。

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的程式碼中設定

修改`ConfigureServices`方法的`Startup.cs`類別如下所示：

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

> [!IMPORTANT]
> 使用設定通道`TelemetryConfiguration.Active`不建議用於 ASP.NET Core 應用程式。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 主控台應用程式的程式碼中設定

針對主控台應用程式，程式碼也適用於.NET 和.NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的作業詳細資料

`ServerTelemetryChannel` 到達記憶體緩衝區中的項目存放區。 項目會序列化、 壓縮，並儲存至`Transmission`執行個體一次每隔 30 秒，或當已緩衝 500 個項目。 單一`Transmission`執行個體包含最多 500 個項目，而代表透過 Application Insights 服務的單一 HTTPS 呼叫已傳送的遙測批次。

根據預設，最多 10 個`Transmission`執行個體可以傳送以平行方式。 如果遙測抵達更快的速度，或如果網路或 Application Insights 將端是速度慢，`Transmission`執行個體儲存在記憶體中。 此記憶體中的預設容量`Transmission`緩衝區是 5 MB。 已超過記憶體容量，`Transmission`執行個體儲存在本機的磁碟，上限是 50 MB。 `Transmission` 執行個體時，儲存在本機磁碟上也有網路問題。 只有儲存在本機的磁碟的項目才會存留應用程式當機。 每次應用程式一次啟動時，他們正在傳送。

## <a name="configurable-settings-in-channels"></a>在頻道中可設定的設定

可設定的設定，每個通道的完整清單，請參閱：

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

以下是最常用的設定`ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`:以位元組為單位，到緩衝區在記憶體中的傳輸通道所使用的記憶體最大數量。 當到達此容量時，新的項目會儲存到本機的磁碟直接。 預設值是 5 MB。 設定較高的值會導致較少的磁碟使用量，但請記住，是否應用程式當機，在記憶體中的項目將會遺失。

1. `MaxTransmissionSenderCapacity`:最大數目`Transmission`會同時傳送至 Application Insights 的執行個體。 預設值為 10。 此設定較高的數目，就會產生大量遙測時，建議使用。 高容量通常會發生在負載測試，或取樣關閉時。

1. `StorageFolder`:由通道用來儲存至磁碟所需的項目資料夾。 在 Windows 中，%LOCALAPPDATA%或 %TEMP%如果會使用明確指定任何其他路徑。 在非 Windows 環境中，您必須指定有效的位置或遙測資料不會儲存到本機磁碟。

## <a name="which-channel-should-i-use"></a>我應該使用哪個通道？

`ServerTelemetryChannel` 建議針對大部分的實際執行案例牽涉到長時間執行的應用程式。 `Flush()`方法實作`ServerTelemetryChannel`不同步的而它也不保證傳送所有擱置中的項目從記憶體或磁碟。 如果您使用這個通道在案例中，應用程式即將關閉，我們建議您引入了一些延遲之後呼叫, `Flush()`。 延遲，您可能需要的確切量不是可預測的。 這取決於因素，例如多少項目或`Transmission`執行個體是在記憶體、 磁碟上有多少，多少會傳送到後端，以及通道是否中間指數退避法案例。

如果您需要執行同步排清，我們建議您改用`InMemoryChannel`。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 通道是否保證傳遞遙測？ 如果沒有，遙測可能會遺失的案例是什麼？

簡短的答案是沒有任何內建的通道提供的遙測傳送至後端的交易類型保證。 `ServerTelemetryChannel` 更進階的比較與`InMemoryChannel`，可靠的傳遞，但它也會只傳送遙測的最佳嘗試。 在許多情況下，包括這些常見的案例，可能仍會遺失遙測：

1. 應用程式損毀時，會遺失記憶體中的項目。

1. 在很長的網路問題，遙測會遺失。 在網路中斷，或當使用 Application Insights 的後端發生問題，遙測會儲存到本機磁碟。 不過，早於 24 小時內的項目都會被捨棄。

1. 將遙測資料儲存在 Windows 中的預設磁碟位置是 %LOCALAPPDATA%或 %TEMP%。 這些位置是通常是本機電腦。 如果應用程式移轉實體從一個位置之間，儲存在原始位置中的任何遙測將會遺失。

1. 在 Windows 上的 Web 應用程式的預設磁碟儲存體位置會是 D:\local\LocalAppData。 此位置不會持續保存。 它被抹除應用程式重新啟動、 小數位數挑出和其他這類作業，導致遺失儲存在其中任何遙測。 您可以覆寫預設值，並指定儲存體來保存的位置，例如 D:\home。 不過，這類保存的位置由遠端儲存體來提供，因此可能會很慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel 未在 Windows 以外的系統上運作？

雖然其套件和命名空間的名稱包含"WindowsServer"，此通道被支援不是 Windows，發生下列例外狀況的系統。 在 Windows 以外的系統，通道不會預設建立的本機儲存體資料夾。 您必須建立本機儲存體資料夾，並設定要使用的通道。 設定本機儲存體之後，通道會在所有系統運作方式相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否會建立暫存本機儲存體？ 在儲存體加密的資料嗎？

SDK 會將遙測項目儲存在本機儲存體中，期間節流或網路問題的期間。 這項資料不會在本機加密。

適用於 Windows 系統，SDK，自動建立暫時的本機資料夾中的 %TEMP%或 %LOCALAPPDATA%目錄，並限制系統管理員 」 和 「 目前使用者的存取進行相關的設定。

適用於 Windows 以外的系統，sdk，會自動建立任何本機存放裝置，並因此沒有資料儲存在本機的預設值。 您可以自行建立儲存體目錄，並設定要使用的通道。 在此情況下，您必須負責確保目錄受到保護。
深入了解[資料保護和隱私權](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)。

## <a name="open-source-sdk"></a>開放原始碼 SDK
每個適用於 Application Insights SDK，例如通道是開放原始碼。 讀取和貢獻程式碼或回報問題，在[官方 GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-dotnet)。

## <a name="next-steps"></a>後續步驟

* [取樣](../../azure-monitor/app/sampling.md)
* [SDK 進行疑難排解](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
