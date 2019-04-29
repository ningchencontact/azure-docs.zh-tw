---
title: 監視 logic apps 與 Azure 監視器記錄檔-Azure Logic Apps |Microsoft Docs
description: 使用 Azure Log Analytics 取得深入解析和偵錯資料，以便進行邏輯應用程式執行的疑難排解和診斷
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106205"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視邏輯應用程式

若要監視，並取得更豐富的偵錯詳細資料，關於您的 logic apps，請開啟[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)當您建立邏輯應用程式。 Azure 監視器記錄檔會提供診斷記錄和監視您的邏輯應用程式，當您在 Azure 入口網站中安裝 Logic Apps 管理解決方案。 此解決方案也可提供邏輯應用程式執行的彙總資訊，以及狀態、執行時間、重新提交狀態和相互關聯識別碼等特定詳細資料。 本文說明如何開啟 Azure 監視器記錄檔，因此您可以檢視執行階段事件，並執行邏輯應用程式的資料。

若要開啟現有的 logic apps 的 Azure 監視器記錄檔，請遵循下列步驟[開啟診斷記錄，並將邏輯應用程式執行階段資料傳送至 Azure 監視器記錄](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

> [!NOTE]
> 以前，本頁面所描述的是如何使用 Microsoft Operations Management Suite (OMS) 來執行這些工作的步驟，但 OMS 將於 [2019 年 1 月淘汰](../azure-monitor/platform/oms-portal-transition.md)，請改為使用 Azure Log Analytics 來取代這些步驟。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必要條件

開始之前，您需要有 Log Analytics 工作區。 了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>建立 Logic Apps 時開啟診斷記錄

1. 在 [Azure 入口網站](https://portal.azure.com)中，建立一個邏輯應用程式。 選擇 [建立資源] > [整合] > [邏輯應用程式]。

   ![建立邏輯應用程式](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. 在 [建立邏輯應用程式] 下，執行下列工作：

   1. 為您的邏輯應用程式命名並選取您的 Azure 訂用帳戶。 

   1. 建立或選取一個 Azure 資源群組。

   1. 將 [Log Analytics] 設定為 [開啟]。 

   1. 在列出的 Log Analytics 工作區清單中，選取您要傳送邏輯應用程式執行相關資料的工作區。 

      ![建立邏輯應用程式](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      完成此步驟之後，Azure 會建立您的邏輯應用程式，此應用程式現在會與您的 Log Analytics 工作區建立關聯。 
      此外，此步驟也會在您的工作區中自動安裝 Logic Apps 管理解決方案。

   1. 完成之後，請選擇 [建立]。

1. 若要檢視您的邏輯應用程式執行，請[繼續進行這些步驟](#view-logic-app-runs-oms)。

## <a name="install-logic-apps-management-solution"></a>安裝 Logic Apps 管理解決方案

如果您已開啟 Azure 監視器記錄您在建立邏輯應用程式時，略過此步驟。 您已安裝 Logic Apps 管理解決方案。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [所有服務]。 在搜尋方塊中，尋找「記錄分析」，然後選取 [Log Analytics]。

   ![選取 [Log Analytics]](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. 在 [Log Analytics] 下，尋找並選取 Log Analytics 工作區。 

   ![選取 Log Analytics 工作區](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. 在 [開始使用 Log Analytics] > [設定監視解決方案] 下，選擇 [檢視解決方案]。

   ![選擇 [檢視解決方案]](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. 在 [概觀] 頁面上，選擇 [新增] 以開啟 [管理解決方案] 清單。 從該清單中，選取 [Logic Apps 管理]。 

   ![選擇 [Logic Apps 管理]](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   如果找不到該解決方案，請在清單底部選擇 [載入更多] 直到解決方案出現。

1. 選擇 [建立]，確認您想要在其中安裝解決方案的 Log Analytics 工作區，然後再次選擇 [建立]。   

   ![針對 [Logic Apps 管理] 選擇 [建立]](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   如果您不想使用現有工作區，也可以在此時建立新的工作區。

   完成時，Logic Apps 管理解決方案會出現在 [概觀] 頁面上。 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>檢視邏輯應用程式執行資訊

邏輯應用程式開始執行後，您就可以在 [Logic Apps 管理] 圖格上檢視這些執行的狀態和計數。 

1. 請移至 Log Analytics 工作區，然後開啟 [概觀] 頁面。 選擇 [Logic Apps 管理]。 

   ![邏輯應用程式執行狀態和計數](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   在這裡，您的邏輯應用程式執行會依名稱或執行狀態分組。 
   此頁面也將同時顯示邏輯應用程式執行動作或觸發程序失敗的相關詳細資料。

   ![邏輯應用程式執行的狀態摘要](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. 若要檢視特定邏輯應用程式或狀態的執行，請選取邏輯應用程式或狀態的資料列。

   以下範例顯示特定邏輯應用程式的所有執行：

   ![檢視邏輯應用程式或狀態的執行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   此頁面包含以下進階選項：

   * **追蹤的屬性：**

     此欄顯示邏輯應用程式的追蹤屬性，並依照依動作分組。 若要檢視追蹤屬性，請選擇 [檢視]。 
     請可以使用資料行篩選來搜尋追蹤屬性。
   
     ![檢視邏輯應用程式的追蹤屬性](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     初次顯示任何新增的追蹤屬性可能需費時 10-15 分鐘。 深入了解[如何將追蹤屬性新增至應用程式邏輯](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)。

   * **重新提交：** 您可以重新提交一個或多個已失敗、已成功或仍在執行的邏輯應用程式執行。 針對您要重新提交的執行選取核取方塊，然後選擇 [重新提交]。 

     ![重新提交邏輯應用程式執行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. 若要篩選這些結果，您可以執行用戶端和伺服器端篩選。

   * **用戶端篩選：** 針對每個資料行，選擇您想要的篩選條件，例如：

     ![資料行篩選範例](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **伺服器端篩選**：若要選擇特定時間範圍或限制出現的執行次數，請使用頁面頂端的範圍控制項。 根據預設，一次只能出現 1,000 筆記錄。
   
     ![變更時間範圍](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. 若要檢視特定執行的所有動作及其詳細資料，請選取邏輯應用程式執行的資料列。

   以下範例顯示特定邏輯應用程式執行的所有動作：

   ![檢視邏輯應用程式執行的動作](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. 若要在任何結果頁面上檢視結果背後的查詢，或查看所有結果，請選擇 [檢視全部] 來開啟 [記錄搜尋] 頁面。
   
   ![在結果頁面上檢視全部](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   在 [記錄搜尋] 頁面上，

   * 若要在表格中檢視查詢結果，請選擇 [表格]。

   * 若要變更查詢，您可以編輯搜尋列中的查詢字串。 
   若要獲得更佳的體驗，請選擇 [進階分析]。

     ![檢視邏輯應用程式執行的動作和詳細資料](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     在 log analytics 頁面上，您可以更新查詢，並檢視資料表的結果。 此查詢使用 [Kusto 查詢語言](https://aka.ms/LogAnalyticsLanguageReference)，如果您想要檢視不同的結果，可予以編輯。 

     ![log analytics-查詢檢視](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>後續步驟

* [監視 B2B 訊息](../logic-apps/logic-apps-monitor-b2b-message.md)