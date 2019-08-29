---
title: 使用 Azure 監視器 Azure Logic Apps 監視邏輯應用程式
description: 取得深入解析和偵測資料, 以使用 Azure 監視器記錄進行邏輯應用程式執行的疑難排解和診斷
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: a038a05f03ce7a209ae82203441750749bc6c4c4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138724"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>使用 Azure 監視器記錄, 取得邏輯應用程式的深入解析和偵測資料

若要監視並取得更豐富的邏輯應用程式詳細資料, 請在建立邏輯應用程式時開啟[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)。 當您在 Azure 入口網站中安裝 Logic Apps 管理解決方案時, Azure 監視器記錄可為您的邏輯應用程式提供診斷記錄和監視。 此解決方案也可提供邏輯應用程式執行的彙總資訊，以及狀態、執行時間、重新提交狀態和相互關聯識別碼等特定詳細資料。 本文說明如何開啟 Azure 監視器記錄, 讓您可以查看邏輯應用程式執行的運行時間事件和資料。

本主題說明當您建立邏輯應用程式時, 如何設定 Azure 監視器記錄。 若要開啟現有邏輯應用程式的 Azure 監視器記錄, 請遵循下列步驟來[開啟診斷記錄, 並將邏輯應用程式執行時間資料傳送至 Azure 監視器記錄](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

> [!NOTE]
> 本頁先前所述的步驟, 說明如何使用 Microsoft Operations Management Suite (OMS) 來執行這些工作 (已[于2019年1月淘汰](../azure-monitor/platform/oms-portal-transition.md)), 並將這些步驟取代為[Azure 監視器記錄](../azure-monitor/platform/data-platform-logs.md), 這會取代「Log Analytics」一詞。 記錄資料仍儲存在 Log Analytics 工作區中，並仍由相同的 Log Analytics 服務收集和分析。 如需詳細資訊, 請參閱[Azure 監視器術語變更](../azure-monitor/terminology.md)。

## <a name="prerequisites"></a>必要條件

開始之前，您需要有 Log Analytics 工作區。 了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

## <a name="turn-on-logging-for-new-logic-apps"></a>開啟新邏輯應用程式的記錄功能

1. 在[Azure 入口網站](https://portal.azure.com)中, 建立您的邏輯應用程式。 在主要 Azure 功能表中，選取 [建立資源] > [整合] > [邏輯應用程式]。

   ![建立新的邏輯應用程式](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. 在 [**邏輯應用程式**] 底下, 遵循下列步驟:

   1. 為您的邏輯應用程式命名並選取您的 Azure 訂用帳戶。

   1. 建立或選取一個 Azure 資源群組。 選取邏輯應用程式的 [位置]。

   1. 在 [ **Log Analytics**] 底下, 選取 [**開啟**]。

   1. 從 [ **Log Analytics 工作區**] 清單中, 選取您想要從邏輯應用程式執行傳送資料的工作區。

      ![提供邏輯應用程式資訊](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      完成此步驟之後，Azure 會建立您的邏輯應用程式，此應用程式現在會與您的 Log Analytics 工作區建立關聯。 此外, 此步驟會自動在您的工作區中安裝 Logic Apps 管理解決方案。

   1. 當您完成時，選取 [建立]。

1. 若要檢視您的邏輯應用程式執行，請[繼續進行這些步驟](#view-logic-app-runs-oms)。

## <a name="install-logic-apps-management-solution"></a>安裝 Logic Apps 管理解決方案

如果您已在建立邏輯應用程式時設定 Azure 監視器記錄, 請略過此步驟。 您已安裝 Logic Apps 管理解決方案。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [所有服務]。 在搜尋方塊中, 尋找「log analytics 工作區」, 然後選取 [ **Log analytics 工作區**]。

   ![選取 [Log Analytics 工作區]](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. 在 [ **Log Analytics 工作區**] 底下, 選取您的工作區。

   ![選取 Log Analytics 工作區](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. 在 [總覽] 窗格的 [**開始使用 Log Analytics**  > **設定監視解決方案**] 底下, 選取 [ **View 解決方案**]。

   ![選取 [查看解決方案]](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. 在 **[總覽**] 底下, 選取 [**新增**]。

   ![選取 [新增]](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. 在**Marketplace**開啟之後, 請在搜尋方塊中輸入「邏輯應用程式管理」, 然後選取 [ **Logic Apps 管理**]。

   ![選擇 [Logic Apps 管理]](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. 在 [解決方案描述] 窗格上, 選取 [**建立**]。

   ![針對 [Logic Apps 管理] 選取 [建立]](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. 請檢查並確認您要在其中安裝解決方案的 Log Analytics 工作區, 然後再次選取 [**建立**]。

   ![針對 [Logic Apps 管理] 選取 [建立]](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   在 Azure 將解決方案部署至包含 Log Analytics 工作區的 Azure 資源群組之後, 解決方案會出現在工作區的 [摘要] 窗格上。

   ![工作區摘要窗格](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>檢視邏輯應用程式執行資訊

邏輯應用程式開始執行後，您就可以在 [Logic Apps 管理] 圖格上檢視這些執行的狀態和計數。

1. 移至您的 Log Analytics 工作區, 然後選取 [**工作區摘要** > ] [**Logic Apps 管理**]。

   ![邏輯應用程式執行狀態和計數](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   在這裡，您的邏輯應用程式執行會依名稱或執行狀態分組。 此頁面也將同時顯示邏輯應用程式執行動作或觸發程序失敗的相關詳細資料。

   ![邏輯應用程式執行的狀態摘要](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. 若要檢視特定邏輯應用程式或狀態的執行，請選取邏輯應用程式或狀態的資料列。

   以下範例顯示特定邏輯應用程式的所有執行：

   ![查看邏輯應用程式的執行和狀態](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   此頁面具有 advanced 選項: 

   * **追蹤的屬性**資料行:針對您設定追蹤屬性的邏輯應用程式 (依動作分組), 您可以從這個資料行查看這些屬性。 若要查看這些追蹤的屬性, 請選取 [ **view**]。 請可以使用資料行篩選來搜尋追蹤屬性。

      ![檢視邏輯應用程式的追蹤屬性](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      初次顯示任何新增的追蹤屬性可能需費時 10-15 分鐘。 深入了解[如何將追蹤屬性新增至應用程式邏輯](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)。

   * **重新提交**:您可以重新提交一個或多個已失敗、已成功或仍在執行中的邏輯應用程式。 選取您要重新提交的執行核取方塊, 然後選取 [**重新提交**]。

     ![重新提交邏輯應用程式執行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. 若要篩選您的結果, 您可以執行用戶端和伺服器端的篩選。

   * **用戶端篩選：** 針對每個資料行, 選取您想要的篩選準則, 例如:

     ![資料行篩選範例](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **伺服器端篩選**：若要選取特定時間範圍或限制出現的執行次數, 請使用頁面頂端的 [範圍] 控制項。 根據預設，一次只能出現 1,000 筆記錄。

     ![變更時間範圍](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. 若要查看特定執行的所有動作及其詳細資料, 請選取邏輯應用程式執行的列。

   以下範例顯示特定邏輯應用程式執行的所有動作和觸發程式:

   ![檢視邏輯應用程式執行的動作](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. 在任何結果頁面上, 若要查看結果背後的查詢, 或查看所有結果, 請選取 [**查看全部**], 這會開啟 [**記錄**] 頁面。

   ![查看所有結果](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   在 [**記錄**檔] 頁面上, 您可以選擇下列選項:

   * 若要在資料表中查看查詢結果, 請選取 [**資料表**]。

   * 查詢會使用[Kusto 查詢語言](https://aka.ms/LogAnalyticsLanguageReference), 如果您想要查看不同的結果, 您可以進行編輯。 若要變更查詢, 請更新查詢字串, 然後選取 [**執行**] 來查看資料表中的結果。 

     ![Log Analytics-查詢檢視](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>後續步驟

* [監視 B2B 訊息](../logic-apps/logic-apps-monitor-b2b-message.md)