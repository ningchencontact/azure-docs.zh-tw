---
title: 在 Application Insights 中探索 .NET 追蹤記錄
description: 搜尋由追蹤、 NLog 或 Log4Net 產生的記錄檔。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 74cb1b3ec4e0570aa4316e6f45e99719f36815d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150706"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>在 Application Insights 中探索 .NET/.NET Core 追蹤記錄

將 ILogger、 NLog、 log4Net、 或 System.Diagnostics.Trace 來 ASP.NET/ASP.NET 核心應用程式的診斷追蹤記錄檔傳送[Azure Application Insights][start]。 然後，您可以探索，並搜尋它們。 這些記錄檔會合併與從您的應用程式的其他記錄檔，因此您可以識別每個使用者要求相關聯，並將它們與其他事件和例外狀況報告相互關聯的追蹤。

> [!NOTE]
> 您需要記錄擷取模組嗎？ 它是有用的配接器的第三方記錄器。 如果您還沒使用 Nlog、log4net、 log4Net、 或 System.Diagnostics.Trace，請考慮直接呼叫，但是[ **Application Insights tracktrace （)** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)直接。
>
>
## <a name="install-logging-on-your-app"></a>在您的 app 上安裝記錄
在您的專案，在 app.config 或 web.config 中的項目應導致安裝您所選擇的記錄架構。

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>設定 Application Insights 收集記錄
[將 Application Insights 新增至您的專案](../../azure-monitor/app/asp-net.md)如果您尚未完成的動作。 您將會看見包含記錄收集器的選項。

或以滑鼠右鍵按一下方案總管 中的專案**設定 Application Insights**。 選取 **設定追蹤集合**選項。

