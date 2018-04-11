---
title: 使用 Azure 監視器來建立、檢視及管理警示 | Microsoft Docs
description: 使用最新且統一的 Azure 警示體驗從一個位置撰寫、檢視及管理計量和記錄警示規則。
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: fdb3ebe3820191a642c4503851b04dd5fc5e6048
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>使用 Azure 監視器來建立、檢視及管理警示  

## <a name="overview"></a>概觀
本文會示範如何使用 Azure 入口網站內的新警示介面來設定警示。 警示規則的定義分成三個部分：
- 目標：要監視的特定 Azure 資源
- 條件：訊號中所示且會觸發動作的特定條件或邏輯
- 動作：傳送至通知 (電子郵件、SMS、Webhook 等) 接收者的特定呼叫。

Azure 警示也提供所有警示規則的統一檢視，並可讓您在單一位置管理這些警示規則，包括檢視任何未解決的警示。 您可以從 [Azure 警示 - 概觀](monitoring-overview-unified-alerts.md)來深入了解功能。

「警示」使用**記錄警示**一詞來描述符合下列條件的警示：其訊號是以 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-analytics.md) 為基礎的自訂查詢。 [新計量警示功能](monitoring-near-real-time-metric-alerts.md)可讓您針對特定 Azure 資源的[多維度計量](monitoring-metric-charts.md)發出警示。 這類資源的警示可針對建立**多維度計量警示**的維度使用額外的篩選。


> [!NOTE]
> Azure 警示讓您在 Azure 中建立警示時擁有嶄新且增強的體驗。 但現有的[警示 (傳統)](monitoring-overview-alerts.md) 體驗也仍持續可供使用。
>

接下來將提供 Azure 警示的詳細逐步使用指南。

