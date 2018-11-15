---
title: 針對 Azure 自動化共用資源的錯誤進行疑難排解
description: 了解如何針對 Azure 自動化共用資源的錯誤進行疑難排解
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263413"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>針對共用資源的錯誤進行疑難排解

本文討論在 Azure 自動化中使用共用資源時，您可以跨資源解決問題的解決方案。

## <a name="modules"></a>模組

### <a name="module-stuck-importing"></a>案例：模組在匯入時卡住

#### <a name="issue"></a>問題

當您匯入或更新您在 Azure 自動化中的模組時，您會發現模組卡在**匯入**狀態。

#### <a name="error"></a>Error

匯入 PowerShell 模組是一種複雜的多步驟程序。 此程序可能會發生模組未正確匯入的問題。 如果發生這種情況，您所匯入的模組可能會卡在暫時性的狀態。 若要深入了解此程序，請參閱[匯入 PowerShell 模組]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解決方案

若要解決此問題，您必須使用 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) Cmdlet 移除卡在**匯入**狀態的模組。 您可以稍後再重試匯入模組。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。