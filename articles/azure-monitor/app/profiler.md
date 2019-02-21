---
title: 使用 Application Insights 來分析即時 Azure App Service 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure App Service 上的即時應用程式。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: f3ec10a970406cbb1bb6a1a52ffa8508e37fc516
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414162"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure App Service 應用程式

Profiler 目前適用於在 Azure App Service 上執行的 ASP.NET 和 ASP.NET Core 應用程式。 需要基本或更高的服務層，才可使用 Profiler。 目前僅可透過[這個方法](profiler-aspnetcore-linux.md)，在 Linux 上啟用 Profiler。

## <a id="installation"></a> 為應用程式啟用 Profiler
若要為應用程式啟用 Profiler，請依照下列指示進行操作。 如果您正在執行不同類型的 Azure 服務，以下是在其他支援的平台上啟用 Profiler 的指示：
* [雲端服務](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 會隨著 App Service 執行階段一起預先安裝，但您必須開啟它，才能取得 App Service 應用程式的設定檔。 部署應用程式之後，即使您已在原始程式碼中包含 App Insights SDK，仍請依照下列步驟來啟用 Profiler。

1. 移至 Azure 入口網站中的 [App Service] 窗格。
2. 瀏覽至 [設定] > [Application Insights] 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/profiler/AppInsights-AppServices.png)

3. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定 Profiler 為 [開啟]。

   ![新增 App Insights 網站延伸模組][Enablement UI]

4. 現在已使用 App Service 應用程式設定來啟用 Profiler。

    ![Profiler 的應用程式設定][profiler-app-setting]

## <a name="disable-profiler"></a>停用 Profiler

若要為個別應用程式的執行個體停止或重新啟動 Profiler，請在 [Web 工作] 底下，移至該應用程式資源。 若要刪除 Profiler，請移至 [擴充功能]。

![停用 Web 作業的 Profiler][disable-profiler-webjob]

建議您在所有應用程式上都啟用 Profiler，以儘早找出任何效能問題。

如果您使用 WebDeploy 來部署對 Web 應用程式所做的變更，請確定您已排除 App_Data 資料夾，以免系統在部署期間刪除它。 否則，下次您將 Web 應用程式部署到 Azure 時，系統就會刪除 Profiler 擴充功能的檔案。



## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
