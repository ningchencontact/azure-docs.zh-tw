---
title: "使用 Azure 監視器建立、檢視及管理警示 - 警示 (預覽) | Microsoft Docs"
description: "使用最新且統一的 Azure 警示體驗從一個位置撰寫、檢視及管理計量和記錄警示規則。"
author: msvijayn
manager: kmadnani1
editor: 
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
ms.openlocfilehash: 5e4068cc694b623f67d998f410f207356efd873f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>使用 Azure 監視器建立、檢視及管理警示 - 警示 (預覽)

## <a name="overview"></a>概觀
本文會示範如何使用 Azure 入口網站中的最新警示 (預覽) 介面來設定警示。 警示規則的定義分成三個部分：
- 目標：要監視的特定 Azure 資源
- 條件：訊號中所示且會觸發動作的特定條件或邏輯
- 動作：傳送至通知 (電子郵件、SMS、Webhook 等) 接收者的特定呼叫。

警示 (預覽) 會使用**記錄警示**字詞來描述警示，而訊號是以 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 為基礎的自訂查詢。 現有警示體驗中稱為「[近乎即時的計量警示](monitoring-near-real-time-metric-alerts.md)」的計量警示功能，在警示 (預覽) 中稱為**計量警示**。 在*計量警示*中，某些資源類型會針對特定 Azure 資源提供[多維度計量](monitoring-metric-charts.md)，因此可針對這類資源使用其他篩選，來依據維度發出更具體的警示；這類警示稱為**多維度計量警示**。
Azure 警示 (預覽) 也會提供所有警示規則的統一檢視，並且可讓您在單一位置管理這些警示規則；包括檢視任何未解決的警示。 可從 [Azure 警示 (預覽) - 概觀](monitoring-overview-unified-alerts.md)中深入了解功能。

> [!NOTE]
> Azure 警示 (預覽) 讓您在 Azure 中建立警示時擁有最新且強化的體驗。 但您仍可使用現有的 [Azure 警示](monitoring-overview-alerts.md)體驗
>

下列是使用 Azure 警示 (預覽) 的詳細逐步指南。

