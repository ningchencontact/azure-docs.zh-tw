---
title: "建立、 檢視及管理警示使用 Azure 監視的警示 （預覽） |Microsoft 文件"
description: "使用新的統一 Azure 警示體驗撰寫、 檢視及管理度量和記錄警示規則從一個位置。"
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: e3902ec5fc27c829fa97042d15552c8c895c6408
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>建立、 檢視及管理警示使用的 Azure 監視的警示 （預覽）

## <a name="overview"></a>概觀
這篇文章會示範如何設定使用新的警示 （預覽） 介面，在 Azure 入口網站內的警示。 警示規則的定義是三個部分：
- 目標： 特定 Azure 資源，這是要監視
- 條件： 特定條件或邏輯的信號所示，當應該觸發動作
- 動作： 傳送至通知的收件者的特定的呼叫-電子郵件、 SMS、 webhook 等等。

警示 （預覽） 會使用詞彙**記錄檔警示**來描述其中的訊號是自訂查詢是根據的警示[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md)。 度量的警示功能稱為[接近即時度量警示](monitoring-near-real-time-metric-alerts.md)中現有的警示經驗會稱為**度量警示**警示 （預覽） 中。 在*度量警示*，某些資源類型提供[多維度計量](monitoring-metric-charts.md)特定的 Azure 資源，並因此發出警示的更特定的使用其他篩選器上進行這類資源維度;這類警示指**多維度計量警示**。 Azure 的警示 （預覽） 也會提供統一的檢視所有警示的規則和能力，來管理它們的單一位置;包括檢視任何未解決的警示。 深入了解功能[Azure Alerts(Preview)-概觀](monitoring-overview-unified-alerts.md)。

> [!NOTE]
> 而 Azure 警示 （預覽） 提供新增和強化的經驗，在 Azure 中建立警示。 現有[Azure 警示](monitoring-overview-alerts.md)經驗會保持可用狀態
>

詳細的下列是使用 Azure 警示 （預覽） 的逐步指南。

