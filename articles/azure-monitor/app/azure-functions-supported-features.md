---
title: Azure Application Insights - 支援 Azure Functions 的功能 | Microsoft Docs
description: 支援 Azure Functions 的 Application Insights 功能
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496056"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>適用於 Azure Functions 的 Application Insights 支援的功能

Azure Functions 提供與 Application Insights 的[內建整合](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)，可透過 ILogger 介面來使用。 以下列出目前支援的功能。 請檢閱 Azure Functions 的[開始使用](https://github.com/Azure/Azure-Functions/wiki/App-Insights)指南。

## <a name="supported-features"></a>支援的功能

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights.NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **自動收集**        |                 |                   |               
| &bull; 要求                     | 是             | 是               | 
| &bull; 例外狀況                   | 是             | 是               | 
| &bull; 效能計數器         | 是             | 是               |
| &bull; 相依性                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | 是               | 
| | | | 
| **支援的功能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | 是             | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; 保護控制通道|                 | 是               | 
| &bull; 取樣                     | 是             | 是               | 
| &bull; 活動訊號                   |                 | 是               | 
| | | | 
| **相互關聯**                       |                   |                   |               
| &bull; ServiceBus                     |                   | 是               | 
| &bull; EventHub                       |                   | 是               | 
| | | | 
| **可設定**                      |                   |                   |           
| &bull;可完整設定。<br/>如需相關指示，請參閱 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)。<br/>請參閱 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) 以了解所有選項。               |                   | 是                   | 


## <a name="performance-counters"></a>效能計數器

只有 Windows 機器才能進行效能計數器的自動收集。


## <a name="live-metrics--secure-control-channel"></a>即時計量和保護控制通道

您指定的自訂篩選條件準則會傳回給 Application Insights SDK 中的即時計量元件。 篩選條件可能會包含機密資訊，例如 customerIDs。 您可以利用祕密 API 金鑰來保護通道安全。 如需指示，請參閱[保護控制通道](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)。

## <a name="sampling"></a>取樣

Azure Functions 依預設會在其設定中啟用取樣。 如需詳細資訊，請參閱[設定取樣](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)。

如果您的專案上執行手動追蹤的遙測的 Application Insights SDK 的相依性，可能會遇到奇怪的行為，如果您的取樣組態不同於函式的取樣組態中。 

我們建議使用相同的設定，做為函式。 具有**Functions v2**，您可以取得相同的組態，您的建構函式中使用相依性插入：

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
