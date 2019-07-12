---
title: 排程週期性工作，以滑動視窗觸發程序-Azure Logic Apps
description: 排程及執行 Azure Logic Apps 中的滑動時間範圍觸發程序的重複執行的自動化的工作和工作流程
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299498"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>建立、 排程及執行週期性工作和工作流程與 Azure Logic Apps 中的滑動視窗觸發程序

定期執行工作、 處理序或作業必須處理連續區塊中的資料，您可以開始與您的邏輯應用程式工作流程**滑動時間範圍-排程**觸發程序。 您可以設定日期和時間，以及啟動工作流程和重複該工作流程的循環時區。 如果週期會因為任何原因錯過了，此觸發程序會處理這些遺失的週期。 比方說，當同步處理您的資料庫和備份儲存體之間的資料，使用滑動視窗觸發程序，以便取得同步處理的資料而不會產生間距。 如需有關內建的排程觸發程序和動作的詳細資訊，請參閱 <<c0> [ 排程和執行週期性自動化、 工作和使用 Azure Logic Apps 的工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是一些此觸發程序支援的模式：

* 立即執行並重複每隔*n*的秒數、 分鐘或小時數。

* 開始在特定日期和時間，然後執行並重複每隔*n*的秒數、 分鐘或小時數。 此觸發程序，您可以指定開始時間在過去，所有過去的週期執行。

* 延遲特定期間之前執行的每個循環。

此觸發程序的循環觸發程序之間的差異或排程重複執行的工作流程的詳細資訊，請參閱[排程和執行重複執行自動化工作、 處理序和使用 Azure Logic Apps 的工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果您要觸發邏輯應用程式，並只執行一次在未來，請參閱[執行作業只執行一次](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 基本知識[邏輯應用程式](../logic-apps/logic-apps-overview.md)。 如果您不熟悉 logic apps，了解[如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-sliding-window-trigger"></a>新增滑動視窗觸發程序

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 在 [搜尋] 方塊中，邏輯應用程式設計工具出現之後輸入 「 sliding window 」 作為篩選條件。 從觸發程序 清單中，選取此觸發程序在邏輯應用程式工作流程中的第一個步驟：**滑動視窗**

   ![選取 「 滑動視窗 」 觸發程序](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 屬性 | 必要項 | JSON 名稱 | type | 描述 |
   |----------|----------|-----------|------|-------------|
   | **間隔** | 是 | interval | 整數 | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- Hour：1-12,000 小時 </br>- Minute：1-72,000 分鐘 </br>- Second：1-9,999,999 秒<p>比方說，如果 interval 是 6，而 frequency 是"Hour"，則週期為每隔 6 小時。 |
   | **頻率** | 是 | frequency | 字串 | 用於週期的時間單位：**第二個**，**分鐘**，或**小時** |
   ||||||

   ![進階的週期選項](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   如需更多的循環選項，開啟**加入新參數**清單。 
   選取範圍之後，觸發程序會出現任何您所選取的選項。

   | 屬性 | 必要項 | JSON 名稱 | type | 描述 |
   |----------|----------|-----------|------|-------------|
   | **Delay** | 否 | delay | 字串 | 延後循環使用每個持續時間[ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **時區** | 否 | timeZone | 字串 | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | 否 | startTime | 字串 | 提供開始日期和時間，格式如下： <p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>若要在 2017 年 9 月 18 日下午 2:00，比方說，然後指定"2017年-09-18T14:00:00"，然後選取 太平洋標準時間時區。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**注意：** 這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>簡單的排程的開始時間是第一次出現，進行進階的週期觸發程序並不會引發更快於開始時間。 [*我可以使用開始日期和時間的方式有哪些？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 現在，建立您其餘的工作流程，與其他動作。 您可以新增更多動作，請參閱[適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---sliding-window"></a>工作流程定義-滑動時間範圍

在邏輯應用程式的基礎工作流程定義中，它會使用 JSON，您可以檢視滑動視窗觸發程序定義，使用您選擇的選項。 若要檢視此定義中，設計工具工具列上，選擇**程式碼檢視**。 若要返回設計工具，選擇 設計工具工具列上**設計工具**。

此範例會示範滑動視窗觸發程序定義的可能外觀基礎的工作流程定義中的每個週期延遲所在的每小時的循環的五秒：

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [延遲工作流程中的下一個動作](../connectors/connectors-native-delay.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)