---
title: 管理 Azure Windows VM 的更新和修補程式
description: 本文章提供概觀，說明如何使用 Azure 自動化 - 更新管理來管理您 Azure Windows VM 的更新和修補程式。
services: automation
author: zjalexander
ms.service: automation
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: bded1621dc56a6e621408e567ce39a3107bec7c9
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>使用 Azure 自動化來管理 Windows 更新

更新管理可讓您管理虛擬機器的更新和修補程式。
在本教學課程中，您會了解如何快速評估可用更新的狀態、排程何時安裝必要的更新，並檢閱部署結果來確認更新已成功套用。

如需價格資訊，請參閱[更新管理的自動化價格](https://azure.microsoft.com/pricing/details/automation/)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 將 VM 上架以進行更新管理
> * 檢視更新評估
> * 排定更新部署
> * 檢視部署的結果

## <a name="prerequisites"></a>先決條件

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)可保存監看員和動作 Runbook，以及監看員工作。
* 要上架的[虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登入 Azure

在 http://portal.azure.com 上登入 Azure 入口網站。

## <a name="enable-update-management"></a>啟用更新管理

您必須先啟用本教學課程中的 VM 更新管理。 如果您先前已啟用 VM 的另一個自動化解決方案，就不需要此步驟。

1. 在左側功能表上，選取 [虛擬機器]，然後從清單中選取 VM
2. 在左側功能表的 [作業] 區段下，按一下 [更新管理]。 [啟用更新管理] 頁面隨即開啟。

將會執行驗證來判斷此 VM 是否已啟用更新管理。
驗證包括檢查 Log Analytics 工作區及連結的自動化帳戶，以及解決方法是否在工作區中。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作區可用來收集功能和服務 (例如更新管理) 所產生的資料。
工作區提供單一位置來檢閱和分析來自多個來源的資料。
若要在需要更新的 VM 上執行其他動作，Azure 自動化可讓您對 VM 執行 Runbook，例如下載和套用更新。

驗證程序也會檢查 VM 是否以 Microsoft Monitoring Agent (MMA) 和自動化混合式 Runbook 背景工作角色佈建。
此代理程式用來與 VM 通訊，並取得更新狀態的相關資訊。

選擇 Log Analytics 工作區和自動化帳戶，然後按一下 [啟用] 以啟用解決方案。 啟用解決方案最多需要 15 分鐘。

如果在上線期間遺漏下列任何必要條件，就會自動新增：

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作區
* [自動化](./automation-offering-get-started.md)
* VM 上已啟用 [Hybrid Runbook 背景工作角色](./automation-hybrid-runbook-worker.md)

[更新管理] 畫面隨即開啟。 設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後按一下 [啟用]。 如果欄位呈現灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用相同的工作區和自動化帳戶。

![[啟用更新] 管理解決方案視窗](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

啟用解決方案可能需要 15 分鐘。 在此期間，請勿關閉瀏覽器視窗。
啟用解決方案之後，有關在 VM 上遺漏更新的相關資訊會流向 Log Analytics。
可能需要 30 分鐘到 6 小時，資料才可供分析。

## <a name="view-update-assessment"></a>檢視更新評量

啟用 [更新管理] 之後，隨即會顯示 [更新管理] 畫面。
如果遺失任何更新，您在 [遺失更新] 索引標籤上就會看到遺失的更新清單。

選取更新上的 [資訊連結]，即可在新視窗開啟更新的支援文章。 您可以在這裡了解關於更新的重要資訊。

![檢視更新狀態](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

按一下更新的任何其他地方，即可開啟所選更新的 [記錄搜尋] 視窗。 記錄搜尋的查詢是針對該特定更新所預先定義的。 您可以修改此查詢或建立您自己的查詢，來檢視部署的更新或您環境中遺失項目的詳細資訊。

![檢視更新狀態](./media/automation-tutorial-update-management/logsearch.png)

## <a name="schedule-an-update-deployment"></a>排定更新部署

您現在知道您的 VM 遺失更新。 若要安裝更新，請將部署排定在發行排程和服務時段之後。
您可以選擇要在部署中包含的更新類型。
例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

> [!WARNING]
> 當更新需要重新開機時，VM 就會自動重新啟動。

排程 VM 的新更新部署，方法是瀏覽回到 [更新管理]，並選取畫面頂端的 [排程更新部署]。

在 [新增更新部署] 畫面上，指定下列資訊：

* **名稱**提供更新部署的唯一名稱。
* **更新分類** - 選取更新部署在部署中包含的軟體類型。 此教學課程中，將所有類型保留選取。

  分類類型包括：

  * 重大更新
  * 安全性更新
  * 更新彙總套件
  * Feature Pack
  * Service Pack
  * 定義更新
  * 工具
  * 更新

* **排程設定** - 將未來的時間設定為 5 分鐘。 您也可以接受預設值，也就是目前時間之後的 30 分鐘。
您也可以指定部署是否為發生一次，或設定週期性排程。
選取 [循環]下的 [週期性]。 將預設值保留為 1 天，然後按一下 [確定]。 這樣會設定週期性的排程。

* **維護期間 (分鐘)** - 將這個值保留為預設值。 您可以指定需要執行更新部署的時段。 此設定有助於確保在您定義的服務時段內執行變更。

![更新排程設定畫面](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

完成設定排程之後，請按一下 [建立] 按鈕。 您會回到狀態儀表板。 選取 [排程更新部署] 可顯示您所建立的部署排程。

## <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理] 畫面的 [更新部署] 索引標籤上看到該部署的狀態。
當該部署目前正在執行時，狀態就會顯示為 [進行中]。
當它完成時，如果成功，狀態就會變更為 [成功]。
當部署中發生一或多個更新失敗時，狀態就會是 [部分失敗]。
按一下已完成的更新部署，即可查看該更新部署的儀表板。

![特定部署的更新部署狀態儀表板](./media/automation-tutorial-update-management/manageupdates-view-results.png)

在 [更新結果] 磚中，會有摘要提供 VM 上的更新總數和部署結果。
右邊的表格會顯示每個更新的詳細明細及安裝結果。
下列清單會顯示可用的值：

* **未嘗試** - 未安裝更新，因為在已定義的維護時段內，沒有足夠的時間可用。
* **成功** - 更新已順利完成
* **失敗** - 更新失敗

按一下 [所有記錄] 以查看部署已建立的所有記錄項目。

按一下 [輸出] 磚，以查看負責在目標 VM 上管理更新部署之 Runbook 的作業串流。

按一下 [錯誤]，即可查看部署所傳回之任何錯誤的詳細資訊。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 將 VM 上架以進行更新管理
> * 檢視更新評估
> * 排定更新部署
> * 檢視部署的結果

繼續閱讀更新管理解決方案的概觀。

> [!div class="nextstepaction"]
> [更新管理解決方案](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
