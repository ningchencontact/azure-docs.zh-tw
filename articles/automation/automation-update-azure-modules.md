---
title: 更新 Azure 自動化中的 Azure 模組
description: 本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f461bc1fd17ee957be5f223de731608011d021f5
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959354"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何更新 Azure 自動化中的 Azure PowerShell 模組

每個自動化帳戶依預設都會提供最通用的 Azure PowerShell 模組。 Azure 小組會定期更新 Azure 模組。 在您的自動化帳戶中，我們會提供方式讓您在入口網站有新的版本可供使用時，更新帳戶中的模組。

由於產品小組會定期更新模組，因此，內含的 Cmdlet 可能會發生變更。 此動作可能會根據變更的類型 (例如，重新命名參數或完全淘汰 Cmdlet) 對您的 Runbook 產生負面影響。 為了避免影響 Runbook 以及它們所自動化的流程，請先測試和驗證，然後再繼續。 如果您沒有適用於此用途的專用自動化帳戶，請考慮建立一個，讓您可以在開發 Runbook 期間測試許多不同的案例。 此測試應該包含更新 PowerShell 模組之類的反覆變更。 如果您在本機開發您的指令碼，建議您在測試時於本機使用您自動化帳戶中的相同模組版本，以確保您會收到相同的結果。 結果已經過驗證且您已套用所需的任何變更之後，您就能將變更移到生產環境。

> [!NOTE]
> 新的自動化帳戶可能不會包含最新的模組。

## <a name="updating-azure-modules"></a>更新 Azure 模組

1. 在自動化帳戶的 [模組] 頁面中，有一個稱為**更新 Azure 模組**的選項。 它一律為啟用狀態。<br><br> ![[模組] 頁面中的 [更新 Azure 模組] 選項](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > 更新 Azure 模組之前，建議您先在測試用的自動化帳戶中進行更新，確保現有指令碼會如預期般運作後，再更新 Azure 模組。
  >
  > [更新 Azure 模組] 按鈕功能僅適用於公用雲端。 不適用於[主權區域](https://azure.microsoft.com/global-infrastructure/)。 請參閱[更新模組的替代方式](#alternative-ways-to-update-your-modules)一節，以深入了解。

2. 按一下 [更新 Azure 模組]，則會顯示確認通知，詢問您是否要繼續。<br><br> ![更新 Azure 模組通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. 按一下 [是] 就會開始模組更新程序。 更新程序大約需要 15-20 分鐘來更新下列模組︰

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    如果模組已是最新狀態，此程序在幾秒鐘內便會完成。 當更新流程完成之後，您就會收到通知。<br><br> ![更新 Azure 模組更新狀態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    Azure 自動化中不支援 .NET Core 的 AzureRm 模組 (AzureRm.*.Core)，因此無法匯入。

> [!NOTE]
> 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。  

如果您在 Runbook 中使用來自這些 Azure PowerShell 模組的 Cmdlet，則您會想要每隔大約一個月執行一次此更新程序，以確保您擁有最新模組。 Azure 自動化會在更新模組時，使用 `AzureRunAsConnection` 連線來驗證。 如果服務主體已過期或不再存在於訂用帳戶層級，模組更新將會失敗。

## <a name="alternative-ways-to-update-your-modules"></a>更新模組的替代方式

如上所述，[更新 Azure 模組] 按鈕無法在主權雲端中使用，該功能僅適用於全域 Azure 雲端。 這是因為 PowerShell 資源庫中最新版的 Azure PowerShell 模組可能不適用於目前部署在這些雲端中的 Resource Manager 資源。

您可以匯入並執行 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook，以嘗試更新自動化帳戶中的 Azure 模組。 這通常是同時更新所有 Azure 模組的好方法。 但是，如果您嘗試從資源庫匯入的版本與目前部署至目標 Azure 環境的 Azure 服務不相容，則此流程可能會失敗。 這可能需要您確認 Runbook 參數中指定了模組的相容版本。

使用 `AzureRmEnvironment` 參數可將正確的環境傳遞至 Runbook。  允許的值為 **AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud** 和 **AzureUSGovernment**。 這些值可以使用 `Get-AzureRmEnvironment | select Name` 來擷取。 如果您未傳遞值給這個參數，Runbook 將會預設為 Azure 公用雲端 **AzureCloud**。

如果您想要使用特定的 Azure PowerShell 模組版本，而不是 PowerShell 資源庫上提供的最新版本，可將這些版本傳遞給 **Update-AzureModule** Runbook 的選擇性 `ModuleVersionOverrides` 參數。 如需範例，請參閱 [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook。 `ModuleVersionOverrides` 參數中未提及的 Azure PowerShell 模組會以 PowerShell 資源庫上的最新模組版本來更新。 如果您未傳遞任何項目給 `ModuleVersionOverrides` 參數，則所有模組都會以 PowerShell 資源庫上的最新模組版本來更新。 此行為與 [更新 Azure 模組] 按鈕相同。

## <a name="next-steps"></a>後續步驟

* 若要深入了解整合模組，以及如何建立自訂模組以進一步整合自動化與其他系統、服務或解決方案，請參閱[整合模組](automation-integration-modules.md)。

