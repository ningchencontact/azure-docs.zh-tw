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
ms.openlocfilehash: dd28bc3925b0f07a441c46a26498ef1a14c3e650
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510318"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>適用於 Azure Functions 的 Application Insights 支援的功能

Azure Functions 提供與 Application Insights 的[內建整合](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)，可透過 ILogger 介面來使用。 以下列出目前支援的功能。 請檢閱 Azure Functions 的[開始使用](https://github.com/Azure/Azure-Functions/wiki/App-Insights)指南。

## <a name="supported-features"></a>支援的功能

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **自動收集**        |                 |                   |               
| &bull; 要求                     | yes             | yes               | 
| &bull; 例外狀況                   | yes             | yes               | 
| &bull; 效能計數器         | yes             |                   |
| &bull; 相依性                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | yes               | 
| | | | 
| **支援的功能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | yes             | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; 保護控制通道|                 | yes               | 
| &bull; 取樣                     | yes             | yes               | 
| &bull; 活動訊號                   |                 | yes               | 
| | | | 
| **相互關聯**                       |                   |                   |               
| &bull; ServiceBus                     |                   | yes               | 
| &bull; EventHub                       |                   | yes               | 
| | | | 
| **可設定**                      |                   |                   |           
| &bull;可完整設定。<br/>如需相關指示，請參閱 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)。<br/>請參閱 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) 以了解所有選項。               |                   | yes                   | 


## <a name="performance-counters"></a>效能計數器

只有 Windows 機器才能進行效能計數器的自動收集。


## <a name="live-metrics--secure-control-channel"></a>即時計量和保護控制通道

您指定的自訂篩選條件準則會傳回給 Application Insights SDK 中的即時計量元件。 篩選條件可能會包含機密資訊，例如 customerIDs。 您可以利用祕密 API 金鑰來保護通道安全。 如需指示，請參閱[保護控制通道](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)。

## <a name="sampling"></a>取樣

Azure Functions 依預設會在其設定中啟用取樣。 如需詳細資訊，請參閱[設定取樣](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)。
