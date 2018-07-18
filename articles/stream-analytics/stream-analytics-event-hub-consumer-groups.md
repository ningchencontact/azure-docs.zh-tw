---
title: 針對 Azure 串流分析中的事件中樞接收器進行疑難排解
description: 本文說明如何在串流分析作業中為事件中樞輸入使用多個取用者群組。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312951"
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>針對 Azure 串流分析中的事件中樞接收器進行疑難排解

您可以使用 Azure 串流分析中的 Azure 事件中樞，來內嵌或輸出作業的資料。 使用事件中樞的最佳做法是使用多個取用者群組，以確保作業延展性。 其中一個原因是在特定輸入的串流分析作業中，其讀取器數量會影響單一取用者群組中的讀取器數量。 接收器精確數量會以擴展拓撲邏輯的內部實作詳細資料為基礎。 接收器數量不會向外公開。 讀取器數量可在作業開始時或作業升級期間變更。

當接收器數目超過上限時顯示的錯誤訊息為：`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> 如果在作業升級期間變更讀取器數量，暫時性警告會寫入稽核記錄中。 串流分析作業會從這些暫時性問題中自動復原。

## <a name="add-a-consumer-group-in-event-hubs"></a>在事件中樞新增取用者群組
若要在事件中樞執行個體中新增新的取用者群組，請遵循下列步驟：

1. 登入 Azure 入口網站。

2. 找到您的事件中樞。

3. 選取 [實體] 標題下方的 [事件中樞]。

4. 依名稱選取事件中樞。

5. 在 [事件中樞執行個體] 頁面上，[實體] 標題下方，選取 [取用者群組]。 隨即列出名為 **$Default** 的取用者群組。

6. 選取 [+ 取用者群組] 以新增取用者群組。 

   ![在事件中樞新增取用者群組](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. 當您在串流分析作業中建立輸入以指向事件中樞時，您已指定取用者群組。 $Default 用於未指定任何項目時。 一旦您建立新的取用者群組後，請在串流分析作業中編輯事件中樞輸入，並指定新取用者群組的名稱。


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>讀取器數量 (每個分割區) 超過事件中樞上限 (5 個)

如果串流查詢語法參考相同的輸入事件中樞資源多次，則作業引擎會為相同取用者群組中的每個查詢使用多個讀取器。 當對同一個取用者群組有過多參考時，作業會超過五個的限制，並且擲回錯誤。 在這些狀況下，您可以使用下節中所述的解決方案，在多個取用者群組之間使用多個輸入，藉此進一步地分割。 

讀取器數量 (每個分割區) 超過事件中樞上限 (5 個) 的情況如下：

* 多個 SELECT 陳述式︰ 如果您使用多個 SELECT 陳述式參照**相同**的事件中樞輸入，則每個 SELECT 陳述式皆會造成新的接收器建立。
* UNION︰當您使用 UNION 時，就有可能會有多個輸入參照**相同**的事件中樞和取用者群組。
* SELF JOIN︰當您使用 SELF JOIN 作業時，就可能發生參照**相同**事件中樞多次的情形。

## <a name="solution"></a>解決方法

以下最佳做法可減少讀取器數量 (每個分割區) 超過事件中樞上限 (5 個) 的情況。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>使用 WITH 子句將查詢分割成多個步驟

WITH 子句會指定名為結果集的暫存，並且可由查詢中的 FROM 子句加以參照。 您可以定義單一 SELECT 陳述式執行範圍的 WITH 子句。

例如，不要使用此查詢︰

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

使用此查詢︰

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>請確定這些輸入會繫結至不同的取用者群組

如果查詢中有三個以上的輸入與同一個事件中樞取用者群組連線，請建立個別的取用者群組。 這需要建立其他串流分析輸入。


## <a name="next-steps"></a>後續步驟
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
