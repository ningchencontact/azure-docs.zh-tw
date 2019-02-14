---
title: 使用 Application Insights Profiler 來分析 Azure VM 上所執行的 Web 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure VM 上的 Web 應用程式。
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
ms.openlocfilehash: 01d57a10189f9281736e628a83465c96d282af70
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860138"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>使用 Application Insights Profiler 來分析 Azure 虛擬機器或虛擬機器擴展集上所執行的 Web 應用程式

您也可以在這些服務上部署 Azure Application Insights Profiler：
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>在虛擬機器或虛擬機器擴展集上部署 Profiler
本文示範如何在 Azure 虛擬機器 (VM) 或 Azure 虛擬機器擴展集上執行 Application Insights Profiler。 Profiler 會與 VM 的 Azure 診斷擴充功能一起安裝。 設定擴充功能以執行 Profiler，並將 Application Insights SDK 建置到您的應用程式中。

1. 將 Application Insights SDK 新增至 [ASP.NET 應用程式](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)或一般的 [.NET 應用程式。](windows-services.md?toc=/azure/azure-monitor/toc.json)  
  若要檢視您要求的設定檔，您必須將要求遙測傳送至 Application Insights。

1. 在 VM 上安裝 Azure 診斷擴充功能。 如需完整的 Resource Manager 範本範例，請參閱：  
    * [虛擬機器](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [虛擬機器擴展集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    關鍵部分在於 WadCfg 中的 ApplicationInsightsProfilerSink。 若要讓 Azure 診斷啟用 Profiler 將資料傳送至 iKey，請在此區段新增另一個接收端。
    
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. 部署修改過的環境部署定義。  

   套用修改通常會牽涉到完整範本部署或透過 PowerShell Cmdlet 或 Visual Studio 的雲端服務型發佈。  

   下列 PowerShell 命令是只會觸及 Azure 診斷擴充功能之現有虛擬機器的替代方法。 將先前所述的 ProfilerSink 新增到 Get-AzureRmVMDiagnosticsExtension 命令傳回的設定中，然後將更新的設定傳遞給 Set-AzureRmVMDiagnosticsExtension 命令。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果預期的應用程式是透過 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 執行，則啟用 `IIS Http Tracing` Windows 功能。

   a. 建立環境的遠端存取，然後使用 [新增 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 視窗。 或在 PowerShell 中執行下列命令 (以系統管理員身分)：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果建立遠端存取發生問題，您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 來執行下列命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署應用程式。

## <a name="can-profiler-run-on-on-premises-servers"></a>是否可在內部部署伺服器上執行 Profiler？
我們沒有計畫針對內部部署伺服器支援 Applciation Insights Profiler。

## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](monitor-web-app-availability.md))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 入口網站中的 [Profiler 追蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
- 如需 Profiler 問題的疑難排解說明，請參閱 [Profiler 疑難排解](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