## <a name="create-an-alert-rule-with-the-azure-portal"></a>使用 Azure 入口網站建立警示規則
1. 在[入口網站](https://portal.azure.com/)，選取**監視器**，並在 [監視] 區段中的選擇**警示 （預覽）**。  
    ![監視](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. 選取**新的警示規則**按鈕，以在 Azure 中建立新的警示。
    ![新增警示](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. 建立警示 區段會顯示三個部分組成：*定義警示條件*，*定義警示的詳細資料*，和*定義動作群組*。
    ![建立規則](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  第一個使用來定義警示的條件**選取資源**連結，並指定目標中，選取資源。 適當地藉由選擇所需的篩選器*訂用帳戶*，*資源類型*，以及最後選取所需*資源*。 因為 Azure 警示 （預覽） 可以建立各種類型的單一介面; 內的警示根據所需的警示類型選擇為下一個步驟：
    - 如**度量警示**： 遵循步驟 5 到 7，然後移至步驟 11 直接
    - 如**記錄檔警示**，跳到步驟 8 及更新版本

5. *度量的警示*： 確保**資源類型**是所選平台或監視的服務 (以外*記錄分析*)，然後一次適當**資源**是按一下 選擇*完成*按鈕，返回 建立警示。 接下來使用**新增準則**按鈕來選擇特定的訊號清單訊號選項、 監視服務，以及所列出的類型可供使用稍早選取的資源。
    ![選取的資源](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]
    > 快速警示只引進新的計量功能訊號類型中包含從平台服務的度量資訊
  

6. *度量的警示*： 一旦選取訊號時，所述的警示的邏輯。 訊號的歷程資料顯示調整時間視窗中使用的選項，供您參考**顯示歷程記錄**、 過去一週不同從過去六個小時。 與就地，視覺效果**警示邏輯**可供選取的狀態、 彙總及最後臨界值的顯示選項。 預覽中提供的邏輯，以條件訊號歷程記錄，以及視覺效果中顯示，表示會有觸發警示時。 最後，指定哪些持續時間，如警示看起來應該指定的條件進行選擇從**期間**選項以及選取執行警示的頻率**頻率**。
    ![設定標準訊號邏輯](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *度量的警示*： 如果訊號度量，則可使用前述的 Azure 資源的多個資料點或維度篩選警示的視窗。 類似於度量的警示，視覺效果的訊號歷程記錄可選擇所指出的持續期間**顯示歷程記錄**下拉式清單。 此外，選取所選度量的維度來篩選所需的時間序列。選擇維度是選擇性，而且可以使用向上到五個維度。 **警示邏輯**可供選取的狀態、 彙總及最後臨界值的顯示選項。 預覽中提供的邏輯，以條件就會指出警示會有在過去觸發時顯示訊號歷程記錄，以及視覺效果中。 最後，指定哪些持續時間，如警示看起來應該指定的條件進行選擇從**期間**選項以及選取執行警示的頻率**頻率**。
    ![設定多維度計量訊號邏輯](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *記錄警示*： 確保**資源類型**是類似的分析來源*記錄分析*，然後一次適當**資源**是選擇按一下*完成* 按鈕。 接下來使用**新增準則**按鈕，以檢視訊號可用的選項清單資源，然後從訊號清單**自訂記錄檔搜尋**選項。
   ![選取資源的自訂記錄檔搜尋](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

9.  *記錄警示*： 警示查詢選取之後，可以中所述**搜尋查詢**欄位; 如果查詢語法不正確的欄位會以紅色顯示錯誤。 如果查詢語法不正確-參考指定查詢的歷程資料會顯示為選項來調整從過去六個小時的時間間隔到最後一週的圖形。 
   ![設定警示規則](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

    > [!NOTE]
    > 歷程記錄資料視覺效果，才可以顯示查詢結果是否時間詳細資料。 如果您將查詢結果的摘要的資料或特定資料行值-相同會顯示為單數的繪圖 

10.  *記錄警示*： 與備妥，視覺效果**警示邏輯**可供選取的狀態、 彙總及最後臨界值的顯示選項。 最後，指定在邏輯，來評估指定的條件進行的時間使用**期間**選項。 選取執行警示的頻率以及**頻率**。
如**記錄檔警示**可以根據警示：
   - *記錄數目*： 如果查詢所傳回的記錄的計數是大於或小於提供的值，會建立警示。
   - *度量的度量*： 如果每個建立警示*彙總值*結果中超過提供的臨界值，而且它*分組*選擇值。 違反警示的次數是所選的時段中已超過閾值的次數。 您可以指定總破壞任何組合的漏洞，整個結果集或連續的漏洞，需要違反必須位於連續取樣。

   深入了解[記錄警示和其類型](monitor-alerts-unified-log.md)

    > [!TIP]
    > 目前的警示 (Preview)-記錄檔搜尋警示可能需要自訂*期間*和*頻率*分鐘中的值。 值可能與 5 分鐘到 1440 分鐘 （也就是） 24 小時。 因此如果您要警示的期限設為假設三小時內，將它轉換成分鐘-180 分鐘，才能使用

11. 在第二個步驟中，定義您在警示的名稱**警示規則名稱**欄位連同**描述**詳述警示的詳細資訊和**嚴重性**值提供的選項。 這些詳細資料會在所有警示電子郵件、 通知或由 Azure 監視的推播重複使用。 此外，使用者可以選擇以進行立即啟用警示規則上建立適當地切換**啟用規則，在建立時**選項。

如**記錄檔警示**，一些額外的功能時只能在警示詳細資料：
- *抑制警示*： 當您開啟 警示規則隱藏項目時，規則的動作會停用的定義長度的時間後建立新的警示。 此規則仍在執行，並提供符合準則，則會建立警示記錄。 允許您的時間來修正問題，而不需執行重複的動作。
    ![抑制顯示警示的記錄檔警示](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. 在第三個和最後一個步驟中，指定如果有任何**動作群組**必須符合警示條件時觸發警示的規則。 您可以選擇任何現有的動作群組的警示，或建立新的動作群組。 根據選取的動作群組，當警示已觸發程序 Azure 將： 傳送 email(s)、 傳送 SMS(s)、 呼叫 Webhook(s)、 補救使用 Azure Runbook，推入到您 ITSM 工具等等。 深入了解[動作群組](monitoring-action-groups.md)。

如**記錄檔警示**一些額外的功能是可用來覆寫預設的動作：
- *電子郵件通知*： 覆寫動作群組透過傳送電子郵件中的主體。 您無法修改郵件的內文。
- *包括自訂 Json 承載*： 會覆寫 webhook 動作群組所使用的 Json，而改用取代自訂承載中的預設裝載![動作會覆寫記錄檔警示](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. 如果所有欄位都都有效，並以綠色勾號**建立警示規則**按鈕，可以和 Azure 監視-中建立警示的警示 （預覽）。 您可以從警示 （預覽） 的儀表板檢視所有警示。
    ![規則建立](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="view-your-alerts-in-azure-portal"></a>在 Azure 入口網站中檢視警示
1. 在[入口網站](https://portal.azure.com/)，選取**監視器**，並在 [監視] 區段中的選擇**警示 （預覽）**。  

2. **警示 （預覽） 儀表板**會顯示為其中所有 Azure 警示會整合，並顯示在單數面板![警示儀表板](./media/monitoring-overview-unified/alerts-preview-overview.png)
1. 頂端由左到右，從儀表板會顯示您一眼下-按一下就可查看詳細的清單：
    - *引發警示*： 產生的警示目前符合邏輯，並引發狀態中的數目
    - *總計警示規則*： 號碼建立警示規則，以及在 subtext，目前已啟用的數字
2. 所有引發的警示清單會顯示讓使用者可以按一下以檢視詳細資料
3. 達到中尋找特定警示。其中一個可用來篩選特定於最上方的下拉式清單選項*訂用帳戶、 資源群組和/或資源*。 進一步的任何未解決警示，其中一種用法*篩選警示*選項，找不到提供的關鍵字-具有特定比對警示*名稱、 警示準則，資源群組與目標資源*

## <a name="managing-your-alerts-in-azure-portal"></a>管理您在 Azure 入口網站中的警示
1. 在[入口網站](https://portal.azure.com/)，選取**監視器**，並在 [監視] 區段中的選擇**警示 （預覽）**。  
2. 選取**管理規則**工具列上的頂端列，即可導覽到規則管理區段-其中列出所有已建立警示規則，包括已停用的警示。
3. 若要尋找特定的警示規則，其中一個可以使用下拉式清單篩選條件在頂端，shortlist 警示規則允許特定*訂用帳戶、 資源群組和/或資源*。 或者使用搜尋警示規則清單上方的窗格標示*篩選警示*，其中一個可以提供比對的關鍵字*警示名稱、 條件和目標資源*; 只顯示比對規則。 
   ![警示管理規則](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. 若要檢視或修改特定警示規則，請按一下其名稱會顯示為可點按的連結。
5. 顯示警示定義的三個階段結構中： 1） 警示條件 2） 警示詳細資料 3） 動作群組。 **目標準則**按一下就可修改警示的邏輯或新的條件可以加入後刪除較早的邏輯中使用的紙匣 圖示。 同樣地，在警示詳細資料區段-**描述**和**嚴重性**可以進行修改。 和動作群組可以變更，或可以製作新連結至警示使用**新的動作群組**按鈕![修改警示規則](./media/monitor-alerts-unified/AlertsPreviewEdit.png)
6. 使用上方面板，警示的變更可能會反射包括：*儲存*認可任何警示所做的變更*捨棄*返回不認可警示所做的變更*停用*停用警示-之後它不再執行或觸發程序的任何動作。 最後，*刪除*從 Azure 中永久移除整個警示規則。

## <a name="next-steps"></a>後續步驟

* 深入了解新的[近乎即時計量警示 (預覽)](monitoring-near-real-time-metric-alerts.md)
* 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
* 深入了解[Azure 警示 （預覽） 中的記錄檔警示](monitor-alerts-unified-log.md)