## <a name="create-an-alert-rule-with-the-azure-portal"></a>使用 Azure 入口網站建立警示規則
1. 在[入口網站](https://portal.azure.com/)中選取 [監視]，然後在 [監視] 區段下選擇 [警示]。  
    ![監視](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. 選取 [新增警示規則] 按鈕，以在 Azure 中建立新的警示。
    ![新增警示](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. [建立警示] 區段會以三個部分顯示：「定義警示條件」、「定義警示詳細資料」和「定義動作群組」。

    ![建立規則](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  若要定義警示條件，使用 [選取資源] 連結，透過選取資源來指定目標。 請選擇「訂用帳戶」、「資源類型」，以及最後選取所需的「資源」，來進行篩選。

    >[!NOTE]

    > 請先確認選取的資源可使用訊號，再繼續下面的步驟。

    ![選取資源](./media/monitor-alerts-unified/Alert-SelectResource.png)

 使用者介面可讓您從一個位置建立各種類型的警示。 請根據所需的警示類型，依下列方式選擇下一步：

    - 針對**計量警示**：依照步驟 5 到 7 進行操作，然後直接跳到步驟 11。
    - 針對**記錄警示**：跳到步驟 8。

    > [!NOTE]

    > 也支援「活動記錄」警示，但目前為預覽版。 [深入了解](monitoring-activity-log-alerts-new-experience.md)。

5. 計量警示：請確認已選取 [資源類型] 且訊號類型為 [計量]，然後在選擇適當的 [資源] 之後，按一下 [完成] 按鈕以返回 [建立警示]。 接著使用 [新增準則] 按鈕，從訊號選項清單中選擇特定的訊號和其監視服務，以及所列出的類型 (可供稍早選取的資源使用)。

    ![選取資源](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  所有支援[近乎即時警示](monitoring-near-real-time-metric-alerts.md)的資源在列出時，其監視服務為 [平台]，且訊號類型為 [計量]

6. 計量警示：一旦選取訊號後，即可指定警示的邏輯。 您可以使用 [顯示記錄] 來顯示訊號的記錄資料以供參考，並包含選調整時間範圍的選項 (從過去六小時到過去一週)。 當視覺效果就緒後，您就可以從所示的條件、彙總和閾值 (最後選取) 選項來選取**警示邏輯**。 如邏輯預覽所示，條件會與訊號記錄一起顯示視覺效果中，以指出觸發警示的時間。 最後，從 [期間] 選項中選擇警示尋找指定條件的持續時間，以及從 [頻率] 中選取警示的執行頻率。

    ![設定計量的訊號邏輯](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. 計量警示：如果訊號是計量，則可使用多個前述的 Azure 資源資料點或維度來篩選警示視窗。 

    a. 從 [顯示歷程記錄] 下拉式清單中選擇持續時間，以藉由視覺化方式呈現不同的期間。 您可以針對支援的計量選擇維度，以依據時間序列進行篩選；選擇維度是選擇性的，最多可以使用五個維度。 

    b. 您可以從顯示的「條件」、「彙總」及「閾值」選項中選取 [警示邏輯]。 如邏輯預覽所示，條件會與訊號記錄一起顯示於視覺效果中，以指出過去觸發警示的時間。 

    c. 若要指定持續時間，請選擇 [期間]，並藉由一併選取 [頻率] 來指定警示應有的執行頻率。

    ![設定多維度計量的訊號邏輯](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. 記錄警示：請確認 [資源類型] 是分析來源 (例如「Log Analytics」或「Application Insights」) 且訊號類型為 [記錄]，然後在選擇適當的 [資源] 後，按一下 [完成]。 接著使用 [新增準則] 按鈕，檢視資源可用的訊號選項清單，然後從訊號清單中選取所選記錄監視服務 (例如 *Log Analytics* 或 *Application Insights*) 的 [自訂記錄搜尋] 選項。

   ![選取資源 - 自訂記錄搜尋](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > [警示] 清單可匯入分析查詢作為訊號類型 - [記錄 (已儲存的查詢)]，如上圖所示。 因此使用者可在 Analytics 中完成理想的查詢後，將其儲存以供日後在警示中使用 - 如需使用儲存查詢的詳細資料，請參閱[在記錄分析中使用記錄搜尋](../log-analytics/log-analytics-log-searches.md)或[在 Application Insights 分析中的共用的查詢](../log-analytics/log-analytics-overview.md)。 

9.  *記錄警示*：選好後，即可在 [搜尋查詢] 欄位中指定警示的查詢；如果查詢語法不正確，欄位會以紅色顯示錯誤。 如果查詢語法正確 - 指定查詢的記錄資料會顯示為圖形以供參考，以及顯示調整時間範圍的選項 (從過去六小時到過去一週)。

 ![設定警示規則](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > 記錄資料的視覺效果只會在查詢結果具有時間詳細資料時才會顯示。 如果您的查詢結果的是摘要的資料或特定資料行的值，則會以單一繪圖顯示相同資料。

    >  若使用 Application Insights 的記錄警示類型是「計量測量」，您可以使用 [彙總依據] 選項來指定要群組資料的特定變數，如下所示：

    ![[依據彙總] 選項](./media/monitor-alerts-unified/aggregate-on.png)

10.  記錄警示：當視覺效果就緒後，您就可以從所示的條件、彙總和閾值 (最後選取) 選項來選取**警示邏輯**。 最後，使用 [期間] 選項，在邏輯中指定用來評估指定條件的時間。 以及，透過選取 [頻率]，來指定警示執行的頻率。
針對**記錄警示**，可以根據：
   - 記錄數目：如果查詢所傳回的記錄數目大於或小於所提供的值，則會建立警示。
   - 計量測量：如果結果中的每個「彙總值」超過提供的閾值，而且彙總值依據選擇的值來「分組」，則會建立警示。 警示的違規數是所選時段中超過閾值的次數。 您可以跨結果集針對任何違規組合指定 [違規數總計]，或指定 [連續違規] 以要求違規必須發生於連續取樣中。 深入了解[記錄警示和其類型](monitor-alerts-unified-log.md)。

    > [!TIP]
    > 目前在警示中 - 記錄搜尋警示可以採用自訂的「期間」和「頻率」值 (以分鐘為單位)。 這些值可以是 5 分鐘到 1440 分鐘 (也就是 24 小時)。 因此，假設您要將警示的期間設為三小時，則需先將它轉換成分鐘 (180 分鐘)，才能使用

11. 第二個步驟是在 [警示規則名稱] 欄位中定義警示的名稱，以及在 [描述] 中具體描述警示，並從提供的選項中選取 [嚴重性] 的值。 這些詳細資料會在所有由 Azure 監視器完成的警示電子郵件、通知或推送中重複使用。 此外，使用者可以透過適當地切換 [在建立時啟用規則] 選項，選擇在建立警示規則後立即啟用警示。

    警示詳細資料中有一些額外的功能可使用 (僅限於**記錄警示**)：

    - **隱藏警示**：當您開啟警示規則的隱藏功能時，此規則的動作會在建立新警示後停用並持續一段您所定義的時間。 此規則仍在執行中，並且會在符合準則時建立警示記錄。 讓您有時間更正問題，而不需執行重複的動作。

        ![記錄警示的隱藏警示](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. 第三個步驟 (也就是最後一個步驟) 是指定當警示條件符合時，是否有任何**動作群組**必須根據警示規則來觸發。 您可以選擇任何具有警示的現有動作群組，或建立新的動作群組。 根據選取的動作群組，當警示觸發時，Azure 將會：傳送電子郵件、傳送 SMS、呼叫 Webhook、使用 Azure Runbook 進行補救，發送到您的 ITSM 工具等等。 深入了解[動作群組](monitoring-action-groups.md)。

    針對**記錄警示**，有一些額外的功能可用來覆寫預設的動作：

    - **電子郵件通知**：覆寫透過「動作群組」傳送之電子郵件中的「電子郵件主旨」；如果前述的「動作群組」中存在一或多個電子郵件動作。 您無法修改電子郵件的本文，且此欄位**不是**用於電子郵件地址。
    - **包括自訂 JSON 承載**：覆寫「動作群組」所使用的 Webhook JSON；如果前述的「動作群組」中存在一或多個 Webhook 動作。 使用者可以指定要用於相關聯動作群組中設定之所有 Webhook 的 JSON 格式；如需有關 Webhook 格式的詳細資訊，請參閱[記錄警示的 Webhook 動作](monitor-alerts-unified-log-webhook.md)。 系統會提供 [測試 Webhook] 選項來檢查格式，且目的地會使用範例 JSON 來處理這個選項，此選項如標示所示僅供**測試**之用。

        ![記錄警示的動作覆寫](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. 如果所有欄位都有效，並且有綠色勾號，您就可以按一下 [建立警示規則] 按鈕，在 Azure [監視器 - 警示] 中建立警示。 您可以從「警示儀表板」中檢視所有警示。

    ![建立規則](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="view-your-alerts-in-azure-portal"></a>在 Azure 入口網站中檢視警示

1. 在[入口網站](https://portal.azure.com/)中選取 [監視]，然後在 [監視] 區段下選擇 [警示]。  

2. **警示儀表板**隨即顯示 - 其中會整合所有 Azure 警示，並顯示在單一面板 (![警示儀表板](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)) 中
3. 從儀表板的左上方到右上方，您一眼就可以看到下列項目 (按一下即可查看詳細清單)：
    - *引發的警示*：目前的警示數目，這些皆符合邏輯，並處於引發的狀態
    - *警示規則總計*：已建立的警示規則數目，而次要文字是目前啟用的數目
4. 您會看到所有引發的警示清單，使用者可以按一下清單以檢視詳細資料
5. 為協助尋找特定警示；可使用最上方的下拉式清單選項來篩選特定的訂用帳戶、資源群組和/或資源。 如需查看任何未解決的警示；可使用 [篩選警示] 選項來尋找提供的關鍵字 - 具體比對警示的名稱、警示準則、資源群組與目標資源

## <a name="managing-your-alerts-in-azure-portal"></a>在 Azure 入口網站中管理警示
1. 在[入口網站](https://portal.azure.com/)中選取 [監視]，然後在 [監視] 區段下選擇 [警示]。  
2. 在頂端的工具列上選取 [管理規則]，即可瀏覽到規則管理區段 - 其中會列出所有已建立的警示規則 (包括已停用的警示)。
3. 若要尋找特定的警示規則，可以使用頂端的下拉式清單篩選器，這可讓您根據特定的訂用帳戶、資源群組和/或資源來篩選警示規則。 或者，使用位於警示規則清單上方且標示「篩選警示」的搜尋窗格，提供關鍵字以比對警示名稱、條件和目標資源，以顯示符合的規則。
   ![警示管理規則](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. 若要檢視或修改特定警示規則，按一下已顯示為可點按連結的規則名稱。
5. 隨即會顯示已定義的警示 - 分成三個階段的結構：1) 警示條件2) 警示詳細資料 3) 動作群組。 您可以按一下 [目標準則] 來修改警示邏輯，或使用垃圾桶圖示刪除稍早建立的邏輯後，新增準則。 同樣地，在警示詳細資料區段中 - 您可以修改 [描述] 和 [嚴重性]。 動作群組也可以變更，或使用 [新增動作群組] 按鈕製作新的動作群組來連結至警示。

   ![修改警示規則](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. 您可以使用頂端面板，對警示的變更做出下列反應：使用 [儲存] 認可對任何警示所做的變更、使用 [捨棄] 來返回而不認可任何對警示所做的變更、使用 [停用] 來停用警示，之後不再執行或觸發任何動作。 最後，使用 [刪除] 從 Azure 中永久移除整個警示規則。

## <a name="next-steps"></a>後續步驟

- 深入了解新的[近乎即時計量警示](monitoring-near-real-time-metric-alerts.md)
- 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
- 了解 [Azure 警示中的記錄警示](monitor-alerts-unified-log.md)
- [了解 [警示 (預覽)] 體驗中的活動記錄警示](monitoring-activity-log-alerts-new-experience.md)
