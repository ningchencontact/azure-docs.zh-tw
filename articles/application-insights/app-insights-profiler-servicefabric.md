---
title: 使用 Application Insights 來分析即時 Azure Service Fabric 應用程式 | Microsoft Docs
description: 針對 Service Fabric 應用程式啟用 Profiler
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
ms.openlocfilehash: 3eb5b2300ea2af7bc778e0831d105f286eab247c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721403"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Service Fabric 應用程式

您也可以在這些服務上部署 Application Insights Profiler：
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [雲端服務](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [虛擬機器](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>設定環境部署定義

Application Insights Profiler 會隨附於 Windows Azure 診斷 (WAD)。 使用 Service Fabric 叢集的 Azure RM 範本即可安裝 WAD 擴充功能。 這裡有範例範本：[**會將 WAD 安裝在 Service Fabric 叢集上的範本。**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

若要設定環境，請採取下列動作：
1. 若要確保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本，確認部署的作業系統是 `Windows Server 2012 R2` 或更新版本就足夠。

1. 在部署範本檔案中搜尋 [Azure 診斷](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)擴充功能，並新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素。 將 `ApplicationInsightsProfiler` 屬性值取代為您自己的 Application Insights 檢測金鑰：  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      如需將診斷擴充功能新增至部署範本的詳細資訊，請參閱[使用 Windows VM 和 Azure Resource Manager 範本的監視和診斷](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
1. 使用 Azure Resource Manager 範本部署 Service Fabric 叢集。 如果設定全都正確，就會在安裝 WAD 擴充功能時，安裝並啟用 Application Insights Profiler。 
1. 在 Service Fabric 應用程式中新增 Application Insights。 應用程式必須將要求資料傳送至 Application Insights，Profiler 才會收集要求的設定檔。 您可以在[這裡](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)找到指示。
1. 重新部署應用程式。

> [提示] 針對虛擬機器，上述 json 型步驟的替代方式是，在 Azure 入口網站中瀏覽至 [虛擬機器] > [診斷設定] > [接收] > 將診斷資料傳送到 Application Insights 設為 [啟用]，然後選取 Application Insights 帳戶或特定 ikey。

## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 入口網站中的 [Profiler 追蹤](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)。
- 在 [Profiler 疑難排解](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)中取得針對 Profiler 問題進行疑難排解的說明。