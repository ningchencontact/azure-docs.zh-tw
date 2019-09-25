---
title: 使用 Azure 監視器記錄監視 B2B 訊息-Azure Logic Apps |Microsoft Docs
description: 監視適用于整合帳戶和 Azure Logic Apps 的 AS2、X12 和 EDIFACT 訊息，並使用 Azure 監視器記錄設定診斷記錄
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: a4a7f951d34455f2e333f2c11e30d24efdfd22c1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261211"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用 Azure 監視器記錄監視 B2B 訊息

當您在整合帳戶中的交易夥伴之間設定了 B2B 通訊之後，那些合作夥伴就可以彼此交換訊息。 若要檢查此通訊是否以您預期的方式運作，您可以監視 AS2、X12 和 EDIFACT 訊息，並使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)來設定整合帳戶的診斷記錄。 此服務會監視您的雲端和內部部署環境，協助您維護其可用性和效能，並收集執行階段詳細資料和事件，以進行更豐富的偵錯。 您也可以將此資料與其他服務搭配使用，例如 Azure 儲存體和 Azure 事件中樞。

> [!NOTE]
> 此頁面可能仍會參考 Microsoft Operations Management Suite (OMS) (此套件將[在 2019 年 1 月淘汰](../azure-monitor/platform/oms-portal-transition.md))，但將盡可能地使用 Azure Log Analytics 來取代那些步驟。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必要條件

* 已設定診斷記錄的邏輯應用程式。 了解[如何建立邏輯應用程式](quickstart-create-first-logic-app-workflow.md)和[如何設定該邏輯應用程式的記錄](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 符合先前的需求之後，您也需要 Log Analytics 工作區，用於透過 Azure 監視器記錄來監視和追蹤 B2B 通訊。 如果您沒有 Log Analytics 工作區，請了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

* 連結至邏輯應用程式的整合帳戶。 了解[如何建立具有邏輯應用程式連結的整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

## <a name="turn-on-diagnostics-logging"></a>開啟診斷記錄

您可以直接從整合帳戶或[透過 Azure 監視器服務](#azure-monitor-service)來開啟記錄。 Azure 監視器提供基礎結構層級資料的基本監視。 深入了解 [Azure 監視器](../azure-monitor/overview.md)。

### <a name="turn-on-logging-from-integration-account"></a>從整合帳戶開啟記錄

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。 在 [監視] 下方，選取 [診斷設定]。

   ![尋找並選取整合帳戶，然後選取 [診斷設定]](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. 立即尋找並選取您的整合帳戶。 在篩選清單中，選取要套用到整合帳戶的值。
當您完成時，請選擇 [新增診斷設定]。

   | 屬性 | 值 | 描述 | 
   |----------|-------|-------------|
   | **訂用帳戶** | <*Azure-subscription-name*> | 與整合帳戶相關聯的 Azure 訂用帳戶 | 
   | **資源群組** | <*Azure-resource-group-name*> | 適用於整合帳戶的 Azure 資源群組 | 
   | **資源類型** | **整合帳戶** | 您想要開啟記錄的 Azure 資源類型 | 
   | **Resource** | <*integration-account-name*> | 您想要開啟記錄的 Azure 資源名稱 | 
   ||||  

   例如:

   ![針對整合帳戶設定診斷](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 為新的診斷設定提供名稱，然後選取您的 Log Analytics 工作區和想要記錄的資料。

   1. 選取 [傳送至 Log Analytics]。 

   1. 在 [Log Analytics] 下，選取 [設定]。 

   1. 在 [OMS 工作區] 下，選取您想要用於記錄的 Log Analytics 工作區。 

      > [!NOTE]
      > OMS 工作區已由 Log Analytics 工作區所取代。 

   1. 在 [記錄] 下，選取 [IntegrationAccountTrackingEvents] 分類，然後選擇 [儲存]。

   例如: 

   ![設定 Azure 監視器記錄，讓您可以將診斷資料傳送至記錄檔](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 現在，請[在 Azure 監視器記錄中設定 B2B 訊息的追蹤](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>透過 Azure 監視器開啟記錄

1. 在 [Azure 入口網站](https://portal.azure.com)中，於主要 Azure 功能表上選取 [監視]。 在 [設定] 下，選取 [診斷設定]。 

   ![選取 [監視] > [診斷設定] > 您的整合帳戶](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. 立即尋找並選取您的整合帳戶。 在篩選清單中，選取要套用到整合帳戶的值。
當您完成時，請選擇 [新增診斷設定]。

   | 屬性 | 值 | 描述 | 
   |----------|-------|-------------|
   | **訂用帳戶** | <*Azure-subscription-name*> | 與整合帳戶相關聯的 Azure 訂用帳戶 | 
   | **資源群組** | <*Azure-resource-group-name*> | 適用於整合帳戶的 Azure 資源群組 | 
   | **資源類型** | **整合帳戶** | 您想要開啟記錄的 Azure 資源類型 | 
   | **Resource** | <*integration-account-name*> | 您想要開啟記錄的 Azure 資源名稱 | 
   ||||  

   例如:

   ![針對整合帳戶設定診斷](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 為新的診斷設定提供名稱，然後選取您的 Log Analytics 工作區和想要記錄的資料。

   1. 選取 [傳送至 Log Analytics]。 

   1. 在 [Log Analytics] 下，選取 [設定]。 

   1. 在 [OMS 工作區] 下，選取您想要用於記錄的 Log Analytics 工作區。 

      > [!NOTE]
      > OMS 工作區已由 Log Analytics 工作區所取代。 

   1. 在 [記錄] 下，選取 [IntegrationAccountTrackingEvents] 分類，然後選擇 [儲存]。

   例如: 

   ![設定 Azure 監視器記錄，讓您可以將診斷資料傳送至記錄檔](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 現在，請[在 Azure 監視器記錄中設定 B2B 訊息的追蹤](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

## <a name="use-diagnostic-data-with-other-services"></a>將診斷資料與其他服務搭配使用

除了 Azure 監視器記錄之外, 您還可以擴充將邏輯應用程式的診斷資料與其他 Azure 服務搭配使用的方式, 例如: 

* [在 Azure 儲存體中封存 Azure 診斷記錄](../azure-monitor/platform/archive-diagnostic-logs.md)
* [將 Azure 診斷記錄串流至 Azure 事件中樞](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

您可以接著使用其他服務的遙測和分析來取得即時監視，例如 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 和 [Power BI](../azure-monitor/platform/powerbi.md)。 例如:

* [將資料從事件中樞串流至串流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [使用串流分析分析串流資料並在 Power BI 中建立即時分析儀表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根據您要設定的選項，確定您會先[建立 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)或[建立 Azure 事件中樞](../event-hubs/event-hubs-create.md)。 您接著可以選取要傳送診斷資料的目的地。
只有在您選擇使用儲存體帳戶時，才會套用保留期間。

![將資料傳送至 Azure 儲存體帳戶或事件中樞](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>支援的追蹤結構描述

Azure 支援下列追蹤結構描述類型，其中除了自訂類型外都有固定的結構描述。

* [AS2 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* [在 Azure 監視器記錄中追蹤 B2B 訊息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "在 Azure 監視器記錄中追蹤 B2B 訊息")
* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "瞭解企業整合套件")