> [!NOTE]
> 沒有 Application Insights 功能表或記錄檔收集器選項嗎？ 請嘗試進行[疑難排解](#troubleshooting)。

## <a name="manual-installation"></a>手動安裝
如果 Application Insights 安裝程式不支援您的專案類型 (例如 Windows 傳統型專案)，請使用這個方法。

1. 如果您打算使用 log4Net 或 NLog，請將它安裝在您的專案。
2. 在 [方案總管] 中，以滑鼠右鍵按一下您的專案，然後選取**管理 NuGet 套件**。
3. 搜尋"Application Insights"。
4. 選取下列其中一個套件：

   - 針對 ILogger：[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - 針對 NLog：[Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - 針對 Log4Net：[Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 針對 System.Diagnostics：[Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 封裝會安裝必要的組件，並會修改 web.config 或 app.config，是否適用。

## <a name="ilogger"></a>ILogger

如需使用主控台應用程式和 ASP.NET Core 的 Application Insights ILogger 實作的範例，請參閱[.NET Core ilogger ApplicationInsightsLoggerProvider 記錄](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入診斷記錄呼叫
如果您使用 System.Diagnostics.Trace，典型的呼叫如下：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果您偏好 log4net 或 NLog，請使用：

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>使用 EventSource 事件
您可以將 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，安裝 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 套件。 然後編輯 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 檔案的 `TelemetryModules` 區段。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **名稱**指定要收集之 EventSource 的名稱。
 * **層級**指定要收集的記錄等級：*關鍵*，*錯誤*，*參考*，*永遠記錄*， *Verbose*，或*警告*.
 * **關鍵字**（選擇性） 指定要使用的關鍵字組合的整數值。

## <a name="use-diagnosticsource-events"></a>使用 DiagnosticSource 事件
您可以將 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，安裝 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 套件。 然後編輯 "TelemetryModules 」 一節[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)檔案。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

您想要追蹤的每個 DiagnosticSource，新增具有的項目**名稱**屬性設定為 DiagnosticSource 的名稱。

## <a name="use-etw-events"></a>使用 ETW 事件
您可以設定要傳送至 Application Insights 作為追蹤的事件追蹤的 Windows (ETW) 事件。 首先，安裝 `Microsoft.ApplicationInsights.EtwCollector` NuGet 套件。 然後編輯 "TelemetryModules 」 一節[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)檔案。

> [!NOTE] 
> 如果裝載 SDK 的處理序執行身分識別是 Performance Log Users 或 Administrators 的成員，才可收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **ProviderName**是收集的 ETW 提供者的名稱。
 * **ProviderGuid**指定要收集的 ETW 提供者的 GUID。 它可以用來取代`ProviderName`。
 * **層級**設定要收集的記錄等級。 它可以是*重大*，*錯誤*， *Informational*，*永遠記錄*， *Verbose*，或  *警告*。
 * **關鍵字**（選擇性） 設定要使用的關鍵字組合的整數值。

## <a name="use-the-trace-api-directly"></a>直接使用追蹤 API
您可以直接呼叫 Application Insights 追蹤 API。 記錄配接器會使用此 API。

例如︰

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的優點在於您可以將較長的資料放在訊息中。 例如，您可以在該處編碼 POST 資料。

您也可以新增至您的訊息的嚴重性層級。 就像其他遙測，您可以新增屬性值，以協助篩選或搜尋不同的追蹤集。 例如︰

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

這可讓您輕鬆地在篩選出[搜尋][ diagnostic]某特定嚴重性層級相關的所有訊息與特定資料庫。

## <a name="explore-your-logs"></a>探索記錄
在 偵錯模式中執行您的應用程式，或即時部署它。

在您的應用程式 [概觀] 窗格中[Application Insights 入口網站][portal]，選取[搜尋][diagnostic]。

例如，您可以：

* 篩選記錄追蹤或具有特定屬性的項目。
* 詳細檢查特定項目。
* 尋找與相同的使用者要求其他系統記錄檔資料 （具有相同的 OperationId）。
* 將頁面的組態儲存為我的最愛。

> [!NOTE]
>如果您的應用程式傳送大量資料，且您使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本中，*調適型取樣*功能可能會運作，並傳送遙測資料的一小部分。 [深入了解取樣。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>疑難排解
### <a name="how-do-i-do-this-for-java"></a>如果是 Java，我要怎麼做？
使用 [Java 記錄配接器](../../azure-monitor/app/java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>專案內容功能表上沒有 Application Insights 選項
* 請確定已在開發電腦上安裝 Application Insights Tools。 在 Visual Studio**工具** > **擴充功能和更新**，尋找**Application Insights Tools**。 如果不是在**已安裝**索引標籤上，開啟**線上**索引標籤，然後安裝它。
* 這可能是 Application Insights 工具不支援的專案類型。 請使用 [手動安裝](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>在 組態工具中沒有記錄配接器選項
* 第一次安裝記錄架構。
* 如果您使用 System.Diagnostics.Trace，請確定您有[中設定*web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)。
* 請確定您有最新版的 Application Insights。 在 Visual Studio 中，移至**工具** > **擴充功能和更新**，然後開啟**更新** 索引標籤。如果**Developer Analytics Tools**是，選取它來更新它。

### <a name="emptykey"></a>我收到 「 檢測金鑰不能是空白 」 錯誤訊息
您可能會安裝而不需要安裝 Application Insights 的記錄配接器 Nuget 套件。 在 [方案總管] 中，以滑鼠右鍵按一下*ApplicationInsights.config*，然後選取**更新 Application Insights**。 您將會提示您登入 Azure 並建立 Application Insights 資源或重複使用現有的帳戶。 這樣應該可以解決此問題。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我可以看到追蹤，但未在診斷搜尋中的其他事件
可能需要一段時間的所有事件和要求才會通過管線。

### <a name="limits"></a>保留多少資料？
數個因素會影響保留的資料量。 如需詳細資訊，請參閱 <<c0> [ 限制](../../azure-monitor/app/api-custom-events-metrics.md#limits)客戶事件計量頁面的區段。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>看不到某些我預期的記錄項目
如果您的應用程式傳送毫不遲疑的巨量資料與您使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，調適性取樣功能可能會運作，並傳送只包含您的遙測資料的一部分。 [深入了解取樣。](../../azure-monitor/app/sampling.md)

## <a name="add"></a>接續步驟

* [在 ASP.NET 中診斷失敗和例外狀況][exceptions]
* [深入了解搜尋][diagnostic]
* [設定可用性和回應性測試][availability]
* [疑難排解][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md