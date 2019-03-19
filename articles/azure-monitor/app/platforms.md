---
title: Application Insights：程式語言、平台和整合 | Microsoft Docs
description: 適用於 Application Insights 的程式語言、平台和整合
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 5a7fbeafe74926c7d89ac76dc2964756b3582d61
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671143"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>開發人員分析：程式語言、平台和整合
這些項目是我們曾聽過的 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 實作，包括一些協力廠商提供的實作。

## <a name="languages---officially-supported-by-application-insights-team"></a>程式語言 - Application Insights 團隊正式支援
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript web pages](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>程式語言 - 社群支援
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [任何其他項目](#projects)

## <a name="platforms-and-frameworks"></a>平台和架構
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET - 適用於即時應用程式](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [Azure 雲端服務](../../azure-monitor/app/cloudservices.md)&#151;同時包含 Web 與背景工作角色
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE 堆疊](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [通用 Windows app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows 桌面應用程式、服務和背景工作角色](../../azure-monitor/app/windows-desktop.md)
* [任何其他項目](#projects)

## <a name="logging-frameworks"></a>記錄架構
* [Log4Net、NLog 或 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](../../azure-monitor/app/java-trace-logs.md)
* [語意記錄 (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - 與[語意記錄應用程式區塊](https://msdn.microsoft.com/library/dn440729.aspx)整合
* [雲端式負載測試](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash 外掛程式](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Azure 監視器](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>內容管理系統
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>匯出和資料分析
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [串流分析](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> 建置您自己的 SDK
如果還沒有適合您程式語言或平台使用的 SDK，您會想要建置一個嗎？ 查看 [GitHub 上之 Application Insights SDK 專案](https://github.com/Microsoft/AppInsights-Home)中列出的現有 SDK 程式碼。
