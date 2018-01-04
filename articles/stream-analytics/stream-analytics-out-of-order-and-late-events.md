---
title: "Azure 串流分析的事件順序和延遲處理 | Microsoft Docs"
description: "深入了解串流分析如何處理資料串流中順序錯亂或延遲的事件。"
keywords: "順序錯亂、延遲、事件"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 71929b449f2a0fa55327fd3f9741208506859e85
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Azure Stream Analytics 事件順序考量

## <a name="arrival-time-and-application-time"></a>抵達時間與應用程式時間

在暫存資料流的事件，每個事件指派的時間戳記。 Azure Stream Analytics 將使用抵達時間或應用程式時間指派給每個事件的時間戳記。 **System.Timestamp**資料行具有指派給事件的時間戳記。 

事件到達來源時，系統會為輸入來源指派抵達時間。 您可以使用來存取抵達時間**EventEnqueuedTime**事件中樞輸入和使用屬性[BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) blob 輸入的屬性。 

當產生此事件，而且它裝載的一部分時，會指派應用程式時間。 若要處理事件的應用程式時間，使用**依時間戳記**select 查詢中的子句。 如果**依時間戳記**子句不存在，事件會由抵達時間處理。 

Azure Stream Analytics 會產生輸出的時間戳記的順序，並提供次序不對資料處理的設定。


## <a name="handling-of-multiple-streams"></a>多個資料流處理

Azure Stream Analytics 工作結合來自多個情況下，如下所示時刻表的事件：

* 從多個分割區產生輸出。 沒有明確的查詢**由 PartitionId 分割**子句必須結合來自所有分割區的事件。
* 兩個以上不同輸入來源的聯集。
* 聯結輸入來源。

在其中會結合多個時間軸的情況下，Azure Stream Analytics 會產生輸出的時間戳記*t1*只會結合的所有來源都至少都為時間之後*t1*。 例如，假設查詢讀取從事件中樞分割區具有兩個資料分割。 其中一個資料分割， *P1*之前, 有事件*t1*。 另一個磁碟分割， *P2*之前, 有事件*t1 + x*。 然後會產生輸出之前*t1*。 但是，如果沒有明確**由 PartitionId 分割**子句中，兩個資料分割單獨進度。

延遲抵達容錯的設定用來處理缺少某些資料分割中的資料。

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>延遲抵達容錯和不按照順序的容錯設定
未依照順序的輸入串流為以下兩者其中之一：
* 排序 （而因此延遲）
* 調整系統，根據使用者指定的原則

Stream Analytics 容忍晚期和不按照順序的事件時您正在處理的應用程式時間。 下列設定可用於**事件順序**在 Azure 入口網站的選項： 

