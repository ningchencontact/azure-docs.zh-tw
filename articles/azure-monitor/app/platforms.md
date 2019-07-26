---
title: 'Application Insights: 語言、平臺和整合 |Microsoft Docs'
description: 適用于 Application Insights 的語言、平臺和整合
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: dd2fb0c9ef157e3706ca3d902f7feebaf4c2f279
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478916"
---
# <a name="supported-languages"></a>支援的語言

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>支援的平台和架構

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署之應用程式的檢測 (無程式碼, 以代理程式為基礎)
* [Azure VM 和 Azure 虛擬機器擴展集](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 適用於即時應用程式](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 雲端服務](../../azure-monitor/app/cloudservices.md), 包括 web 和背景工作角色
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>透過程式碼進行檢測 (Sdk)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [通用 Windows app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows 桌面應用程式、服務和背景工作角色](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>記錄架構
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net、NLog 或 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash 外掛程式](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure 監視器](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>匯出和資料分析
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [串流分析](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-community-sdks"></a>不支援的社區 Sdk

有許多社區 Application Insights Sdk。 Microsoft 不會正式支援及維護社區 Sdk。 我們無法針對不在正式支援清單上的任何 SDK 提供支援。 