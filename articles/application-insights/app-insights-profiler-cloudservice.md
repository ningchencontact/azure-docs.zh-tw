---
title: 使用 Application Insights 來分析即時 Azure 雲端服務 | Microsoft Docs
description: 啟用雲端服務的 Application Insights Profiler。
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
ms.openlocfilehash: 28e21180fde50d19154830694cd4959795ae9d5c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789376"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>使用 Application Insights 來分析即時 Azure 雲端服務

您也可以在這些服務上部署 Application Insights Profiler：
* [Azure App Service](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 會與 Windows Azure 診斷 (WAD) 擴充功能一起安裝。 您只需要設定 WAD，以便安裝 Profiler 並將設定檔傳送至 Application Insights 資源。

## <a name="enable-profiler-for-your-azure-cloud-service"></a>針對 Azure 雲端服務啟用 Profiler
1. 確認您所使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本。  確認 ServiceConfiguration.\*.cscfg 檔案的 `osFamily` 值為 "5" 以上即可。
1. [將 Application Insights SDK 新增至雲端服務](../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)。
1. 使用 Application Insights 來追蹤要求：

    若為 ASP.Net Web 角色，Application Insights 可以自動追蹤要求。

    若為背景工作角色，請[新增程式碼來追蹤要求。](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. 設定 Windows Azure 診斷 (WAD) 擴充功能以啟用 Profiler。



    1. 針對您的應用程式角色找出 [Azure 診斷](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx 檔案，如下所示：  

       ![診斷組態檔的位置](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        如果找不到檔案以便了解如何在您的 Azure 雲端服務專案中啟用診斷擴充功能，請參閱[為 Azure 雲端服務和虛擬機器設定診斷](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)。

    1. 新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素：  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    >   **注意：** 如果 diagnostics.wadcfgx* 檔案也包含類型 `ApplicationInsights` 的另一個接收，則以下三個檢測金鑰全都必須符合：  
    >  * 應用程式使用的金鑰。  
    >  * `ApplicationInsights` 接收使用的金鑰。  
    >  * `ApplicationInsightsProfiler` 接收使用的金鑰。  
    >
    > 您可以在 ServiceConfiguration.\*.cscfg 檔案中找到 `ApplicationInsights` 接收所使用的實際檢測金鑰值。  
    > Visual Studio 15.5 Azure SDK 發行之後，只有應用程式和 `ApplicationInsightsProfiler` 接收所使用的檢測金鑰必須彼此相符。
1. 使用新的診斷設定部署服務，然後 Application Insights Profiler 就會設定為在服務上執行。
 
## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 入口網站中的 [Profiler 追蹤](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)。
- 在 [Profiler 疑難排解](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)中取得針對 Profiler 問題進行疑難排解的說明。