---
title: 使用 Application Insights Profiler 來分析 Azure VM 上所執行的 Web 應用程式 | Microsoft Docs
description: 使用 Application Insights Profiler 來分析 Azure VM 上的 Web 應用程式。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 10cd05bd40262815e3b27c861982debc18e5b4f3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142710"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>使用 Application Insights Profiler 來分析 Azure 虛擬機器或虛擬機器擴展集上所執行的 Web 應用程式
您也可以在這些服務上部署 Application Insights Profiler：
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [雲端服務](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>在虛擬機器或擴展集上部署 Profiler
此頁面會引導您完成所需步驟，以便讓 Application Insights Profiler 在 Azure VM 或 Azure 虛擬機器擴展集上執行。 Application Insights Profiler 會與 VM 的 Windows Azure 診斷擴充功能一起安裝。 您必須設定此擴充功能以便執行 Profiler，並將 App Insights SDK 建置到應用程式，以取得 VM 上所執行 Web 應用程式的設定檔。

1. 將 Application Insights SDK 新增至 [ASP.Net 應用程式](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)或一般的 [.NET 應用程式。](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) 您必須將要求遙測傳送至 Application Insights，才能看到要求的設定檔。
1. 在 VM 上安裝 Windows Azure 診斷擴充功能。 如需完整的 Resource Manager 範本範例，請參閱：  
    * [虛擬機器](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [虛擬機器擴展集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. 部署修改過的環境部署定義。  

   若要套用修改，通常會牽涉到完整範本部署或透過 PowerShell Cmdlet 或 Visual Studio 的雲端服務型發佈。  

   下列 PowerShell 命令是只會觸及 Azure 診斷擴充功能之現有虛擬機器的替代方法：  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果預期的應用程式是透過 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 執行，則藉由執行下列作業來啟用 `IIS Http Tracing` Windows 功能：  

   a. 建立環境的遠端存取，然後使用 [新增 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 視窗或在 PowerShell 中執行下列命令 (以系統管理員身分)：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果建立遠端存取發生問題，您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 來執行下列命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署應用程式。

## <a name="enable-profiler-on-on-premises-servers"></a>在內部部署伺服器上啟用 Profiler

在內部部署伺服器上啟用 Profiler 也稱為在獨立模式中執行 Application Insights Profiler。 這不會涉及 Azure 診斷擴充程式修改。

我們尚未規劃正式針對內部部署伺服器支援 Profiler。 如果您對於此案例中的測試有興趣，您可以[下載支援程式碼](https://github.com/ramach-msft/AIProfiler-Standalone)。 我們「不」負責維護該程式碼，或回應與該程式碼相關的問題和功能要求。

## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 入口網站中的 [Profiler 追蹤](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)。
- 在 [Profiler 疑難排解](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)中取得針對 Profiler 問題進行疑難排解的說明。
