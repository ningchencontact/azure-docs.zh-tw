---
title: 在 Azure SQL 資料倉儲中使用 T-SQL 檢視 | Microsoft Docs
description: 在 Azure SQL 資料倉儲中使用 T-SQL 檢視開發解決方案的秘訣。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901715"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲中的檢視
檢視能以許多不同的方式使用，提升您的方案品質。 

Azure SQL 資料倉儲支援標準和具體化的視圖。 兩者都是使用 SELECT 運算式建立的虛擬資料表，並以邏輯資料表形式呈現給查詢。 Views 會封裝一般資料計算的複雜性，並將抽象層新增至計算變更，因此不需要重寫查詢。

## <a name="standard-view"></a>標準視圖
每次使用 view 時，標準視圖都會計算其資料。 磁片上不會儲存任何資料。 人們通常會使用標準視圖作為工具，以協助組織資料庫中的邏輯物件和查詢。 若要使用標準視圖，查詢必須直接參考它。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) 文件。

SQL 資料倉儲中的檢視只會儲存為中繼資料。 因此無法使用下列選項︰
* 沒有結構描述繫結選項
* 無法透過檢視更新基底資料表
* 無法在暫存資料表上建立檢視
* 不支援 EXPAND / NOEXPAND 提示
* SQL 資料倉儲中沒有索引檢視表

您可以使用標準視圖來強制執行資料表之間的效能優化聯結。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。 檢視的另一項好處是強制執行特定查詢或聯結提示。 以這種方式使用檢視，可確保一律以最佳方式執行聯結，使用者不需要記住其聯結的正確建構。

## <a name="materialized-view"></a>具體化檢視
具體化視圖會預先計算、儲存和維護其在 Azure SQL 資料倉儲中的資料，就像資料表一樣。 每次使用具體化視圖時，都不需要重新計算。 當資料載入基表時，Azure SQL 資料倉儲同步地重新整理具體化的視圖。  查詢最佳化工具會自動使用已部署的具體化視圖來改善查詢效能，即使查詢中未參考這些 views 也一樣。  查詢最能受益于具體化 views 的查詢（通常是具有聯結和匯總的查詢）會在產生小型結果集的大型資料表上進行。  

如需具體化視圖語法和其他需求的詳細資訊，請參閱[CREATE 具體化 view AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)。  

如需查詢微調指導方針，請參閱[使用具體化視圖進行效能微調](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)。 

## <a name="example"></a>範例
常見的應用程式模式就是在載入資料時，使用後面接著物件重新命名模式的 CREATE TABLE AS SELECT (CTAS) 來重建資料表。  下列範例會將新的日期記錄加入至日期維度。 請注意新的資料表 DimDate_New 最初是如何建立，然後重新命名，以取代原始版本的資料表。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```
不過，此方法可能導致資料表在使用者的檢視中出現和消失，還有「資料表不存在」錯誤訊息。 儘管基礎物件已重新命名，檢視可為使用者提供一致的展示層。 透過檢視存取資料，意味著使用者不需要看見基礎資料表。 這一層提供一致的使用者體驗，同時確保資料倉儲設計人員能持續改進資料模型。 能夠改進基礎資料表，意味著設計人員可在資料載入過程中使用 CTAS 充分發揮效能。   

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀](sql-data-warehouse-overview-develop.md)。


