---
title: 監視裝載于 Azure VM 和 Azure 虛擬機器擴展集的應用程式效能 |Microsoft Docs
description: Azure VM 和 Azure 虛擬機器擴展集的應用程式效能監視。 圖表載入和回應時間、相依性資訊, 以及設定效能警示。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597445"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>監視裝載在 Azure VM 和 Azure 虛擬機器擴展集上的應用程式效能

在以 .NET 為基礎的 web 應用程式上啟用監視[虛擬機器](https://azure.microsoft.com/services/virtual-machines/)和[azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)現在比以往更容易。 取得使用 Application Insights 而不修改程式碼的所有優點。

本文將逐步引導您使用 ApplicationMonitoringWindows 擴充功能來啟用 Application Insights 監視功能, 並提供將大規模部署程式自動化的初步指導方針。

> [!IMPORTANT]
> Azure ApplicationMonitoringWindows 擴充功能目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供, 不建議用於生產工作負載。 某些功能可能不受支援, 有些可能會有限制的功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>啟用 Application Insights

有兩種方式可針對 Azure VM 和 Azure 虛擬機器擴展集裝載的應用程式啟用應用程式監視:

* 以**代理程式為基礎的應用程式監視**(ApplicationMonitoringWindows 擴充功能)。
    * 這個方法最容易啟用, 而且不需要任何 advanced 設定。 這通常稱為「執行時間」監視。 針對 Azure Vm 和 Azure 虛擬機器擴展集, 我們建議您至少啟用此層級的監視。 之後, 根據您的特定案例, 您可以評估是否需要手動檢測。
    * 目前僅支援 .Net IIS 裝載的應用程式。

* 藉由安裝 Application Insights SDK,**透過程式碼手動檢測應用程式**。

    * 這種方法更容易自訂, 但需要[在 APPLICATION INSIGHTS SDK NuGet 套件上新增](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)相依性。 這種方法也表示您必須自行管理最新版本套件的更新。

    * 如果您需要進行自訂 API 呼叫來追蹤預設不是以代理程式為基礎所捕捉的事件/相依性, 則必須使用此方法。 若要深入瞭解, 請參閱[自訂事件和計量的 API 一文](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)。

> [!NOTE]
> 如果偵測到以代理程式為基礎的監視和以手動 SDK 為基礎的檢測, 則只會接受手動檢測設定。 這是為了防止傳送重複的資料。 若要深入瞭解, 請參閱下面的[疑難排解一節](#troubleshooting)。

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>使用 PowerShell 在 VM 上管理 .NET 應用程式的代理程式型監視

安裝或更新 VM 的應用程式監視擴充功能
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

從 VM 卸載應用程式監視擴充功能
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

查詢應用程式監視 VM 的延伸模組狀態
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

取得 VM 已安裝的擴充功能清單
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>使用 powershell 在 Azure 虛擬機器擴展集上管理 .NET 應用程式的代理程式型監視

安裝或更新 Azure 虛擬機器擴展集的應用程式監視擴充功能
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

從 Azure 虛擬機器擴展集卸載應用程式監視擴充功能
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 虛擬機器擴展集的查詢應用程式監視延伸模組狀態
```powershell
# Not supported by extensions framework
```

取得 Azure 虛擬機器擴展集已安裝的擴充功能清單
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>疑難排解

以下是在 Azure VM 和 Azure 虛擬機器擴展集上執行之 .NET 應用程式的延伸模組式監視的逐步疑難排解指南。

> [!NOTE]
> 只有 Azure VM 和 Azure 虛擬機器擴展集上的 .NET Core、JAVA 和 node.js 應用程式可透過手動以 SDK 為基礎的檢測來支援, 因此下列步驟不適用於這些案例。

延伸模組執行輸出會記錄至下列目錄中的檔案：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>後續步驟
* 瞭解如何將[應用程式部署至 Azure 虛擬機器擴展集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* [設定可用性 web 測試](monitor-web-app-availability.md), 以在您的端點關閉時收到警示。
