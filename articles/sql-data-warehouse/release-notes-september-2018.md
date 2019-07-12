---
title: 2018 年 9 月 Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/08/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 5041458fdc3c6a49c59f2c4c476db71098531419
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65912169"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Azure SQL 資料倉儲有哪些最新功能？ 2018 年 9 月
Azure SQL 資料倉儲會持續改進。 本文說明 2018 年 9 月導入的新功能和變更。

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>SQL 資料倉儲 Gen2 有新的較低進入點
2018 年 4 月，[Microsoft 發表了](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL 資料倉儲 Gen2，其可提供 5 倍的效能、5 倍的計算規模、4 倍的並行存取和無限制的儲存體。 如 Gigaom 在[雲端資料倉儲基準](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)中所述，SQL 資料倉儲 Gen2 **的效能比 Amazon Redshift 高 42%** 。

現在，較低進入點的 DWU500c 已正式推出 Gen2，讓您可以執行規模較小的資料倉儲或開發/測試環境，卻擁有所有最新的服務改進。 新的進入點保留了 Gen2 的所有功能，包括[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)、[超快速資料輪換](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)以及支援[即時資料倉儲](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/)。

## <a name="sql-vulnerability-assessment"></a>SQL 弱點評量
[SQL 弱點評定 (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) \(英文\) 是容易使用的服務，它會持續不斷地監控您的資料倉儲。 它可協助您隨時確保高層級的安全性且符合您組織的原則。 它會針對每個找到的問題，提供完整的安全性報告和可採取動作的補救步驟。 即使您不是安全性專家，此報告也可以讓您輕鬆地主動管理您的資料庫安全性等級，並將注意力放在影響最大的動作。 對於變更頻繁而難以追蹤的動態環境，VA 最適合用來偵測使您資料倉儲易受到攻擊的設定。

## <a name="improved-availability-with-query-restartability"></a>藉由能夠重新啟動查詢來改善可用性
執行查詢期間可能會發生任何數量的問題，而造成查詢失敗。 網路中斷、硬體失敗或其他中斷連線情形都可能導致作業中斷。 SQL 資料倉儲現在支援查詢重新啟動能力，可供步驟或陳述式層級的 SELECT 查詢使用。 

有了查詢重新啟動能力後，執行中的查詢若遇到失敗而中斷，將會自動重新啟動。 根據步驟數目、查詢形狀或查詢是否在執行期間暫止，SQL 資料倉儲引擎會重新啟動完整查詢，或從最後一個完成的查詢步驟繼續。 從使用者觀點來看，查詢就是完成了。 

## <a name="maintenance-scheduling-preview"></a>維護排程 (預覽)
Azure SQL 資料倉儲維護排程現在為預覽版。 此個新功能可以無縫整合服務健康狀態計劃性維護通知、資源健康狀態檢查監視器，以及 Azure SQL 資料倉儲維護排程服務。 維護排程可讓您排程一個時段，方便您接收新的功能、升級與修補程式。

維護排程會充分利用 Azure 監視器，並可讓客戶決定他們想要收到即將發生的維護事件通知的方式，與應該觸發哪些自動化流程來管理停機時間，並將其作業的影響降到最低。 通知可包括電子郵件或文字。 

## <a name="wide-row-support-in-polybase"></a>在 Polybase 中支援寬資料列
在將資料載入 SQL 資料倉儲時，一般的指引是使用 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase)，因為其支援平行載入資料。 此版本可支援寬度更大的資料行 (從 32 K 到 1 MB) - 可讓您載入具有寬資料列大小的資料表。 由於支援寬資料列，因此可為資料列較寬的資料表簡化其資料載入程序。

> [!Note]
> 資料列大小總計不可超過 1 MB 大小。

## <a name="additional-language-support"></a>其他語言支援
此版本導入了下列 T-SQL 語言元素的支援：

### <a name="stringsplit"></a>STRING_SPLIT
[STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) 函式會使用指定分隔符號來分割字元字串。 STRING_SPLIT 適用於資料行可能有多個值要剖析並插入到另一個資料表的資料載入案例。

#### <a name="example"></a>範例
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>COMPRESS/DECOMPRESS 函式
[COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) 函式可讓您使用 GZIP 演算法壓縮或解壓縮字串輸入。

#### <a name="example"></a>範例

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>用於卸除檢視的 IF EXISTS 子句
[DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) 陳述式中新增了 IF EXISTS 子句，從而簡化了從資料倉儲移除檢視所需的 T-SQL 程式碼。 IF EXISTS 語法在套用至 DROP VIEW 陳述式時將會卸除檢視 (如果檢視存在) 或忽略陳述式 (如果檢視不存在)。

#### <a name="example"></a>範例
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>改善了單一資料庫插入和 DDL 陳述式的編譯時間 
遵循傳統的擷取、轉換和載入 (ETL) 模型來插入資料，通常會導致執行目標為資料庫資料表的單一資料庫插入 ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql))。 此版本藉由減少執行這類陳述式所需的編譯時間，來改善單一資料庫插入作業的效能。 在某些情況下，觀察到的編譯速度提升幅度達到 3 倍之多。 這項改善會降低執行單一資料庫插入陳述式所需的時間。 

這項改善也有益於具有大量物件的資料倉儲，因為其可減少資料定義語言 (DDL) 陳述式 (包括 CREATE、ALTER 和 DELETE 作業) 的查詢編譯時間。 

最後，這項改善可減少陳述式在較寬資料表 (具有大量資料行的資料表) 上執行時的整體執行時間。 這項改善會減少查詢編譯步驟時間，從而減少查詢的整體執行時間。

## <a name="bug-fixes"></a>錯誤修正

| 標題 | 描述 |
|:---|:---|
| **針對唯一條件約束的發行版本建立統計資料時的修正** | 此修正可解決使用者遇到的錯誤，若在只有指定資料表時執行 UPDATE STATISTICS，且資料表定義了唯一條件約束，使用者就會遇到此錯誤。 |
| **針對外部資料表編譯查詢時的修正** | 此修正可解決會對涉及外部資料表的查詢影響其編譯時間的缺失。|
| **執行具有大型類型陳述式的修正** | 針對以宣告為其中一個「大型」  類型 (nvarchar(max)、varchar(max) 和 varbinary(max)) 的參數所備妥的陳述式編譯，解決其缺失。 |
| **當深度巢狀查詢發生錯誤時的修正** | 在深度巢狀查詢超過系統限制時，提供明確的錯誤訊息。|
| **當陳述式包含相互關聯的子查詢和執行時間常數時的編譯時間錯誤修正** |針對以特定方式組合了相互關聯的子查詢和執行時間常數 (例如 GETDATE()) 的查詢，解決其編譯時間錯誤。|
| **解決在取得 PDW 物件鎖定和 autostats 並行存取插槽時的逾時** |此修正會新增鎖定逾時，以防止 autostats 要求長時間封鎖原始要求。|

## <a name="next-steps"></a>後續步驟
現在您已稍微了解 SQL 資料倉儲，請了解如何快速[建立 SQL 資料倉儲][create a SQL Data Warehouse]。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙][Azure glossary]在您遇到新術語時很有幫助。 或者，也可以看一下其中一些其他 SQL 資料倉儲資源。  

* [客戶成功案例]
* [部落格]
* [功能要求]
* [影片]
* [客戶諮詢小組部落格]
* [Stack Overflow 論壇]
* [Twitter]


[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客戶諮詢小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客戶成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 論壇]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
