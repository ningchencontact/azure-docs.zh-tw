---
title: 針對 Azure 串流分析中格式不正確的輸入事件進行疑難排解 | Microsoft Docs
description: 如何知道我輸入資料中的哪個事件造成串流分析作業中的問題
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>串流分析的常見問題以及疑難排解的步驟

## <a name="troubleshoot-for-malformed-input-events"></a>針對格式不正確的輸入事件進行疑難排解

當串流分析作業的輸入資料流包含格式不正確的訊息時，就會導致序列化問題。 格式不正確的訊息會包含不正確的序列化，例如遺漏 JSON 物件中的括號，或不正確的時間戳記格式。 當串流分析作業收到格式不正確的訊息時，它會捨棄訊息，並以警告通知使用者。 警告符號會顯示在串流分析作業的 [輸入] 圖格 (只要作業處於執行中狀態，此警告符號就會一直存在)：

![[輸入] 圖格](media/stream-analytics-malformed-events/inputs_tile.png)

您可以啟用診斷記錄以檢視警告的詳細資料。 對於格式錯誤的輸入事件，執行記錄包含具有類似以下訊息的項目：「訊息：無法從資源 <blob URI> 將輸入事件還原序列化為 json」。 

### <a name="troubleshooting-steps"></a>疑難排解步驟

1. 瀏覽至 [輸入] 圖格，然後按一下以檢視警告。
2. 輸入詳細資料圖格會顯示一組警告，以及問題的相關詳細資料。 以下是警告訊息範例，警告訊息會顯示發生 JSON 資料格式不正確的磁碟分割、位移及序號。 

   ![位移的警告訊息](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 若要取得格式錯誤的 JSON 資料，執行 CheckMalformedEvents.cs 程式碼，即可從 [GitHub 範例存放庫](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)取得。 這個程式碼會讀取磁碟分割識別碼、位移並列印位於該位移的資料。 

4. 一旦您讀取資料後，可以分析並修正序列化格式。 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>使用 Azure SQL Database 作為輸出串流分析作業的輸出時，處理重複的記錄

當您設定 Azure SQL Database 作為串流分析作業的輸出時，它會將記錄大量插入至目的地資料表。 一般情況下，Azure 串流分析保證[至少一次傳遞]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)至輸出接收，當 SQL 資料表已定義唯一限制式時，仍然可以[達到準確一次傳遞]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)至 SQL 輸出。 

一旦在 SQL 資料表上設定唯一索引鍵限制式，而且有重複的記錄插入 SQL 資料表，Azure 串流分析就會將資料分割為多個批次並以遞迴方式插入批次，直到找到單一重複記錄為止，藉此移除重複的記錄。 如果您的管線中有相當多的重複資料列，分割並以遞迴方式插入資料而逐一忽略重複項目是很費時的程序。 如果您在過去一小時內的活動記錄中看到多個索引鍵違規警告訊息，有可能是 SQL 輸出減緩整個作業。 若要解決此問題，您應藉由啟用 IGNORE_DUP_KEY 選項來[設定索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) (造成索引鍵違規的索引)。 啟用此選項可讓 SQL 在大量插入期間略過重複值，且 SQL Azure 只會產生警告訊息而非錯誤。 Azure 串流分析不會再產生主索引鍵違規錯誤。

針對數種類型的索引設定 IGNORE_DUP_KEY 時，請注意下列觀察：

* 您無法在主索引鍵或者使用 ALTER INDEX 的唯一限制式上設定 IGNORE_DUP_KEY，您必須卸除而後重新建立索引。  
* 您可以使用 ALTER INDEX 對唯一索引設定 IGNORE_DUP_KEY 選項，這與使用 CREATE INDEX 或 INDEX 定義建立的主索引鍵/唯一限制式不同。  
* 因為您無法在這類索引上強制執行唯一性，所以 IGNORE_DUP_KEY 不適用於資料行存放區索引。  

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

