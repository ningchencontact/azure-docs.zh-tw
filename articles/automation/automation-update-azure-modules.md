---
title: "更新 Azure 自動化中的 Azure 模組 | Microsoft Docs"
description: "本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.openlocfilehash: f5e7c66cfd26bd6927d48ffd8bc0f82e9a3e2d13
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何更新 Azure 自動化中的 Azure PowerShell 模組

每個自動化帳戶依預設都會提供最通用的 Azure PowerShell 模組。  Azure 小組會定期更新 Azure 模組，因此在自動化帳戶中，我們會提供方法供您在入口網站有新的版本可供使用時，更新帳戶中的模組。  

因為會根據產品群組定期更新模組，所以內含 Cmdlet 可能會變更，這樣可能會根據變更類型 (例如重新命名參數，或完全淘汰 Cmdlet) 對 Runbook 造成負面影響。 若要避免影響 Runbook 以及它們所自動化的處理序，強烈建議您先進行測試和驗證，再繼續進行。  如果您沒有適用於此用途的專用自動化帳戶，請考慮建立自動化帳戶，讓您除了更新 PowerShell 模組這類反覆變更之外，還可以在開發 Runbook 期間測試許多不同的案例和排列。  驗證結果且套用任何所需變更之後，請繼續協調任何需要修改之 Runbook 的移轉，並如下所述在生產環境中執行更新。     

## <a name="updating-azure-modules"></a>更新 Azure 模組

1. 在您的自動化帳戶的 [模組] 頁面上，沒有選項**更新 Azure 模組**。 它一律為啟用狀態。<br><br> ![更新模組 頁面中的 Azure 模組選項](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. 按一下**更新 Azure 模組**，您會看到確認通知，詢問您是否要繼續。<br><br> ![更新 Azure 模組通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. 按一下**是**模組更新程序開始。 更新程序大約需要 15-20 分鐘來更新下列模組︰

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    如果模組已有最新狀態，然後在程序完成在幾秒鐘的時間。 在更新程序完成時，系統會通知您。<br><br> ![更新 Azure 模組更新狀態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。    

如果您使用 runbook 中的下列 Azure PowerShell 模組 cmdlet 來管理 Azure 資源，然後您想要執行這個更新程序的每個月或因此若要確保您可以將最新的模組。

## <a name="next-steps"></a>後續步驟

* 若要深入了解整合模組，以及如何建立自訂模組以進一步整合自動化與其他系統、服務或解決方案，請參閱[整合模組](automation-integration-modules.md)。

* 請考慮使用 [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) 或 [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) 的原始檔控制整合，以集中管理和控制自動化 Runbook 和設定組合的發行。  