## <a name="create-an-alert-rule-with-the-azure-portal"></a>使用 Azure 入口網站建立警示規則
1. 在[入口網站](https://portal.azure.com/)中選取 [監視]，並在 [監視] 區段下選擇 [警示 (預覽)]。  
    ![監視](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. 選取 [新增警示規則] 按鈕，以在 Azure 中建立新的警示。
    ![新增警示](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. [建立警示] 區段會以三個部分顯示：「定義警示條件」、「定義警示詳細資料」和「定義動作群組」。

    ![建立規則](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  若要定義警示條件，使用 [選取資源] 連結，透過選取資源來指定目標。 藉由選擇所需的「訂用帳戶」、「資源類型」，以及最後選取所需「資源」來進行適當的篩選。

    >[!NOTE]

    > 請先確認選取的資源可使用訊號，再繼續下面的步驟。

    ![選取資源](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Azure 警示 (預覽) 可讓您從單一介面建立各種類型的警示；請根據所需的警示類型選擇下一個步驟：

    - 如需**計量警示**：請遵循步驟 5 到 7，然後直接移至步驟 11
    - 如需**記錄警示**，請跳到步驟 8。

    > [!NOTE]

    > 統一的警示 (預覽) 也支援活動記錄警示。 [深入了解](monitoring-activity-log-alerts-new-experience.md)。

5. 計量警示：請確認**資源類型**是所選的平台或監視服務 (不是 Log Analytics)，選取適當的**資源**後，按一下 [完成] 按鈕以返回「建立警示」。 接著使用 [新增準則] 按鈕，從訊號選項清單中選擇特定的訊號和其監視服務，以及所列出的類型 (可供稍早選取的資源使用)。

    ![選取資源](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    > 為快速警示引進的新計量功能，只會包含在平台服務中作為計量的訊號類型中

6. 計量警示：一旦選取訊號後，即可指定警示的邏輯。 您可以使用 [顯示記錄] 來顯示訊號的記錄資料以供參考，並包含選調整時間範圍的選項 (從過去六小時到過去一週)。 當視覺效果就緒後，您就可以從所示的條件、彙總和閾值 (最後選取) 選項來選取**警示邏輯**。 如邏輯預覽所示，條件會與訊號記錄一起顯示視覺效果中，以指出觸發警示的時間。 最後，從 [期間] 選項中選擇警示尋找指定條件的持續時間，以及從 [頻率] 中選取警示的執行頻率。

    ![設定計量的訊號邏輯](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. 計量警示：如果訊號是計量，則可使用多個前述的 Azure 資源資料點或維度來篩選警示視窗。 與計量警示類似，您可以透過從 [顯示記錄] 下拉式清單中指定持續時間，來選擇訊號記錄的視覺效果。 此外，您可以選取所選計量的維度，針對所需的時間序列進行篩選，選擇維度是選用項目，最多可以使用五個維度。 您可以從所示的條件、彙總和閾值 (最後選取) 選項來選取**警示邏輯**。 如邏輯預覽所示，條件會與訊號記錄一起顯示於視覺效果中，以指出過去觸發警示的時間。 最後，從 [期間] 選項中選擇警示尋找指定條件的持續時間，以及從 [頻率] 中選取警示的執行頻率。

    ![設定多維度計量的訊號邏輯](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. 記錄警示：確認**資源類型**是分析來源 (例如 Log Analytics/Application Insights)，並在選取適當的**資源**後，按一下 [完成]。 接著使用 [新增準則] 按鈕，檢視資源可用的訊號選項清單，然後從訊號清單中選取所選記錄監視服務 (例如 Log Analytics/Application Insights) 的 [自訂記錄搜尋] 選項。

   ![選取資源 - 自訂記錄搜尋](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > 在 Log Analytics 中選擇資源時，**警示預覽**會將儲存的記錄搜尋列在「記錄」訊號類型 (已儲存的查詢)。
   因此您可以在 Analytics 中完成理想的查詢後，將其儲存以供日後使用，如需詳細資料，請參閱[在記錄分析中使用記錄搜尋](../log-analytics/log-analytics-log-searches.md)。 然後您可以直接根據這些查詢來建立警示規則，如下列範例畫面所示，其中便具有已儲存的搜尋：

   ![選取資源 - 自訂記錄搜尋](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

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
    > 在目前的警示 (Preview) 中 - 記錄搜尋警示可採用自訂「期間」和「頻率」 (以分鐘計算)。 這些值可以是 5 分鐘到 1440 分鐘 (也就是 24 小時)。 因此，假設您要將警示的期間設為三小時，則需先將它轉換成分鐘 (180 分鐘)，才能使用

11. 第二個步驟是在 [警示規則名稱] 欄位中定義警示的名稱，以及在 [描述] 中具體描述警示，並從提供的選項中選取 [嚴重性] 的值。 這些詳細資料會在所有由 Azure 監視器完成的警示電子郵件、通知或推送中重複使用。 此外，使用者可以透過適當地切換 [在建立時啟用規則] 選項，選擇在建立警示規則後立即啟用警示。

    警示詳細資料中有一些額外的功能可使用 (僅限於**記錄警示**)：

    - **隱藏警示**：當您開啟警示規則的隱藏功能時，此規則的動作會在建立新警示後停用並持續一段您所定義的時間。 此規則仍在執行中，並且會在符合準則時建立警示記錄。 讓您有時間更正問題，而不需執行重複的動作。

        ![記錄警示的隱藏警示](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. 第三個步驟 (也就是最後一個步驟) 是指定當警示條件符合時，是否有任何**動作群組**必須根據警示規則來觸發。 您可以選擇任何具有警示的現有動作群組，或建立新的動作群組。 根據選取的動作群組，當警示觸發時，Azure 將會：傳送電子郵件、傳送 SMS、呼叫 Webhook、使用 Azure Runbook 進行補救，發送到您的 ITSM 工具等等。 深入了解[動作群組](monitoring-action-groups.md)。

    針對**記錄警示**，有一些額外的功能可用來覆寫預設的動作：

    - **電子郵件通知**：覆寫透過動作群組傳送的電子郵件主旨。 您無法修改郵件的內文。
    - **包含自訂 Json 承載**：覆寫動作群組所使用的 Webhook Json，改用自訂承載取代預設承載。 如需有關 Webhook 格式的詳細資訊，請參閱[記錄警示的 Webhook 動作](monitor-alerts-unified-log-webhook.md)

        ![記錄警示的動作覆寫](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. 如果所有欄位都有效，並且有綠色勾號，您就可以按一下 [建立警示規則] 按鈕，在 Azure 監視器 - 警示 (預覽) 中建立警示。 您可以從警示 (預覽) 儀表板中檢視所有警示。

    ![建立規則](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="view-your-alerts-in-azure-portal"></a>在 Azure 入口網站中檢視警示

1. 在[入口網站](https://portal.azure.com/)中選取 [監視]，並在 [監視] 區段下選擇 [警示 (預覽)]。  

2. **警示 (預覽) 儀表板**隨即會顯示 - 其中所有 Azure 警示皆會整合，並顯示在單一面板「![警示儀表板](./media/monitoring-overview-unified/alerts-preview-overview.png)」中
3. 從儀表板的左上方到右上方，您一眼就可以看到下列項目 (按一下即可查看詳細清單)：
    - *引發的警示*：目前的警示數目，這些皆符合邏輯，並處於引發的狀態
    - *警示規則總計*：已建立的警示規則數目，而次要文字是目前啟用的數目
4. 您會看到所有引發的警示清單，使用者可以按一下清單以檢視詳細資料
5. 為協助尋找特定警示；可使用最上方的下拉式清單選項來篩選特定的訂用帳戶、資源群組和/或資源。 如需查看任何未解決的警示；可使用 [篩選警示] 選項來尋找提供的關鍵字 - 具體比對警示的名稱、警示準則、資源群組與目標資源

## <a name="managing-your-alerts-in-azure-portal"></a>在 Azure 入口網站中管理警示
1. 在[入口網站](https://portal.azure.com/)中選取 [監視]，並在 [監視] 區段下選擇 [警示 (預覽)]。  
2. 在頂端的工具列上選取 [管理規則]，即可瀏覽到規則管理區段 - 其中會列出所有已建立的警示規則 (包括已停用的警示)。
3. 若要尋找特定的警示規則，可以使用頂端的下拉式清單篩選器，這可讓您根據特定的訂用帳戶、資源群組和/或資源來篩選警示規則。 或者，使用位於警示規則清單上方且標示「篩選警示」的搜尋窗格，提供關鍵字以比對警示名稱、條件和目標資源，以顯示符合的規則。
   ![警示管理規則](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. 若要檢視或修改特定警示規則，按一下已顯示為可點按連結的規則名稱。
5. 隨即會顯示已定義的警示 - 分成三個階段的結構：1) 警示條件2) 警示詳細資料 3) 動作群組。 您可以按一下 [目標準則] 來修改警示邏輯，或使用垃圾桶圖示刪除稍早建立的邏輯後，新增準則。 同樣地，在警示詳細資料區段中 - 您可以修改 [描述] 和 [嚴重性]。 動作群組也可以變更，或使用 [新增動作群組] 按鈕製作新的動作群組來連結至警示。

   ![修改警示規則](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. 您可以使用頂端面板，對警示的變更做出下列反應：使用 [儲存] 認可對任何警示所做的變更、使用 [捨棄] 來返回而不認可任何對警示所做的變更、使用 [停用] 來停用警示，之後不再執行或觸發任何動作。 最後，使用 [刪除] 從 Azure 中永久移除整個警示規則。

## <a name="next-steps"></a>後續步驟

- 深入了解新的[近乎即時計量警示 (預覽)](monitoring-near-real-time-metric-alerts.md)
- 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
- 深入了解 [Azure 警示 (預覽) 中的記錄警示](monitor-alerts-unified-log.md)
- [深入了解警示 (預覽) 體驗中的活動記錄警示](monitoring-activity-log-alerts-new-experience.md)
