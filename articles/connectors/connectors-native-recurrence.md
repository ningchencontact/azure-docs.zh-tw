---
title: 排程與循環觸發程序-Azure Logic Apps 的週期性工作
description: 排程及執行 Azure Logic Apps 中的循環觸發程序的重複執行的自動化的工作和工作流程
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297567"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>建立、 排程及執行週期性工作和工作流程與 Azure Logic Apps 中的循環觸發程序

若要定期執行工作、 處理序或工作特定的排程，您可以使用內建啟動邏輯應用程式工作流程 **『 週期-排程**觸發程序。 您可以設定日期和時間，以及啟動工作流程和重複該工作流程的循環時區。 如果週期就會遺失任何原因，此觸發程序會繼續在下一個排定的間隔重複執行。 如需有關內建的排程觸發程序和動作的詳細資訊，請參閱 <<c0> [ 排程和執行週期性自動化、 工作和使用 Azure Logic Apps 的工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

以下是一些模式，此觸發程序支援以及更進階的週期和複雜的排程：

* 立即執行並重複每隔*n*的秒數、 分鐘、 小時、 天、 週或月數。

* 開始在特定日期和時間，然後執行並重複每隔*n*的秒數、 分鐘、 小時、 天、 週或月數。

* 每天執行並重複一次或多次，例如上午 8:00 和下午 5:00。

* 每週執行並重複，但僅在特定的星期幾，例如星期六和星期日。

* 每週執行並重複，但僅在特定的星期幾和時間，例如星期一到星期五的上午 8:00 和下午 5:00。

此觸發程序和滑動視窗觸發程序之間的差異或排程重複執行的工作流程的詳細資訊，請參閱[排程和執行重複執行自動化工作、 處理序和使用 Azure Logic Apps 的工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

> [!TIP]
> 如果您要觸發邏輯應用程式，並只執行一次在未來，請參閱[執行作業只執行一次](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 基本知識[邏輯應用程式](../logic-apps/logic-apps-overview.md)。 如果您不熟悉 logic apps，了解[如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-recurrence-trigger"></a>新增循環觸發程序

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用程式設計工具隨即出現，在 [搜尋] 方塊中之後, 請輸入"recurrence"作為篩選條件。 從觸發程序 清單中，選取此觸發程序在邏輯應用程式工作流程中的第一個步驟：**週期性**

   ![選取 [週期] 觸發程序](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 設定重複的間隔和頻率。 在此範例中，將這些屬性設定為每週執行工作流程。

   ![設定間隔和頻率](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 屬性 | 必要項 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **間隔** | 是 | interval | Integer | 描述工作流程根據 frequency 多久執行一次的正整數。 以下是最小和最大間隔： <p>- Month：1-16 個月 </br>- Day：1-500 天 </br>- Hour：1-12,000 小時 </br>- Minute：1-72,000 分鐘 </br>- Second：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是「月」，則週期為每隔 6 個月。 |
   | **頻率** | 是 | frequency | 字串 | 用於週期的時間單位：**秒**、**分鐘**、**小時**、**天**、**週**或**月** |
   ||||||

   如需詳細的排程選項，開啟**加入新參數**清單。 
   選取範圍之後，觸發程序會出現任何您所選取的選項。

   ![進階排程選項](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 屬性 | 必要項 | JSON 名稱 | 類型 | 描述 |
   |----------|----------|-----------|------|-------------|
   | **時區** | 否 | timeZone | String | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 選取您要套用的時區。 |
   | **開始時間** | 否 | startTime | String | 提供開始日期和時間，格式如下： <p>YYYY-MM-DDThh:mm:ss (如果您選取時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未選取時區) <p>若要在 2017 年 9 月 18 日下午 2:00，比方說，然後指定"2017年-09-18T14:00:00"，然後選取 太平洋標準時間時區。 或是指定 "2017-09-18T14:00:00Z"，但不指定時區。 <p>**附註：** 這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不選取時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 [*我可以使用開始日期和時間的方式有哪些？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **在這幾天內** | 否 | weekDays | 字串或字串陣列 | 如果您選取 [週]，可以選取想要在哪一天或哪幾天執行工作流程：**星期一**、**星期二**、**星期三**、**星期四**、**星期五**、**星期六**和**星期日** |
   | **在這幾小時內** | 否 | hours | 整數或整數陣列 | 如果您選取 [天] 或 [週] 時，您可以選取一個或多個整數從 0 到 23 之間做為您想要執行工作流程一天的小時。 <p><p>比方說，如果您指定"10"、"12"及"14"，您取得上午 10 點、 下午 12 點及的小時、 日的下午 2 點，但一天的分鐘會根據計算的週期開始時。 若要設定的當日分鐘，指定的值**在這幾分鐘內**屬性。 |
   | **在這幾分鐘內** | 否 | minutes | 整數或整數陣列 | 如果您選取 [天] 或 [週]，可以選取從 0 到 59 的一或多個整數，來表示想要在小時的哪幾個分鐘執行工作流程。 <p>例如，您可以指定 "30" 作為分鐘標記，然後使用上個範例代表一天中的整點，這樣就會得出上午 10:30、下午 12:30 及下午 2:30。 |
   |||||

   例如，假設今天是 2017 年 9 月 4 日星期一。 下列的循環觸發程序並不會引發*更快*於開始日期和時間，其位於 2017 年 9 月 18 日星期一上午 8:00。 不過，重複排程已設定為只在星期一的上午 10:30、下午 12:30 和下午 2:30。 因此觸發程序第一次引發並建立邏輯應用程式工作流程執行個體的時間是在上午 10:30。 若要深入了解開始時間如何運作，請參閱這些[開始時間範例](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)。

   未來則會在同一天的下午 12:30 和下午 2:30 執行。 每次重複都會建立自己的工作流程執行個體。 之後，整個排程會在下個星期一全部重複一次。 [*還有其他哪些範例重複？* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![進階排程範例](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > 只有當您選取 [天] 或 [週] 作為頻率時，觸發程序才會顯示指定週期的預覽。

1. 現在，建立您其餘的工作流程，與其他動作。 您可以新增更多動作，請參閱[適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)。

## <a name="workflow-definition---recurrence"></a>工作流程定義-循環

在邏輯應用程式的基礎工作流程定義，它會使用 JSON，您可以檢視[循環觸發程序定義](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)您選擇的選項。 若要檢視此定義中，設計工具工具列上，選擇**程式碼檢視**。 若要返回設計工具，選擇 設計工具工具列上**設計工具**。

此範例會顯示循環觸發程序定義中的基礎的工作流程定義看起來如下：

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [使用延遲動作的暫停工作流程](../connectors/connectors-native-delay.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
