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
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469976"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure App Service 應用程式

您可以執行 Profiler 在 ASP.NET 和 ASP.NET Core 應用程式正在 Azure App Service 上使用基本服務層或更高版本。 目前僅可透過[這個方法](profiler-aspnetcore-linux.md)，在 Linux 上啟用 Profiler。

## <a id="installation"></a> 為應用程式啟用 Profiler
若要為應用程式啟用 Profiler，請依照下列指示進行操作。 如果您執行不同類型的 Azure 服務，以下是其他支援的平台上啟用 Profiler 的指示：
* [雲端服務](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 會預先安裝的應用程式服務執行階段的一部分。 下列步驟將示範如何啟用您的 App Service。 即使您已經包含應用程式的 application Insights SDK 的應用程式在建置階段中，請遵循下列步驟。

1. 啟用 「 永遠開啟 」 設定您的 app service。 您可以更新您的 App Service，[一般設定] 下的 [組態] 頁面中的設定。
1. 移至 Azure 入口網站中的 [App Service] 窗格。
1. 瀏覽至 [設定] > [Application Insights] 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/profiler/AppInsights-AppServices.png)

1. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定 Profiler 為 [開啟]。 如果 Application Insights 資源位於不同的訂用帳戶，從您的 App Service，您無法使用此頁面來設定 Application Insights。 您仍然可以執行它以手動方式雖然藉由建立必要的應用程式設定以手動方式。 [下一節包含手動啟用 Profiler 的指示。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![新增 App Insights 網站延伸模組][Enablement UI]

1. 現在已使用 App Service 應用程式設定來啟用 Profiler。

    ![Profiler 的應用程式設定][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>啟用 Profiler，以手動方式或使用 Azure Resource Manager
建立您的 Azure App Service 應用程式設定，可以啟用 Application Insights Profiler。 如上所示的選項頁面建立這些為您的應用程式設定。 但是，您可以自動建立的範本或其他方式使用這些設定。 如果您的 Application Insights 資源位於不同的訂用帳戶，從您的 Azure App Service，這些設定也會運作。
以下是啟用分析工具所需的設定：

|應用程式設定    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 資源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


您可以設定使用這些值[Azure Resource Manager 範本](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)， [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)， [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)。


## <a name="disable-profiler"></a>停用 Profiler

若要為個別應用程式的執行個體停止或重新啟動 Profiler，請在 [Web 工作] 底下，移至該應用程式資源。 若要刪除 Profiler，請移至 [擴充功能]。

![停用 Web 作業的 Profiler][disable-profiler-webjob]

建議您在所有應用程式上都啟用 Profiler，以儘早找出任何效能問題。

使用 WebDeploy 將變更部署到您的 web 應用程式時，就可以刪除 Profiler 的檔案。 您可以在部署期間被刪除時，排除 [App_Data] 資料夾，以避免刪除。 


## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
