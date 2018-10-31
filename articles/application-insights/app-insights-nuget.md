---
title: Azure Application Insights - 相依性自動收集 | Microsoft Docs
description: Application Insights 會自動收集相依項目，並將其視覺化
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: b6188bbf89d9aee842d7f8e232a820b59c4d9e31
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023248"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet 套件

以下是 Application Insights 目前的穩定版 NuGet 套件清單。

## <a name="common-packages-for-aspnet"></a>常見的 ASP.NET 套件

| 套件名稱 | 穩定版 | 說明 | 下載 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | 提供所有「Application Insights 遙測類型」的傳輸核心功能，並且是所有其他 Application Insights 套件的相依套件 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | 可讓您攔截方法呼叫 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | 適用於 .NET 應用程式的「Application Insights 相依性收集器」。 這是 Application Insights 平台特定套件的相依套件，可自動收集相依性遙測資料。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | 「Application Insights 效能計數器收集器」可讓您將「效能計數器」所收集的資料傳送給 Application Insights。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | 適用於 .NET Web 應用程式的 Application Insights | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet 套件可為 .NET 應用程式自動收集 Application Insights 遙測資料。 此套件可用來作為 Application Insights 平台特定套件的相依套件，或作為平台特定套件未涵蓋之 .NET 應用程式的獨立套件 (例如針對 .NET 背景工作角色)。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | 提供遙測通道給將在離線情況下保留遙測資料的 Application Insights Windows Server SDK。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>常見的 ASP.NET Core 套件

| 套件名稱 | 穩定版 | 說明 | 下載 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | 適用於 ASP.NET Core Web 應用程式的 Application Insights。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | 此套件提供所有「Application Insights 遙測類型」的傳輸核心功能，並且是所有其他 Application Insights 套件的相依套件 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | 適用於 .NET 應用程式的「Application Insights 相依性收集器」。 這是 Application Insights 平台特定套件的相依套件，可自動收集相依性遙測資料。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | 「Application Insights 效能計數器收集器」可讓您將「效能計數器」所收集的資料傳送給 Application Insights。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet 套件可為 .NET 應用程式自動收集 Application Insights 遙測資料。 此套件可用來作為 Application Insights 平台特定套件的相依套件，或作為平台特定套件未涵蓋之 .NET 應用程式的獨立套件 (例如針對 .NET 背景工作角色)。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | 提供遙測通道給將在離線情況下保留遙測資料的 Application Insights Windows Server SDK。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>接聽程式/收集器/附加器

| 套件名稱 | 穩定版 | 說明 | 下載 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  可讓您將事件從 DiagnosticSource 轉送至 Application Insights. | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener 可讓您將資料從 EventSource 事件傳送給 Application Insights。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector 可讓您將資料從「Windows 事件追蹤」(ETW) 傳送給 Application Insights。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | 一個可讓您將追蹤記錄訊息傳送給 Application Insights 的自訂 TraceListener。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | 一個可讓您將 Log4Net 記錄訊息傳送給 Application Insights 的自訂附加器。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  一個可讓您將 NLog 記錄訊息傳送給 Application Insights 的自訂附加器。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | 監視您應用程式中的例外狀況，並自動收集快照集以供離線分析。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| 套件名稱 | 穩定版 | 說明 | 下載 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | 此套件會自動以應用程式執行所在的 Service Fabric 內容來裝飾遙測資料。 請勿將此 NuGet 用於原生 Service Fabric 應用程式。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | 適用於 Service Fabric 應用程式的 Application Insights。 請只將此 NuGet 用於原生 Service Fabric 應用程式。 針對在容器中執行的應用程式，請使用 Microsoft.ApplicationInsights.ServiceFabric 套件。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>狀態監視器

| 套件名稱 | 穩定版 | 說明 | 下載 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  可讓您收集 x64 應用程式的執行階段資料 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  可讓您收集 x86 應用程式的執行階段資料。 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

這些套件構成[狀態監視器](app-insights-monitor-performance-live-website-now.md)中執行階段監視核心功能的一部分。 您無須直接下載這些套件，只要使用「狀態監視器」安裝程式即可。 如果您想要深入了解這些套件的實際運作方式，參閱我們其中一位開發人員所撰寫的這篇[部落格文章](http://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)會是不錯的開始。

## <a name="additional-packages"></a>其他套件

| 套件名稱 | 穩定版 | 說明 | 下載 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | 此延伸模組可讓您在 Azure App Service 上啟用 Application Insights 監視功能。 SDK 2.6.1 版。 指示：新增含有您 ikey 的 'APPINSIGHTS_INSTRUMENTATIONKEY' 應用程式設定，然後重新啟動 WebApp 以讓它生效。| [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | 此套件包含無程式碼 Application Insights 插入所需的檔案 | [下載套件](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>後續步驟

- 監視 [ASP.NET Core](app-insights-asp-net-core.md)。
- 分析 ASP.NET Core [Azure Linux Web 應用程式](app-insights-profiler-aspnetcore-linux.md)。
- 對 ASP.NET [快照集](app-insights-snapshot-debugger.md)進行偵錯。