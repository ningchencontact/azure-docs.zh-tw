---
title: 使用 Application Insights 來分析即時 Azure Service Fabric 應用程式 | Microsoft Docs
description: 針對 Service Fabric 應用程式啟用 Profiler
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
ms.openlocfilehash: 73e453385a6916b6999691afbacd1f102ce7270d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865730"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Service Fabric 應用程式

您也可以在這些服務上部署 Application Insights Profiler：
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>設定環境部署定義

Application Insights Profiler 會隨附於 Azure 診斷。 您可以使用 Azure Resource Manager 範本為 Service Fabric 叢集安裝 Azure 診斷擴充功能。 取得[將 Azure 診斷安裝在 Service Fabric 叢集上的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。

若要設定環境，請採取下列動作：

1. 若要確保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本，確認部署的作業系統是 `Windows Server 2012 R2` 或更新版本就足夠。

1. 在部署範本檔案中搜尋 [Azure 診斷](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)擴充功能。

1. 新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素。 將 `ApplicationInsightsProfiler` 屬性值取代為您自己的 Application Insights 檢測金鑰：  

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

1. 使用 Azure Resource Manager 範本部署 Service Fabric 叢集。  
  如果設定全都正確，就會在安裝 Azure 診斷擴充功能時，安裝並啟用 Application Insights Profiler。 

1. 在 Service Fabric 應用程式中新增 Application Insights。  
  若要讓 Profiler 收集要求的設定檔，您的應用程式必須將要求資料傳送至 Application Insights。 如需詳細資訊，請移至[適用於 Service Fabric 專案的 Application Insights SDK](https://github.com/Microsoft/ApplicationInsights-ServiceFabric) 頁面。

1. 重新部署應用程式。

> [提示] 針對虛擬機器，上述 JSON 型步驟的替代方式是，在 Azure 入口網站中瀏覽至 [虛擬機器] > [診斷設定] > [接收]  > **將診斷資料傳送到 Application Insights 設為 [啟用]**，然後選取 Application Insights 帳戶或特定 ikey。

## <a name="next-steps"></a>後續步驟

* 產生應用程式的流量 (例如，啟動[可用性測試](monitor-web-app-availability.md))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
* 請參閱 Azure 入口網站中的 [Profiler 追蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
* 如需 Profiler 問題的疑難排解說明，請參閱 [Profiler 疑難排解](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
