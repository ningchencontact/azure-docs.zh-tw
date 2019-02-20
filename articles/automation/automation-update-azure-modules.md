---
title: 更新 Azure 自動化中的 Azure 模組
description: 本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d8f57310cf4dbc2a27761fc44cfde6c8fd2791a2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005534"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何更新 Azure 自動化中的 Azure PowerShell 模組

若要更新自動化帳戶中的 Azure 模組，現在建議您使用[更新 Azure 模組 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\)，它現在已經是開放原始碼資源。 此外，您仍然可以使用入口網站中的 [更新 Azure 模組] 按鈕來更新 Azure 模組。 若要深入了解使用開放原始碼 Runbook，請參閱[搭配開放原始碼 Runbook 更新 Azure 模組](#open-source)。

每個自動化帳戶依預設都會提供最通用的 Azure PowerShell 模組。 Azure 小組會定期更新 Azure 模組。 在您的自動化帳戶中，我們會提供方式讓您在入口網站有新的版本可供使用時，更新帳戶中的模組。

由於產品小組會定期更新模組，因此，內含的 Cmdlet 可能會發生變更。 此動作可能會根據變更的類型 (例如，重新命名參數或完全淘汰 Cmdlet) 對您的 Runbook 產生負面影響。

為了避免影響 Runbook 以及它們所自動化的流程，請先測試和驗證，然後再繼續。 如果您沒有適用於此用途的專用自動化帳戶，請考慮建立一個，讓您可以在開發 Runbook 期間測試許多不同的案例。 此測試應該包含更新 PowerShell 模組之類的反覆變更。 

如果您在本機開發您的指令碼，建議您在測試時於本機使用您自動化帳戶中的相同模組版本，以確保您會收到相同的結果。 結果已經過驗證且您已套用所需的任何變更之後，您就能將變更移到生產環境。

> [!NOTE]
> 新的自動化帳戶可能不會包含最新的模組。

## <a name="open-source"></a>搭配開放原始碼 Runbook 更新 Azure 模組

若要開始使用 **Update-AutomationAzureModulesForAccount** Runbook 來更新 Azure 模組，請從 GitHub 上的[更新 Azure 模組 Runbook 存放庫](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 下載它。 您接著便可以將它匯入到自動化帳戶，或是以指令碼的形式執行它。 執行此操作的指示可在[更新 Azure 模組 Runbook 存放庫](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)中找到。

### <a name="considerations"></a>考量

下列為在使用此程序來更新 Azure 模組時應納入考量的項目：

* 如果您以原始名稱 `Update-AutomationAzureModulesForAccount` 匯入此 Runbook，它將會覆寫具有此名稱的內部 Runbook。 因此，在按下 [更新 Azure 模組] 按鈕，或是直接透過此自動化帳戶的 Azure Resource Manager API 叫用此 Runbook 時，系統將會執行匯入的 Runbook。

* 目前只支援 `Azure` 和 `AzureRM.*` 模組。 尚未支援新的 [Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

* 請避免在包含 Az 模組的自動化帳戶上啟動此 Runbook。

* 在啟動此 Runbook 之前，請確定您的自動化帳戶已建立 [Azure 執行身分帳戶認證](manage-runas-account.md)。

* 您可以使用此程式碼作為一般 PowerShell 指令碼，而非 Runbook，作法是先使用 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 命令登入 Azure，然後將 `-Login $false` 傳遞給指令碼。

* 若要在主權雲端上使用此 Runbook，請使用 `AzureRmEnvironment` 參數來將正確的環境傳遞給 Runbook。  允許的值為 **AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud** 和 **AzureUSGovernment**。 這些值可以使用 `Get-AzureRmEnvironment | select Name` 來擷取。 如果您未傳遞值給這個參數，Runbook 將會預設為 Azure 公用雲端 **AzureCloud**。

* 如果您想要使用特定的 Azure PowerShell 模組版本，而不是 PowerShell 資源庫上提供的最新版本，可將這些版本傳遞給 **Update-AutomationAzureModulesForAccount** Runbook 的選擇性 `ModuleVersionOverrides` 參數。 如需範例，請參閱 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) \(英文\) Runbook。 `ModuleVersionOverrides` 參數中未提及的 Azure PowerShell 模組會以 PowerShell 資源庫上的最新模組版本來更新。 如果您未傳遞任何項目給 `ModuleVersionOverrides` 參數，則所有模組都會以 PowerShell 資源庫上的最新模組版本來更新。 此行為與 [更新 Azure 模組] 按鈕相同。

## <a name="update-azure-modules-in-the-azure-portal"></a>在 Azure 入口網站中更新 Azure 模組

1. 在自動化帳戶的 [模組] 頁面中，有一個稱為**更新 Azure 模組**的選項。 它一律為啟用狀態。<br><br> ![[模組] 頁面中的 [更新 Azure 模組] 選項](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > 更新 Azure 模組之前，建議您先在測試用的自動化帳戶中進行更新，確保現有指令碼會如預期般運作後，再更新 Azure 模組。
  >
  > [更新 Azure 模組] 按鈕功能僅適用於公用雲端。 不適用於[主權區域](https://azure.microsoft.com/global-infrastructure/)。 請使用 **Update-AutomationAzureModulesForAccount** Runbook 來更新 Azure 模組。 您可以從[更新 Azure 模組 Runbook 存放庫](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 下載它。 若要深入了解使用開放原始碼 Runbook，請參閱[搭配開放原始碼 Runbook 更新 Azure 模組](#open-source)。

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

## <a name="next-steps"></a>後續步驟

造訪開放原始碼的[更新 Azure 模組 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 來深入了解它。
