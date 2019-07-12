---
title: 在 Azure 監視器記錄檔-Azure Logic Apps 中建立 B2B 訊息的追蹤查詢 |Microsoft Docs
description: 建立查詢，以在適用於 Azure Logic Apps 的 Azure Log Analytics 中追蹤 AS2、X12 和 EDIFACT 訊息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846441"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure 監視器記錄檔中建立 Azure Logic Apps B2B 訊息的追蹤查詢

若要尋找 AS2、 X12 或 EDIFACT 訊息追蹤，搭配[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)，您可以建立篩選器動作，根據特定準則的查詢。 例如，您可以找到根據特定交換控制編號的訊息。

> [!NOTE]
> 以前，本頁面所描述的是如何使用 Microsoft Operations Management Suite (OMS) 來執行這些工作的步驟，但 OMS 將於 [2019 年 1 月淘汰](../azure-monitor/platform/oms-portal-transition.md)，請改為使用 Azure Log Analytics 來取代這些步驟。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先決條件

* 已設定診斷記錄的邏輯應用程式。 了解[如何建立邏輯應用程式](quickstart-create-first-logic-app-workflow.md)和[如何設定該邏輯應用程式的記錄](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 已設定監視和記錄的整合帳戶。 了解[如何建立整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)和[如何設定該帳戶的監視和記錄](../logic-apps/logic-apps-monitor-b2b-message.md)。

* 如果您還[將診斷資料發佈至 Azure 監視器記錄檔](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)並[設定 Azure 監視器記錄檔中追蹤的訊息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

## <a name="create-queries-with-filters"></a>建立具有篩選的查詢

若要根據特定屬性或值來尋找訊息，您可以建立使用篩選的查詢。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [所有服務]  。 在搜尋方塊中，尋找「記錄分析」，然後選取 [Log Analytics]  。

   ![選取 [Log Analytics]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. 在 [Log Analytics]  下，尋找並選取 Log Analytics 工作區。 

   ![選取 Log Analytics 工作區](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. 在工作區的功能表上，選取 [一般]  底下的 [記錄 (傳統)]  或 [記錄]  。 

   本範例說明如何使用傳統的 [記錄] 檢視。 
   如果您在 [完全發揮 Log Analytics 的體驗]  區段的 [搜尋及分析記錄]  底下選擇 [檢視記錄]  ，便可獲得 [記錄 (傳統檢視)]  。 

   ![檢視傳統記錄](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. 在查詢的編輯方塊中，開始輸入您想要尋找的欄位名稱。 當您開始輸入時，查詢編輯器會顯示可能的相符項目以及您可以使用的作業。 在建立查詢後，請選擇 [執行]  或按 Enter 鍵。

   此範例會搜尋 **LogicAppB2B** 的相符項目。 
   深入了解[如何在 Azure 監視器記錄檔中尋找資料](../log-analytics/log-analytics-log-searches.md)。

   ![開始鍵入查詢字串](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. 若要變更您想要檢視的時間範圍，請在左窗格中，從 [持續時間] 清單進行選取或拖曳滑桿。 

   ![變更時間範圍](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. 若要將篩選新增至查詢，請選擇 [新增]  。 

   ![將篩選新增至查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. 在 [新增篩選]  底下，輸入您想要尋找的篩選名稱。 如果找到篩選，請加以選取。 在左窗格中，再次選擇 [新增]  。

   例如，下面這個不同的查詢會搜尋 **Type=="AzureDiagnostics"** 事件，並藉由選取 **event_record_messageProperties_interchangeControlNumber_s** 篩選來根據交換控制編號尋找結果。

   ![選取篩選值](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   在選擇 [新增]  後，您的查詢便會更新為所選取的篩選事件和值。 
   現在也會篩選您的先前結果。 

   例如，這個查詢會搜尋 **Type=="AzureDiagnostics"** ，並藉由使用 **event_record_messageProperties_interchangeControlNumber_s** 篩選來根據交換控制編號尋找結果。

   ![篩選的結果](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>儲存查詢

若要將查詢儲存在 [記錄 (傳統)]  檢視，請遵循下列步驟：

1. 從 [記錄 (傳統)]  頁面上的查詢，選擇 [分析]  。 

   ![選擇 [分析]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. 在查詢工具列上，選擇 [儲存]  。

   ![選擇 [儲存]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. 提供查詢的相關詳細資料 (例如，為查詢提供名稱)，然後選取 [查詢]  並提供類別名稱。 完成之後，請選擇 [儲存]  。

   ![選擇 [儲存]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. 若要檢視已儲存的查詢，請返回查詢頁面。 在查詢工具列上，選擇 [已儲存的搜尋]  。

   ![選擇 [已儲存的搜尋]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. 在 [已儲存的搜尋]  下，選取您的查詢，以檢視結果。 

   ![選取查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   若要更新查詢，以找到不同的結果，請編輯查詢。

## <a name="find-and-run-saved-queries"></a>尋找並執行已儲存的查詢

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [所有服務]  。 在搜尋方塊中，尋找「記錄分析」，然後選取 [Log Analytics]  。

   ![選取 [Log Analytics]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. 在 [Log Analytics]  下，尋找並選取 Log Analytics 工作區。 

   ![選取 Log Analytics 工作區](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. 在工作區的功能表上，選取 [一般]  底下的 [記錄 (傳統)]  或 [記錄]  。 

   本範例說明如何使用傳統的 [記錄] 檢視。 

1. 查詢頁面開啟後，請在查詢工具列上，選擇 [已儲存的搜尋]  。

   ![選擇 [已儲存的搜尋]](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. 在 [已儲存的搜尋]  下，選取您的查詢，以檢視結果。 

   ![選取查詢](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   查詢會自動執行，但如果查詢因為任何原因而未能執行，請在查詢編輯器中選擇 [執行]  。

## <a name="next-steps"></a>後續步驟

* [AS2 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)