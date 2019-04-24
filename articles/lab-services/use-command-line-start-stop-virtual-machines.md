---
title: 使用命令列工具來啟動和停止 Vm 的 Azure DevTest Labs |Microsoft Docs
description: 了解如何使用命令列工具來啟動和停止 Azure DevTest Labs 中的虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: a8132735d1af08055e9341608dcac0564ed4b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236687"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令列工具來啟動和停止 Azure DevTest Labs 虛擬機器
本文說明如何使用 Azure PowerShell 或 Azure CLI 來啟動或停止在 Azure DevTest Labs 中對實驗室中的虛擬機器。 您可以建立 PowerShell/CLI 指令碼來自動化這些作業。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀
Azure DevTest Labs 是建立快速、 簡易且高效的開發/測試環境的方法。 它可讓您管理成本、 快速佈建 Vm，並減少浪費。  有內建在 Azure 入口網站中的功能，讓您在自動啟動，並在特定時間停止實驗室中設定 Vm。 

不過，在某些情況下，您可能想要自動化啟動和停止的 Vm，從 PowerShell/CLI 指令碼。 它可讓您的開始和停止個別的機器，在任何時間，而不是在特定時間的一些彈性。 以下是一些在哪一個執行這些工作，藉由使用指令碼會有幫助的情況。

- 使用時的 3 層式架構應用程式測試環境的一部分，各層必須啟動序列中。 
- 自訂準則符合以節省成本時，請關閉 VM。 
- 使用它作為 CI/CD 工作流程內的工作流程的開頭開始，使用 Vm 當做組建電腦，測試機器或基礎結構，然後完成程序時停止的 Vm。 這個範例會使用 Azure DevTest Labs 自訂映像處理站。  

## <a name="azure-powershell"></a>Azure PowerShell
下列 PowerShell 指令碼會在實驗室中啟動 VM。 [叫用 AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)是此指令碼的主要焦點。 **ResourceId**參數是在實驗室中 VM 的完整的資源識別碼。 **動作**參數是要在哪裡**開始**或是**停止**選項都設為視需要的項目。

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)自動化啟動和停止的 DevTest Labs Vm 的另一種方法。 可以是 azure CLI[安裝](/cli/azure/install-azure-cli?view=azure-cli-latest)不同作業系統上。 下列指令碼可讓您啟動及停止 VM，以在實驗室中的命令。 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>後續步驟
請參閱下列文章中的有使用 Azure 入口網站來執行這些作業：[重新啟動 VM](devtest-lab-restart-vm.md)。
