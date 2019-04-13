---
title: 從 Azure DevTest Labs 中的另一個實驗匯入虛擬機器 |Microsoft Docs
description: 了解如何從另一個實驗室的虛擬機器匯入到目前的實驗室。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 9cd2e5e211fcda7c59469d3b09e9c9e5bdefdbd6
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546578"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>從 Azure DevTest Labs 中的另一個實驗匯入虛擬機器
本文章提供有關如何從另一個實驗室的虛擬機器匯入到您的實驗室資訊。 

## <a name="scenarios"></a>案例
以下是一些情況下，您需要從一個實驗室的 Vm 匯入到另一個實驗室： 

- 個別小組會移至企業內的另一個群組，並想要對新的小組 DevTest Labs 中採取開發人員的桌面。
- 群組達到[訂用帳戶層級配額](../azure-subscription-service-limits.md)並想要分割成幾個訂用帳戶小組
- 該公司會移至 Express Route （或一些其他新的網路拓撲），並將小組想要移動虛擬機器，以使用此新基礎結構

## <a name="solution-and-constraints"></a>解決方案和條件約束
這項功能可讓您匯入一個實驗室 （來源） 的 Vm，到另一個實驗室 （目的地）。 您可以選擇性地提供程序中的目的地 VM 的新名稱。 匯入程序包含所有相依性，例如磁碟、 排程、 網路設定，依此類推。

此程序會需要一些時間，並會受到下列因素：

- （因為它是複製作業，而不是移動作業），會附加到來源機器的磁碟數目/大小 
- 目的地 （例如，美國東部區域東南亞） 的距離。  

此程序完成之後，來源虛擬機器會維持關機及一個目的地實驗室中用來執行新。

有兩個要注意的規劃從一個實驗室中的 Vm 匯入至另一個實驗室時的索引鍵條件約束：

- 支援跨訂用帳戶和跨區域的虛擬機器匯入，但訂用帳戶必須是相同的 Azure Active Directory 租用戶產生關聯。
- 虛擬機器不能在來源實驗室可宣告的狀態。
- 您已在目的地實驗室中實驗室的擁有者與來源實驗室 VM 的擁有者。
- 目前，只能透過 Powershell 和 REST API 支援此功能。

## <a name="use-powershell"></a>使用 PowerShell
下載檔案 ImportVirtualMachines.ps1 [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)。 您可以使用指令碼至單一 VM 或所有來源實驗室 Vm 匯入目的地實驗室。 

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 匯入單一 VM
執行這個 powershell 指令碼需要識別 VM 的來源和目的地實驗室中，並選擇性地提供要用於目的地電腦的新名稱：

```powershell 
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 匯入所有來源實驗室 Vm
如果未指定來源虛擬機器，則指令碼會自動匯入在 DevTest Labs 中的所有 Vm。  例如︰
 
```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 匯入 VM
REST 呼叫很簡單。 您提供足夠的資訊來識別來源和目的地的資源。 請記住，作業會在目的地實驗室資源。

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