![串流分析事件處理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>延遲抵達容錯
您正在處理的應用程式時間時，只適用於延遲抵達容錯。 否則，會忽略的設定。

晚期抵達容限是抵達時間與應用程式時間之間的最大的差異。 如果在事件抵達的延遲抵達容錯度較新版本 (例如，應用程式時間*app_t* < 抵達時間*arr_t* -晚期抵達原則容錯*late_t*)，事件會調整為延遲抵達容錯的最大值 (*arr_t* - *late_t*)。 延遲抵達時間是事件的產生和收到的輸入來源在事件之間的延遲上限。 

當組合多個分割區相同的輸入資料流或多個輸入資料流時，延遲抵達容錯是每個資料分割為新的資料會在等待時間的最大數量。 

根據延遲抵達容錯調整先發生而定。 根據次序不對容錯調整下來發生的狀況。 **System.Timestamp**資料行具有指派給事件的最後一個時間戳記。

### <a name="out-of-order-tolerance"></a>次序不對容錯
事件不按順序抵達，但設定次序不對容錯間隔內重新排列時的時間戳記。 容錯時間晚抵達的事件都是：
* **調整**︰調整為看起來是在最低可接受時間抵達。 
* **捨棄**︰放棄。

Stream Analytics 重新排列次序不對容錯時間內未收到的事件，當查詢的輸出是因為不按照順序的容錯時間，而延遲。

### <a name="example"></a>範例

* 延遲抵達容許 = 10 分鐘<br/>
* 次序不對容錯 = 3 分鐘<br/>
* 按照應用時間處理<br/>
* 事件：
   1. **應用程式時間**= 00:00:00**抵達時間**= 00:10:01 **System.Timestamp** = 00:00:01，調整因為 (**抵達時間-應用程式時間**) 是多個延遲抵達容錯。
   2. **應用程式時間**= 00:00:01**抵達時間**= 00:10:01 **System.Timestamp** = 00:00:01，不會因為應用程式時間延遲抵達時間內進行調整。
   3. **應用程式時間**= 00:10:00**抵達時間**= 00:10:02 **System.Timestamp** = 00:10:00，不會因為應用程式時間延遲抵達時間內進行調整。
   4. **應用程式時間**= 00:09:00**抵達時間**= 00:10:03 **System.Timestamp** = 00:09:00，因為應用程式時間是在次序不對接受原始時間戳記容錯。
   5. **應用程式時間**= 00:06:00**抵達時間**= 00:10:04 **System.Timestamp** = 00:07:00，因為應用程式時間超過次序不對容錯調整。

### <a name="practical-considerations"></a>實際考量
如先前所述，延遲抵達容錯是應用程式時間和抵達時間之間的最大的差異。 當您正在處理的應用程式時間時，之後的時間比設定的延遲抵達容錯度的事件會進行調整之前的次序不對容錯設定會套用。 因此，有效順序是延遲抵達容錯和不按照順序的容錯的最小值。

在資料流內的次序不對事件的原因包括：
* 時鐘誤差之間寄件者。
* 變數寄件者和輸入的事件來源之間的延遲。

晚期抵達的原因包括：
* 批次和間隔的間隔後，更新版本中，傳送事件，寄件者。
* 將事件傳送者傳送和接收輸入來源事件之間的延遲。

當您設定延遲抵達容錯和特定工作的次序不對容錯時，請考慮正確性、 延遲需求和前述的因素。

以下是一些範例。

#### <a name="example-1"></a>範例 1 
查詢具有**由 PartitionId 分割**子句。 內的單一資料分割，事件會透過同步傳送方法進行傳送。 同步傳送方法會封鎖，直到事件傳送出去為止。

在此情況下，次序為零，因為傳送下一個事件之前明確經過確認的順序傳送事件。 晚期抵達是產生的事件及傳送事件，再加上寄件者與輸入的來源之間的最大延遲之間的延遲上限。

#### <a name="example-2"></a>範例 2
查詢具有**由 PartitionId 分割**子句。 內的單一資料分割，事件會透過非同步傳送方法進行傳送。 非同步傳送方法可以啟動多個傳送在相同的時間可能會導致不按照順序的事件。

在此情況下，順序及較晚抵達至少是產生的事件及傳送事件，再加上寄件者與輸入的來源之間的最大延遲之間的延遲上限。

#### <a name="example-3"></a>範例 3
查詢沒有**由 PartitionId 分割**子句，而且有至少兩個資料分割。

範例 2 相同的組態。 不過，缺少其中一個資料分割中的資料可能會延遲其他延遲傳入容錯時間的輸出。

## <a name="handling-event-producers-with-differing-timelines"></a>處理事件產生者與不同的時間軸
單一輸入的事件資料流通常會包含來自多個事件產生者，例如個別裝置的事件。 這些事件可能會到達失序先前討論過的原因。 在這些情況下，雖然跨事件產生者混雜可能很大，從單一生產者事件內混雜會是小型的 （或甚至不存在）。

Azure Stream Analytics 提供一般不按照順序的事件處理機制。 處理延遲 （在等待要連線到系統的 straggling 事件），這類機制導致卸除，或是調整事件，或兩者。

尚未在許多情況下，所要的查詢正在處理事件從不同的事件產生者獨立。 比方說，它可能彙總每個視窗中，每個裝置的事件。 在這些情況下，無須延遲一個事件產生者等候其他事件產生者趕上對應的輸出。 換句話說，就不必再處理產生者之間的時間誤差。 您可以忽略它。

當然，這表示輸出事件本身是相對於其時間戳記按順序。 下游取用者必須能夠處理這類行為。 但在輸出中的每一個事件正確。

Azure Stream Analytics 實作這項功能使用[TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx)子句。

## <a name="summary"></a>總結
* 設定延遲抵達容錯和不按照順序期間根據正確性和延遲需求。 也請考慮如何傳送事件。
* 我們建議次序不對容限是小於延遲抵達容錯。
* 在您結合多個時間軸時，缺少其中一個來源或資料分割中的資料可能會延遲其他延遲傳入容錯時間的輸出。

## <a name="get-help"></a>取得說明
如需其他協助，請再試一次[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
