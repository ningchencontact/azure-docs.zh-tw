---
title: Azure SQL 資料倉儲中的 CREATE TABLE AS SELECT (CTAS) | Microsoft Docs
description: 說明和中 Azure SQL 資料倉儲以便開發解決方案的 CREATE TABLE AS SELECT (CTAS) 陳述式的範例。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: c8e9f3ccdfaee64f75443f6a4eb89a3df7c48b0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403699"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>在 Azure SQL 資料倉儲中建立 TABLE AS SELECT (CTAS)

這篇文章會說明 Azure SQL 資料倉儲中的 CREATE TABLE AS SELECT (CTAS) T-SQL 陳述式，以便開發解決方案。 本文也會提供程式碼範例。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) 陳述式是其中一個最重要的 T-SQL 功能。 CTAS 是建立新的資料表的 SELECT 陳述式的輸出為基礎的平行作業。 CTAS 是最簡單且最快速的方式，來建立，並將資料插入資料表，以使用單一命令。

## <a name="selectinto-vs-ctas"></a>選取此項目...至 vs。CTAS

CTAS 是自訂程度版本[選取...到](/sql/t-sql/queries/select-into-clause-transact-sql)陳述式。

以下是 SELECT 的簡單的範例...到：

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

選取此項目...為不允許您的作業期間變更分散方法或索引類型。 您建立`[dbo].[FactInternetSales_new]`使用 ROUND_ROBIN 的預設散發類型和叢集資料行存放區索引的預設資料表結構。

相反地，使用 CTAS，您可以指定分佈資料表的資料，以及資料表的結構類型。 若要轉換成 CTAS 先前的範例：

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> 如果您只想要變更您的 CTAS 作業中的索引，來源資料表是雜湊散發，維護相同的散發資料行和資料類型。 這可以避免跨散發資料移動作業期間，也就是更有效率。

## <a name="use-ctas-to-copy-a-table"></a>使用 CTAS 複製資料表

可能 CTAS 最常見用途之一建立一份資料表，才能變更 DDL。 讓我們假設您原先將資料表建立為`ROUND_ROBIN`，而現在想要將它變更為散發資料行的資料表。 CTAS 是來變更散發資料行。 您也可以使用 CTAS 來變更資料分割、 索引或資料行的類型。

讓我們假設您建立此資料表使用的預設散發類型`ROUND_ROBIN`，不指定散發資料行中的`CREATE TABLE`。

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

現在您想要建立這個資料表的新複本`Clustered Columnstore Index`，因此您可以利用叢集資料行存放區資料表的效能。 您也想要散發此資料表上`ProductKey`，因為您預期這個資料行的聯結，而且想要避免聯結期間的資料移動上`ProductKey`。 最後，您也想要加入資料分割上`OrderDateKey`，因此您可以卸除舊的資料分割，以快速刪除舊的資料。 以下是 CTAS 陳述式，這會將您的舊資料表複製到新的資料表。

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

最後，您可以重新命名資料表，以便交換在新的資料表，然後再卸除舊的資料表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>使用 CTAS 來解決不支援的功能

您也可以使用 CTAS 來解決不支援的功能下面所列的數目。 這個方法通常可以證明很有幫助，因為不只您的程式碼會相容，但它通常執行速度會在 SQL 資料倉儲。 此效能是完全平行化設計的結果。 案例包括：

* ANSI JOINS on UPDATEs
* ANSI JOINs on DELETEs
* MERGE 陳述式

> [!TIP]
> 請想想"CTAS 第一次。 」 使用 CTAS 來解決問題通常是不錯的方法，即使您如此一來撰寫更多資料。

## <a name="ansi-join-replacement-for-update-statements"></a>update 陳述式的 ANSI 聯結取代

您可能會發現您有複雜的更新。 更新執行 UPDATE 或 DELETE 使用 ANSI 聯結語法，請以一起加入兩個以上的資料表。

假設您必須更新此資料表：

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

原始的查詢可能看起來如下列範例：

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

SQL 資料倉儲不支援中的 ANSI 聯結`FROM`子句`UPDATE`陳述式，因此您無法使用上述的範例，但無法加以修改。

