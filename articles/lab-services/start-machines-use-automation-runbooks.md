---
title: 啟動機器在 Azure DevTest Labs 中使用自動化 runbook |Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中實驗室中啟動虛擬機器使用 Azure 自動化 runbook。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996657"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>啟動虛擬機器的實驗室中使用 Azure 自動化 runbook 的順序
[Autostart](devtest-lab-set-lab-policy.md#set-autostart) DevTest Labs 功能可讓您設定為在指定時間自動啟動的 Vm。 不過，這項功能不支援以特定順序啟動機器。 有數個這類的自動化會很有用的案例。  其中一個案例是 Jumpbox VM 實驗室內需要的位置第一次，啟動之前其他 Vm，那麼 Jumpbox 作為其他 Vm 的存取點。  這篇文章會示範如何設定 Azure 自動化帳戶與執行指令碼的 PowerShell runbook。 指令碼在 Vm 上，在實驗室中，讓您控制的啟動順序，而不需要變更指令碼，使用標記。

## <a name="setup"></a>設定
在此範例中，在實驗室中的 Vm 需要有標記**StartupOrder**加上適當的值 (0,1,2，等等。)。 將指定的任何電腦，不需要啟動為-1。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶
建立 Azure 自動化帳戶中的下列指示[這篇文章](../automation/automation-create-standalone-account.md)。 選擇**執行身分帳戶**選項建立帳戶時。 自動化帳戶建立之後，開啟**模組**頁面，然後選取**更新 Azure 模組**功能表列上。 預設的模組是舊的和沒有更新指令碼的數個版本可能無法運作。

## <a name="add-a-runbook"></a>新增 runbook
現在，若要將 runbook 新增至自動化帳戶中，選取**Runbook**左側功能表上。 選取 [**新增 runbook** ] 功能表上，遵循指示[建立 PowerShell runbook](../automation/automation-first-runbook-textual-powershell.md)。

## <a name="powershell-script"></a>PowerShell 指令碼
下列指令碼會採用訂用帳戶名稱，做為參數的實驗室名稱。 指令碼的流程是在實驗室中，取得所有 Vm，並接著剖析標記資訊來建立的 VM 名稱和其啟動順序的清單。 指令碼將逐步引導完成順序中的 Vm，並啟動 Vm。 如果在幾個特定的順序有多個 Vm，他們會開始以非同步方式使用 PowerShell 作業。 沒有標記時，設定啟動值的最後一個 (10)，這些 vm 會啟動這類最後，根據預設。  如果實驗室不想要某項自動啟動 VM，設定標記值為 11，將會忽略它。

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>建立排程
若要讓每日、 執行此指令碼[建立的排程](../automation/shared-resources/schedules.md#creating-a-schedule)的自動化帳戶中。 建立排程時，一旦[將它連結至 runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)。 

在大規模的情況下有多個實驗室的多個訂用帳戶，儲存參數資訊的檔案不同的實驗室中，並將檔案傳遞至指令碼，而不是個別的參數。 指令碼會需要修改，但核心執行會相同。 雖然此範例會使用 Azure 自動化來執行 PowerShell 指令碼，但也有其他選項，例如在組建/發行管線中使用的工作。

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以深入了解 Azure 自動化：[Azure 自動化簡介](../automation/automation-intro.md)。
