---
title: 將成品新增至在 Azure DevTest Labs 中的 VM |Microsoft Docs
description: 了解如何將成品新增至在 Azure DevTest Labs 中對實驗室中的虛擬機器
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 19a7d6052091f8889a88c61793186b7bf7d9d869
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60304172"
---
# <a name="add-an-artifact-to-a-vm"></a>將成品新增至 VM
在建立 VM 時，您可以新增現有的成品。 這些成品可以是從其中[公用 DevTest Labs Git 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)或從您自己的 Git 存放庫。 這篇文章會示範如何在 Azure 入口網站中，並使用 Azure PowerShell 新增的成品。 

Azure DevTest Labs「構件」  可讓您指定會在 VM 佈建時執行的「動作」  ，例如執行 Windows PowerShell 指令碼、執行 Bash 命令，以及安裝軟體。 構件「參數」  可讓您自訂適用於特定案例的構件。

若要深入了解如何建立自訂構件，請參閱文章：[建立自訂構件](devtest-lab-artifact-author.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>使用 Azure 入口網站 
1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [所有服務]  ，然後從清單中選取 [DevTest Labs]  。
1. 從實驗室清單中，選取您想要使用之 VM 所在的實驗室。  
1. 選取 [我的虛擬機器]  。
1. 選取所需的 VM。
1. 選取 [管理構件]  。 
1. 選取 [套用構件]  。
1. 在 [套用構件]  窗格中，選取您要新增到 VM 的構件。
1. 在 [新增構件]  窗格中，輸入必要的參數值以及任何您所需的選用參數。  
1. 選取 [新增]  以新增構件，然後返回 [套用構件]  窗格。
1. 視需要繼續為您的 VM 加入構件。
1. 加入構件之後，您可以 [變更構件的執行順序](#change-the-order-in-which-artifacts-are-run)。 您也可以返回來 [檢視或修改構件](#view-or-modify-an-artifact)。
1. 當您新增好構件時，選取 [套用] 

### <a name="change-the-order-in-which-artifacts-are-run"></a>變更構件的執行順序
根據預設，構件的動作是依照它們加入 VM 的順序來執行。 下列步驟說明如何變更構件的執行順序。

1. 在 [套用構件]  窗格頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件]  窗格中，將構件拖放到所需的順序。 如果您在拖曳成品時發生問題，請確定您是從成品左側進行拖曳。 
1. 完成時選取 [確定]  。  

### <a name="view-or-modify-an-artifact"></a>檢視或修改構件
下列步驟說明如何檢視或修改構件的參數︰

1. 在 [套用構件]  窗格頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件]  窗格中，選取您想要檢視或編輯的構件。  
1. 在 [新增構件]  窗格中，進行任何所需的變更，然後選取 [確定]  以關閉 [新增構件]  窗格。
1. 選取 [確定]  以關閉 [選取的構件]  窗格。

## <a name="use-powershell"></a>使用 PowerShell
下列指令碼會套用指定的成品至指定的 VM。 [Invoke AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)命令就是執行作業。  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>後續步驟
在成品，請參閱下列文章：

- [指定必要的成品，您的實驗室](devtest-lab-mandatory-artifacts.md)
- [建立自訂構件](devtest-lab-artifact-author.md)
- [將構件儲存機制加入實驗室](devtest-lab-artifact-author.md)
- [診斷構件失敗](devtest-lab-troubleshoot-artifact-failure.md)