您可以使用 CTAS 和隱含聯結的組合來取代先前的範例：

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>delete 陳述式的 ANSI 聯結取代

有時候刪除資料的最佳方法是使用 CTAS，特別是針對`DELETE`陳述式，使用 ANSI 聯結語法。 這是因為 SQL 資料倉儲不支援中的 ANSI 聯結`FROM`子句`DELETE`陳述式。 而非刪除資料，選取您想要保留的資料。

以下是範例轉換的`DELETE`陳述式：

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>取代 merge 陳述式

您可以取代 merge 陳述式中，至少在部分中，使用 CTAS。 您可以結合`INSERT`而`UPDATE`為單一陳述式。 任何已刪除的記錄應該限制不得`SELECT`陳述式，從結果中略過。

下列範例是針對`UPSERT`:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>明確陳述資料類型和輸出可為 null

在移轉程式碼時，您可能會發現您遇到這種類型的程式碼撰寫模式：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

您可能會認為您應該將此程式碼移轉至 CTAS，而系統會以正確的方式將。 不過，還有一個隱藏的問題。

下列程式碼不會產生相同的結果：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

請注意，資料行 "result" 沿用運算式的資料類型和可為 null 值。 執行資料型別轉送可能會導致值出現細微的變化如果您不小心。

請嘗試此範例中：

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

儲存的結果值是不同的。 結果資料行中的持續的值用在其他運算式時，錯誤會變得更加明顯。

![螢幕擷取畫面的 CTAS 結果](media/sql-data-warehouse-develop-ctas/ctas-results.png)

這是很重要的資料移轉。 即使第二個查詢無疑更為準確，還有一個問題。 資料會有不同相較於來源系統時，，因而導致資料移轉的完整性問題。 這是「錯誤」答案其實是正確答案的少數原因之一！

我們會看到兩個結果之間差異的原因是因為隱含類型轉換。 在第一個範例中，資料表會定義資料行定義。 插入資料列時，就會發生隱含類型轉換。 在第二個範例中，沒有任何隱含類型轉換因為運算式會定義資料行的資料類型。

另請注意，第二個範例中的資料行已定義為 Null 的資料行，而在第一個範例中沒有。 當建立資料表時，第一個範例中時，已明確定義資料行 null 屬性。 在第二個範例中，它會保留為運算式，並依預設會產生一個 NULL 定義。

若要解決這些問題，您必須明確設定型別轉換和可 null 性在 CTAS 陳述式的 SELECT 部分中。 您無法在 CREATE TABLE 中設定這些屬性。
下列範例示範如何修正程式碼：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

請注意：

* 您可以使用轉型或轉換。
* 您可以使用 ISNULL、 不是 COALESCE，來強制可 null 性。 請參閱下列附註。
* ISNULL 是最外層的函式。
* ISNULL 的第二個部分是常數，0。

> [!NOTE]
> 正確設定可為 null 屬性，請務必使用 ISNULL 而不是 COALESCE。 COALESCE 不是具決定性的函數，並因此運算式的結果一律可為 Null。 但 ISNULL 不同。 它是具決定性。 因此，當 ISNULL 函數的第二個部分是常數或常值，產生的值會是 NOT NULL。

確保您的計算的完整性也很重要的資料表資料分割切換。 假設您有這個資料表定義為事實資料表：

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

不過，[數量] 欄位是計算的運算式。 它不是來源資料的一部分。

若要建立分割的資料集，您可能想要使用下列程式碼：

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

執行查詢就非常適合。 問題出在當您嘗試進行資料分割切換。 資料表定義不相符。 若要讓資料表定義相符，修改將 CTAS`ISNULL`函式，以保留資料行的 null 屬性。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

您可以看到類型一致性以及維護 CTAS 上的可 null 性屬性就是工程的最佳做法。 它有助於維護計算的完整性，並也可確保資料分割切換的可能。

CTAS 是其中一個最重要的陳述式，SQL 資料倉儲中。 請確定您已徹底了解。 請參閱[CTAS 文件](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)。

## <a name="next-steps"></a>後續步驟

如需更多開發祕訣，請參閱 [開發概觀](sql-data-warehouse-overview-develop.md)。

