---
title: 延遲工作流程-Azure Logic Apps 中的下一個動作
description: 等待執行下一個動作在邏輯應用程式工作流程中，Azure Logic Apps 中使用的延遲直到動作。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297644"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Azure Logic Apps 中執行的下一個動作的延遲

若要讓邏輯應用程式等候一段時間再執行下一個動作，您可以加入內建**延遲-排程**之前在邏輯應用程式的工作流程動作的動作。 或者，您可以加入內建**延遲到-排定**等待特定的日期和時間，再執行下一個動作的動作。 如需有關內建的排程動作和觸發程序的詳細資訊，請參閱 <<c0> [ 排程和執行週期性自動化、 工作和使用 Azure Logic Apps 的工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

* **延遲**:等候的時間單位，例如秒、 分鐘、 小時、 天、 週或幾個月，在下一個動作執行之前指定的數目。

* **延遲到**:等到下一個動作執行之前的時間與指定的日期。

以下是一些範例方法使用這些動作：

* 等到工作日再透過電子郵件傳送狀態更新。

* 延遲工作流程，直到 HTTP 呼叫完成之前繼續進行並擷取資料。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 基本知識[邏輯應用程式](../logic-apps/logic-apps-overview.md)。 您可以使用動作之前，您的邏輯應用程式必須先啟動以觸發程序。 您可以使用任何觸發程序並將其他動作之前加入延遲動作。 本主題使用 Office 365 Outlook 觸發程序。 如果您不熟悉 logic apps，了解[如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>新增延遲動作

1. 在邏輯應用程式設計工具中，您要新增延遲動作，步驟下選擇**新增步驟**。

   若要加入步驟之間的延遲動作，請將指標移的箭號連接的步驟。 選擇加號 （+），然後再選取**新增動作**。

1. 在 [搜尋] 方塊中，輸入 「 延遲 」 作為篩選條件。 從 [動作] 清單中，選取此動作：**Delay**

   ![新增 「 延遲 」 動作](./media/connectors-native-delay/add-delay-action.png)

1. 指定下一個動作執行之前要等待的時間的量。

   ![一段時間的延遲](./media/connectors-native-delay/delay-time-intervals.png)

   | 屬性 | JSON 名稱 | 必要項 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | 計數 | count | 是 | Integer | 要延遲的時間單位數 |
   | 單位 | unit | 是 | String | Jednotka č a s，例如： `Second`， `Minute`， `Hour`， `Day`， `Week`，或 `Month` |
   ||||||

1. 新增您想要在您的工作流程中執行的任何其他動作。

1. 完成後，儲存邏輯應用程式。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>新增延遲-直到動作

1. 在邏輯應用程式設計工具中，您要新增延遲動作，步驟下選擇**新增步驟**。

   若要加入步驟之間的延遲動作，請將指標移的箭號連接的步驟。 選擇加號 （+），然後再選取**新增動作**。

1. 在 [搜尋] 方塊中，輸入 「 延遲 」 作為篩選條件。 從 [動作] 清單中，選取此動作：**延遲直到**

   ![新增「延遲直到」動作](./media/connectors-native-delay/add-delay-until-action.png)

1. 為您想要繼續工作流程中提供的結束日期和時間。

   ![指定何時結束延遲的時間戳記](./media/connectors-native-delay/delay-until-timestamp.png)

   | 屬性 | JSON 名稱 | 必要項 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | 是 | String | 結束日期和時間繼續工作流程使用此格式： <p>YYYY-MM-DDThh:mm:ssZ <p>比方說，如果您想在 2017 年 9 月 18 日下午 2:00 時，指定"2017年-09-18T14:00:00Z"。 <p>**附註：** 此時間格式必須遵循[ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)中[UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)，但不含[UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 沒有時區中，您必須新增不包含任何空格結尾的字母"Z"。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 |
   ||||||

1. 新增您想要在您的工作流程中執行的任何其他動作。

1. 完成後，儲存邏輯應用程式。

## <a name="next-steps"></a>後續步驟

* [建立、 排程及執行週期性工作和工作流程的循環觸發程序](../connectors/connectors-native-recurrence.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)