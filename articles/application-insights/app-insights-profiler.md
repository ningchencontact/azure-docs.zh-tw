---
title: 使用 Application Insights 來分析即時 Azure Web 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure 上的即時 Web 應用程式。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 4d957c26bd4e4ae278c0909c9df1476b02954b86
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138002"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Web 應用程式

Profiler 目前適用於在 Web Apps 上執行的 ASP.NET 和 ASP.NET Core Web 應用程式。 需要基本或更高的服務層，才可使用 Profiler。 目前僅可透過[這個方法](app-insights-profiler-aspnetcore-linux.md)，在 Linux 上啟用 Profiler。

## <a id="installation"></a> 為您的 Web Apps 啟用 Profiler
若要為 Web 應用程式啟用 Profiler，請遵循下列指示。 如果您正在執行不同類型的 Azure 服務，以下是在其他支援的平台上啟用 Profiler 的指示：
* [雲端服務](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 會在 App Service 執行階段中預先安裝，但是您需要開啟它以取得 Azure Web Apps 的設定檔。 部署 Web 應用程式後，即使您已在原始程式碼中包含 App Insights SDK，仍請執行下列步驟來啟用 Profiler。

1. 移至 Azure 入口網站中的 [App Service] 窗格。
1. 瀏覽至 [設定] > [Application Insights] 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/app-insights-profiler/AppInsights-AppServices.png)

1. 遵循窗格上的指示以建立新資源，或選取現有 App Insights 資源以監視您的 Web 應用程式。 也請確定 Profiler 為 [開啟]。

   ![新增 App Insights 網站延伸模組][Enablement UI]

1. 現在已使用 App Service 應用程式設定來啟用 Profiler。

    ![Profiler 的應用程式設定][profiler-app-setting]

## <a name="disable-profiler"></a>停用 Profiler

若要停止或重新啟動個別 Web 應用程式執行個體的 Profiler，請在 [Web 作業] 底下，移至該 Web Apps 資源。 若要刪除 Profiler，請移至 [擴充功能]。

![停用 Web 作業的 Profiler][disable-profiler-webjob]

建議您在所有 Web 應用程式上都啟用 Profiler，以儘早找出任何效能問題。

如果您使用 WebDeploy 來部署對 Web 應用程式所做的變更，請確定您已排除 App_Data 資料夾，以免系統在部署期間刪除它。 否則，下次您將 Web 應用程式部署到 Azure 時，系統就會刪除 Profiler 擴充功能的檔案。



## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

