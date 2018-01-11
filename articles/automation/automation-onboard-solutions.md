---
title: "將更新與變更追蹤解決方案上線至 Azure 自動化 | Microsoft Docs"
description: "了解如何將更新與變更追蹤解決方案上線至 Azure 自動化。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: eamono
ms.openlocfilehash: 25ad5e04c96aab9dddd39cdb4d33bd8411817e8e
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>將更新與變更追蹤解決方案上線至 Azure 自動化

在本教學課程中，您會了解如何將適用於 VM 的更新、變更追蹤與清查解決方案上線至 Azure 自動化：

> [!div class="checklist"]
> * 上架 Azure VM
> * 啟用方案
> * 安裝和更新模組
> * 匯入登入 runbook
> * 啟動 Runbook

## <a name="prerequisites"></a>必要條件

若要完成本教學課程中，需要下列條件：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)，以管理電腦。
* A[虛擬機器](../virtual-machines/windows/quick-create-portal.md)上架。

## <a name="onboard-an-azure-vm"></a>上架 Azure VM

若要建立 Azure 虛擬機器自動現有的 VM 必須 onboarded 與變更追蹤或更新的管理解決方案。 在此步驟中，您將產品上架的虛擬機器更新管理與變更追蹤。

### <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

變更追蹤和清查方案可讓您[追蹤變更](automation-vm-change-tracking.md)和[清查](automation-vm-inventory.md)虛擬機器上。 在此步驟中，您可以啟用虛擬機器上的方案。

1. 從左窗格中，選取**自動化帳戶**，然後在清單中選取您的自動化帳戶。
1. 選取**清查**下**CONFIGURATION MANAGEMENT**。
1. 選取現有的 Log Analytics 工作區，或建立新的工作區。 按一下**啟用** 按鈕。

![上架的更新方案](media/automation-onboard-solutions/inventory-onboard.png)

變更追蹤和清查方案上架通知完成時，按一下**更新管理**下**CONFIGURATION MANAGEMENT**。

### <a name="enable-update-management"></a>啟用更新管理

更新管理解決方案可讓您管理您的 Azure Windows Vm 的最新更新和修補程式。 您可以評估可用的更新，安裝必要的更新排程的狀態，並檢閱部署結果，以確認更新已成功套用至 VM。 在此步驟中，您可以啟用方案 vm。

1. 從您的自動化帳戶選取**更新管理**下**更新管理**。
1. 選取記錄分析工作區是在上一個步驟中使用相同的工作區。 按一下**啟用**上架的更新管理解決方案。

![上架的更新方案](media/automation-onboard-solutions/update-onboard.png)

正在安裝的更新管理解決方案，而會顯示藍色橫幅。 啟用方案時選取**變更追蹤**下**CONFIGURATION MANAGEMENT**移至下一個步驟。

### <a name="select-azure-vm-to-be-managed"></a>選取要管理的 Azure VM

現在已啟用的解決方案，您可以將 Azure VM 加入的即上架到這些方案。

1. 從您的自動化帳戶上**變更追蹤**頁面上，選取**+ 加入 Azure VM**加入您的虛擬機器。

1. 選取您的 VM 清單並選取從**啟用**。 這個動作會啟用變更追蹤和庫存方案的虛擬機器。

   ![啟用 vm 的更新方案](media/automation-onboard-solutions/enable-change-tracking.png)

1. 當 VM 上架通知完成時，從您的自動化帳戶選取**更新管理**下**更新管理**。

1. 選取**+ 加入 Azure VM**加入您的虛擬機器。

1. 選取您的 VM 清單並選取從**啟用**。 這個動作可讓虛擬機器的更新管理解決方案。

   ![啟用 vm 的更新方案](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> 如果您不需要等候其他方案，才能完成，請啟用 下一步的方案時您收到訊息，指出：*安裝另一個解決方案是這或不同的虛擬機器上進行中。該安裝完成時已啟用 啟用 按鈕，以及您可以要求這部虛擬機器上之方案的安裝。*

## <a name="install-and-update-modules"></a>安裝和更新模組

必須更新為最新的 Azure 模組，並匯入`AzureRM.OperationalInsights`成功自動化解決方案上架。

## <a name="update-azure-modules"></a>更新 Azure 模組

從您的自動化帳戶選取**模組**下**共用資源**。 選取**更新 Azure 模組**Azure 模組更新為最新版本。 選取**是**上的最新版本更新所有現有的 Azure 模組的提示。

![更新模組](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>安裝 AzureRM.OperationalInsights 模組

從**模組**頁面上，選取**瀏覽圖庫**開啟模組資源庫。 搜尋 AzureRM.OperationalInsights，並將此模組匯入至自動化帳戶。

![匯入 OperationalInsights 模組](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>匯入登入 runbook

1. 從您的自動化帳戶中，選取上**Runbook**下**程序自動化**。
1. 選取**瀏覽圖庫**。
1. 搜尋**更新與變更追蹤**，按一下 runbook 並選取**匯入**上**檢視原始檔**頁面。 選取**確定**將 runbook 匯入的自動化帳戶。

  ![匯入登入 runbook](media/automation-onboard-solutions/import-from-gallery.png)

1. 在**Runbook**頁面上，選取**編輯**，然後選取**發行**。 在**發行 Runbook**對話方塊中，選取**是**發佈的 runbook。

## <a name="start-the-runbook"></a>啟動 Runbook

您必須已將變更追蹤或更新解決方案上線至 Azure VM，才能啟動這個 Runbook。 它需要目前具有已針對參數上線之解決方案的虛擬機器和資源群組。

1. 開啟 Enable-MultipleSolution Runbook。

   ![多個方案 runbook](media/automation-onboard-solutions/runbook-overview.png)

1. 按一下 [開始] 按鈕，並針對參數輸入下列值。

   * **VMNAME** -保留空白。 要上線至更新或變更追蹤解決方案的現有 VM 名稱。 根據將此值保留空白，在資源群組中的所有 Vm 都都就緒。
   * **VMRESOURCEGROUP** -要 onboarded Vm 的資源群組的名稱。
   * **SUBSCRIPTIONID** -保留空白。 新的 VM 是 onboarded 訂用帳戶 ID。 如果保留空白，則會使用工作區的訂用帳戶。 指定不同的訂用帳戶識別碼時，也應該將此自動化帳戶的 RunAs 帳戶新增為此訂用帳戶的參與者。
   * **ALREADYONBOARDEDVM** -已手動更新或變更追蹤 onboarded VM 名稱的方案。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** -VM 所屬的資源群組名稱。
   * **SOLUTIONTYPE** -輸入**更新**或**變更追蹤**

   ![啟用 MultipleSolution runbook 參數](media/automation-onboard-solutions/runbook-parameters.png)

1. 選取**確定**啟動 runbook 工作。
1. 在 Runbook 作業頁面上監視進度和任何錯誤。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 手動將 Azure 虛擬機器上線。
> * 安裝及更新所需的 Azure 模組。
> * 匯入能將 Azure VM 上線的 Runbook。
> * 啟動會自動將 Azure VM 上線的 Runbook。

跟隨此連結以深入了解排程的 runbook。

> [!div class="nextstepaction"]
> [排程 runbook](automation-schedules.md)。
