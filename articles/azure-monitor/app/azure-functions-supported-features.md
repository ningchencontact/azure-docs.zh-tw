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
ms.openlocfilehash: 9ad0579ff9c25753b1e4816b80948b4d8d1232f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081876"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>適用於 Azure Functions 的 Application Insights 支援的功能

Azure Functions 提供與 Application Insights 的[內建整合](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)，可透過 ILogger 介面來使用。 以下列出目前支援的功能。 請檢閱 Azure Functions 的[開始使用](https://github.com/Azure/Azure-Functions/wiki/App-Insights)指南。

## <a name="supported-features"></a>支援的功能

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **自動收集**        |                 |                   |               
| &bull; 要求                     | 是             | 是               | 
| &bull; 例外狀況                   | 是             | 是               | 
| &bull; 相依性                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | 是               | 
| | | | 
| **支援的功能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | 是             | 是               | 
| &bull; 取樣                     | 是             | 是               | 
| &bull; 活動訊號                   |                 | 是               | 
| | | | 
| **相互關聯**                       |                   |                   |               
| &bull; ServiceBus                     |                   | 是               | 
| &bull; EventHub                       |                   | 是               | 
| | | | 
| **可設定**                      |                   |                   |           
| &bull;可完整設定。<br/>如需相關指示，請參閱 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)。<br/>請參閱 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) 以了解所有選項。               |                   | 是                   | 


## <a name="sampling"></a>取樣

Azure Functions 依預設會在其設定中啟用取樣。 如需詳細資訊，請參閱[設定取樣](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)。
