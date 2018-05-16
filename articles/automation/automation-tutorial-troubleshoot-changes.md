---
title: 針對 Azure 虛擬機器上的變更進行疑難排解 | Microsoft Docs
description: 使用變更追蹤來針對 Azure 虛擬機器上的變更進行疑難排解。
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: 變更, 追蹤, 自動化
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/28/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 62d34f82749900e161bebdb7a1a8d470b2e85bbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-changes-in-your-environment"></a>針對您環境中的變更進行疑難排解

在本教學課程中，您會了解如何針對 Azure 虛擬機器上的變更進行疑難排解。 您可以啟用變更追蹤，來追蹤軟體、檔案、Linux 精靈、Windows 服務和 Windows 登錄機碼的變更。
識別這些組態變更可協助您找出環境中的操作問題。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 VM 上架以進行變更追蹤和清查
> * 搜尋已停止服務的變更記錄
> * 設定變更追蹤
> * 啟用活動記錄連線
> * 觸發事件
> * 檢視變更

## <a name="prerequisites"></a>先決條件

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)可保存監看員和動作 Runbook，以及監看員工作。
* 要上架的[虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登入 Azure

在 http://portal.azure.com 上登入 Azure 入口網站。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

您必須先在本教學課程中啟用 VM 的變更追蹤和清查。 如果您先前已啟用 VM 的另一個自動化解決方案，就不需要此步驟。

1. 在左側功能表上，選取 [虛擬機器]，然後從清單中選取 VM
1. 在左側功能表的 [作業] 區段下，按一下 [清查]。 [變更追蹤] 頁面隨即開啟。

![啟用變更](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) [變更追蹤] 畫面隨即開啟。 設定位置、記錄分析工作區以及要使用的自動化帳戶，然後按一下 [啟用]。 如果欄位呈現灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用相同的工作區和自動化帳戶。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作區用來收集功能和服務 (例如清查) 所產生的資料。
工作區提供單一位置來檢閱和分析來自多個來源的資料。

在上線期間，會使用 Microsoft Monitoring Agent (MMA) 和混合式背景工作佈建 VM。
此代理程式用來與 VM 通訊，並取得已安裝軟體的相關資訊。

啟用解決方案可能需要 15 分鐘。 在此期間，請勿關閉瀏覽器視窗。
啟用解決方案之後，有關在 VM 上已安裝軟體和變更的相關資訊會流向 Log Analytics。
可能需要 30 分鐘到 6 小時，資料才可供分析。

## <a name="using-change-tracking-in-log-analytics"></a>使用 Log Analytics 中的變更追蹤

變更追蹤所產生的記錄資料會傳送到 Log Analytics。 若要透過執行查詢來搜尋記錄，請選取 [變更追蹤] 視窗頂端的 [Log Analytics]。
變更追蹤資料會儲存在 **ConfigurationChange** 類型之下。 下列範例 Log Analytics 查詢會傳回所有已停止的 Windows 服務。

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

若要深入了解在 Log Analytics 中執行和搜尋記錄檔，請參閱 [Azure Log Analytics](https://docs.loganalytics.io/index)。

## <a name="configure-change-tracking"></a>設定變更追蹤

變更追蹤可讓您追蹤您 VM 上的設定變更。 下列步驟說明如何設定登錄機碼和檔案的追蹤。
 
如需選擇要收集及追蹤的檔案和登錄機碼，請選取 [變更追蹤] 頁面頂端的 [編輯設定]。

> [!NOTE]
> 清查和變更追蹤會使用相同的集合設定，且設定會在工作區層級進行設定。

在 [工作區設定] 視窗中，新增要追蹤的 Windows 登錄機碼、Windows 檔案或 Linux 檔案，如接下來三節所述。

### <a name="add-a-windows-registry-key"></a>新增 Windows 登錄機碼

1. 在 [Windows 登錄] 索引標籤上，選取 [新增]。
    [為變更追蹤新增 Windows 登錄] 視窗隨即開啟。

3. 在 [為變更追蹤新增 Windows 登錄] 上，輸入要追蹤的機碼資訊，然後按一下 [儲存]

|屬性  |說明  |
|---------|---------|
|已啟用     | 判斷是否已套用設定        |
|項目名稱     | 所要追蹤檔案的易記名稱        |
|群組     | 用於將檔案以邏輯方式分組的群組名稱        |
|Windows 登錄機碼   | 要檢查檔案的路徑。例如："HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>新增 Windows 檔案

1. 在 [Windows 檔案] 索引標籤上，選取 [新增]。 [為變更追蹤新增 Windows 檔案] 視窗隨即開啟。

1. 在 [為變更追蹤新增 Windows 檔案] 上，輸入要追蹤的檔案或目錄資訊，然後按一下 [儲存]

|屬性  |說明  |
|---------|---------|
|已啟用     | 判斷是否已套用設定        |
|項目名稱     | 所要追蹤檔案的易記名稱        |
|群組     | 用於將檔案以邏輯方式分組的群組名稱        |
|輸入路徑     | 要檢查檔案的路徑，例如："c:\temp\myfile.txt"       |

### <a name="add-a-linux-file"></a>新增 Linux 檔案

1. 在 [Linux 檔案] 索引標籤上，選取 [新增]。 [為變更追蹤新增 Linux 檔案] 視窗隨即開啟。

1. 在 [為變更追蹤新增 Linux 檔案] 上，輸入要追蹤的檔案或目錄資訊，然後按一下 [儲存]

|屬性  |說明  |
|---------|---------|
|已啟用     | 判斷是否已套用設定        |
|項目名稱     | 所要追蹤檔案的易記名稱        |
|群組     | 用於將檔案以邏輯方式分組的群組名稱        |
|輸入路徑     | 要檢查檔案的路徑。例如："/etc/*.conf"       |
|路徑類型     | 要追蹤的項目類型，可能值為 [檔案] 和 [目錄]        |
|遞迴     | 決定在尋找所要追蹤的項目時是否使用遞迴。        |
|使用 Sudo     | 此設定會決定在檢查項目時是否使用 sudo。         |
|連結     | 此設定會決定在周遊目錄時處理符號連結的方式。<br> **忽略** - 忽略符號連結，而不包含參考的檔案/目錄<br>**遵循** - 遵循遞迴期間的符號連結，而且包含參考的檔案/目錄<br>**管理** - 遵循符號連結並允許變更所傳回內容的處理方式      |

   > [!NOTE]   
   > 不建議選擇「管理」連結選項。 不支援檔案內容擷取。

## <a name="enable-activity-log-connection"></a>啟用活動記錄連線

從您 VM 上的 [變更追蹤] 頁面，選取 [管理活動記錄連線]。 此工作會開啟 [Azure 活動記錄] 頁面。 選取 [連線] 可將變更追蹤連線 VM 的 Azure 活動記錄。

此設定啟用時，瀏覽至您 VM 的 [概觀] 頁面，並選取 [停止] 來將您的 VM 停止。 出現提示時，選取 [是] 可停止 VM。 當它解除配置時，選取 [啟動] 可將您的 VM 重新啟動。

將 VM 記錄停止和啟動，可在其活動記錄中記錄事件。 瀏覽回 [變更追蹤] 頁面。 選取頁面底部的 [事件] 索引標籤。 一段時間之後，就會在圖表和資料表中顯示事件。 如同在先前步驟中，可以選取每一個事件來檢視關於事件的詳細資訊。

![在入口網站中檢視變更詳細資料](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>檢視變更

一旦啟用變更追蹤和清查解決方案後，您就可以檢視 [變更追蹤] 頁面上的結果。

從您的 VM 內，選取 [作業] 下的 [變更追蹤]。

![顯示 VM 變更清單的螢幕擷取畫面](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

圖表會顯示一段時間內已發生的變更。
在您新增活動記錄連線之後，頂端的線條圖表會顯示 Azure 活動記錄事件。
長條圖中的每個資料列都代表不同的可追蹤變更類型。
這些類型為 Linux 精靈、檔案、Windows 登錄機碼、軟體和 Windows 服務。
[變更] 索引標籤會以發生變更時間的遞減順序 (最新的優先)，顯示視覺效果中所顯示變更的詳細資料。
[事件] 索引標籤，資料表會顯示已連線的活動記錄事件，及其對應的詳細資料 (最新的優先)。

您可以在結果中看到系統已進行多次變更，包括服務和軟體的變更。 您可以使用頁面頂端的篩選條件，依 [變更類型] 或依時間範圍來篩選結果。

選取 **WindowsServices** 變更，隨即開啟 [變更詳細資料] 視窗。 變更詳細資料視窗會顯示關於變更的詳細資料，以及變更之前和之後的值。 在本範例中，軟體保護服務已停止。

![在入口網站中檢視變更詳細資料](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 將 VM 上架以進行變更追蹤和清查
> * 搜尋已停止服務的變更記錄
> * 設定變更追蹤
> * 啟用活動記錄連線
> * 觸發事件
> * 檢視變更

如需深入了解，請繼續閱讀變更追蹤和清查解決方案的概觀。

> [!div class="nextstepaction"]
> [變更管理及清查解決方案](automation-change-tracking.md)
