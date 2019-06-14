---
title: 設定事件順序原則的 Azure Stream analytics
description: 這篇文章說明如何設定即使排序 Stream Analytics 中的 設定
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789470"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>設定事件順序原則的 Azure Stream analytics

本文說明如何安裝和使用 Azure Stream Analytics 中的延遲和順序的事件原則。 這些原則會套用在使用時才[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)在查詢中的子句。

## <a name="event-time-and-arrival-time"></a>事件的時間與抵達時間

您的 Stream Analytics 作業可以處理事件根據之一*事件的時間*或是*抵達時間*。 **事件/應用程式時間**時，出現在事件承載中的時間戳記 （所產生的事件）。 **抵達時間**（事件中樞/IoT 中樞或 Blob 儲存體） 為輸入來源收到事件時的時間戳記。 

根據預設，Stream Analytics 會處理事件*抵達時間*，但您可以選擇處理事件所*事件時間*利用[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)在查詢中的子句。 延遲抵達和次序不對的原則是您按照事件的時間處理事件時，才適用。 這些設定時，請考慮延遲性和正確性的需求，您的案例。 

## <a name="what-is-late-arrival-policy"></a>遲到原則為何？

有時事件會因為各種原因而延遲送達。 比方說，事件抵達晚 40 秒後將會有事件時間 = 00:10:00 和抵達時間 = 00:10:40。 如果您將遲到原則設定為 15 秒，延遲抵達超過 15 秒是要卸除 （不是 Stream analytics 處理），或其事件的時間已調整的任何事件。 在上述範例中，為事件抵達 40 秒延遲 （多個原則設定），其事件的時間將會調整為最大值的最新抵達原則 00:10:25 （抵達時間-延遲抵達的原則值）。 延遲傳入原則預設為 5 秒。

## <a name="what-is-out-of-order-policy"></a>次序不對的原則為何？ 

以及事件可能會失序。 根據遲到原則調整事件的時間之後，您也可以選擇要自動卸除或調整事件的次序不對。 如果您設定此原則為 8 秒時，重新排列抵達順序錯亂但 8 秒的時間範圍內的任何事件的事件時間。 抵達時間晚的事件將會捨棄或調整的最大值的次序不對的原則值。 預設順序的原則為 0 秒。 

## <a name="adjust-or-drop-events"></a>調整或卸除事件

如果事件抵達延遲或次序不對，根據您已設定的原則，您可以卸除 （不處理的 Stream Analytics） 這類事件，或調整其事件時間。

讓我們查看動作中這些原則的範例。
<br> **遲到原則：** 15 秒
<br> **次序不對的原則：** 8 秒

| 事件 [否]。 | 事件時間 | 抵達時間 | System.Timestamp | 說明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件抵達延遲和外部的容錯層級。 因此事件的時間取得調整為最大延遲傳入容錯。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件抵達晚，但在容錯層級。 因此不會無法取得調整事件的時間。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 事件抵達時間。 沒有所需的調整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件抵達的順序，但在 8 秒的容許範圍內。 因此，不會無法取得調整事件的時間。 供分析使用這個事件就會被視為上述事件數字 4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件抵達的順序和外部容錯的 8 秒。 因此，事件的時間會調整為外的順序錯亂容錯的最大值。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>這些設定可能會延遲我的工作的輸出？ 

是。 根據預設，次序不對原則設為零 （00 分 00 秒）。 如果您變更預設值時，作業的第一個輸出是延遲，此值 （或更新版本）。 

如果您輸入的資料分割的其中一個未收到事件，您應該預期您的輸出延遲的延遲傳入原則值。 若要了解原因，閱讀 InputPartition 錯誤一節。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>在 我的活動記錄檔中看到 LateInputEvents 訊息

這些訊息會顯示通知您，事件具有晚抵達並會捨棄或調整根據您的設定。 如果您已適當地設定遲到原則，您可以忽略這些訊息。 

此訊息的範例是： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>在 我的活動記錄檔中看到 InputPartitionNotProgressing

您的輸入的來源 （事件中樞/IoT 中樞） 可能會有多個分割區。 Azure Stream Analytics 會產生輸出時間戳記 t1，才會結合的所有資料分割都至少位於時間 t1。 例如，假設查詢是從具有兩個分割區的事件中樞分割區讀取。 其中一個分割區，P1，直到時間 t1 有事件。 另一個分割區 P2，會有事件，直到時間 t1 + x。 然後會產生輸出，直到時間 t1。 但是，如果由 PartitionId 子句沒有明確的資料分割，兩個分割區會單獨進行。 

當合併相同輸入資料流的多個資料分割時，延遲抵達容許是時間的每個分割區等候新資料的最大數量。 如果有一個資料分割事件中樞，或如果 IoT 中樞不會接收的輸入，該資料分割的時間軸不會進行直到到達延遲抵達容許臨界值。 這會延遲您的輸出所延遲抵達容許臨界值。 在這種情況下，您可能會看到下列訊息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
此訊息，通知您您的輸入中的至少一個資料分割是空的延遲抵達臨界值將會延遲您的輸出。 若要解決此問題，建議您其中一個： 
1. 請確定所有資料分割的事件中樞/IoT 中樞接收的輸入。 
2. PartitionID 子句，在您的查詢中使用資料分割。 

## <a name="next-steps"></a>後續步驟
* [時間處理考量](stream-analytics-time-handling.md)
* [串流分析中的可用計量](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
