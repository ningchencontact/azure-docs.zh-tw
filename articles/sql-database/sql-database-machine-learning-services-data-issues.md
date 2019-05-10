---
title: 使用 R 和 SQL 資料類型和物件
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 了解如何使用資料類型及資料物件，在 R 中使用 Azure SQL Database 使用 Machine Learning 服務 （預覽），包括您可能會遇到的常見問題。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 01d3af14963e92393d34a952bddc8097b7b08f18
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232621"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>使用 Azure SQL Database Machine Learning 服務 （預覽） 中的 R 和 SQL 資料

這篇文章將討論一些在 R 與 SQL Database 之間移動資料時，可能會遇到的常見問題[（使用 R) Azure SQL Database 中的 Machine Learning 服務](sql-database-machine-learning-services-overview.md)。 使用您自己的指令碼中的資料時，您可以透過這個練習中此體驗會提供必要的背景。

您可能會遇到的常見問題包括：

- 資料類型有時不相符
- 可能會發生隱含轉換
- Cast 和 convert 作業有時會需要
- R 和 SQL 會使用不同的資料物件

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

- 若要在這些練習中執行範例程式碼，您必須具有已啟用機器學習服務 (搭配 R) 的 Azure SQL 資料庫。 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務。 請遵循[註冊預覽版](sql-database-machine-learning-services-overview.md#signup)中的步驟。

- 確定您已安裝最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但是在本快速入門中，您將使用 SSMS。

## <a name="working-with-a-data-frame"></a>使用資料框架

當您的指令碼從 R 傳回結果，sql 時，它必須傳回資料當做**data.frame**。 任何其他類型的清單、 因數、 向量或二進位資料-不論您的指令碼-產生的物件必須轉換成資料框架，如果您想要輸出做為預存程序結果的一部分。 幸運的是，有多個 R 函數，來支援變更為資料框架的其他物件。 您甚至可以將二進位模型序列化，並傳回在資料框架中，您將在本文稍後執行。

首先，讓我們來試驗-向量、 矩陣和清單-一些基本 R 物件，並查看轉換成資料框架如何變更傳遞至 SQL 輸出。

比較在 r 中的這些兩個 「 Hello World 」 指令碼指令碼看起來幾乎完全相同，但第一個會傳回單一資料行的三個值，而第二個則傳回單一的三個資料行值，每個。

**範例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**範例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

為何結果如此不同？

答案通常可以找到使用 R`str()`命令。 新增函式`str(object_name)`隨處在您的 R 指令碼，將資料傳回結構描述指定的 R 物件做為參考用訊息。 您可以檢視中的訊息**訊息**在 SSMS 中的索引標籤。

若要了解為什麼範例 1 和範例 2 中有這類不同的結果，將行`str(OutputDataSet)`結尾的`@script`變數定義在每個陳述式，就像這樣：

**範例 1 使用 str 函式新增**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**範例 2 使用 str 函式新增**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

現在，檢視中的文字**訊息**以查看輸出不同的原因。

**結果-範例 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**結果-範例 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

如您所見，稍微變化一下 R 語法中的會有很大的影響結果的結構描述。 詳細資料中的所有詳細資料，說明 R 資料類型的差異*資料結構*一節[Hadley Wickham 所著的 《 進階 R"](http://adv-r.had.co.nz)。

現在，只是注意，您需要將強制轉型成資料框架的 R 物件時，檢查預期的結果。

> [!TIP]
> 您也可以使用 R 識別函數，例如`is.matrix`， `is.vector`，以傳回內部資料結構的相關資訊。

## <a name="implicit-conversion-of-data-objects"></a>資料物件的隱含轉換

每個 R 資料物件具有它自己的規則結合其他資料物件，如果兩個資料物件具有相同數目的維度，或如果任何資料物件包含異質性的資料類型的值處理方式。

例如，假設您想要執行使用 r 的矩陣相乘您想要具有三個值的單一資料行矩陣乘以的陣列，具有四個值，並因此預期 4x3 矩陣。

首先，建立測試資料的小型資料表。

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

現在，請執行下列指令碼。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

實際上，三個值的資料行被轉換成單一資料行矩陣。 因為矩陣只是特殊案例的 R，陣列中陣列`y`會隱含地強制轉型的單一資料行矩陣，以使兩個引數相符。

**結果**

|Col1|Col2|Col3|第 4 欄|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

不過，請注意當您變更陣列的大小，會發生什麼事`y`。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

R 現在會傳回單一值做為結果。

**結果**
    
|Col1|
|---|
|1542|

原因為何？ 在此情況下，因為兩個引數可以處理為相同長度的向量，R 會傳回內積作為矩陣。  這是預期的行為，根據線性代數的規則。 不過，它可能會造成問題如果下游應用程式預期輸出結構描述不會再變更 ！

## <a name="merge-or-multiply-columns-of-different-length"></a>合併或相乘不同長度的資料行

R 提供絕佳的彈性，使用不同的大小的向量，以及將這些類似資料行的結構結合成資料框架。 向量的清單看起來會像資料表，但它們不遵循用以管理資料庫資料表的所有規則。

例如，下列指令碼的定義長度為 6 的數值陣列位置，並將它儲存在 R 變數`df1`。 此數值陣列接著會與 RTestData 資料表 （上述所建立） 的整數結合其中包含三 （3） 值，以建立新的資料框架， `df2`。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

若要填寫資料框架，R 會重複項目擷取自 RTestData 許多次，視需要來比對陣列中的項目數`df1`。

**結果**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

請記住，資料框架只看起來像資料表，但其實是一個清單的向量。

## <a name="cast-or-convert-sql-data"></a>Cast 或 convert 的 SQL 資料

R 和 SQL 使用相同的資料類型，因此當您在 SQL 中取得資料，並再將它傳遞至 R 執行階段執行查詢，某些類型的隱含轉換，通常會發生。 當資料從 R 傳回 SQL，另一組轉換將會發生。

- SQL 會將資料從查詢發送至 R 處理序，並將它轉換成內部表示法更高的效率。
- R 執行階段會將資料載入 data.frame 變數，並執行它自己的資料上的作業。
- Database engine 會將資料傳回給使用受保護的內部連接的 SQL，並顯示根據 SQL 資料類型資料。
- 您來取得資料連接到 SQL 使用的用戶端或網路程式庫可以發出 SQL 查詢和處理表格式資料集。 此用戶端應用程式可能會影響以其他方式的資料。

若要查看其運作方式，請在上執行這類查詢[AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)資料倉儲。 此檢視會傳回用來建立預測的銷售資料。

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 您可以使用任何版本的 AdventureWorks，或建立不同的查詢，使用您自己的資料庫。 重點是，嘗試處理一些包含文字、 日期時間和數字值的資料。

現在，請嘗試使用此查詢做為預存程序的輸入。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

如果您收到錯誤，您可能需要進行一些編輯查詢文字。 比方說，WHERE 子句中的字串述詞必須以兩組單引號括住。

取得查詢能夠正常運作之後，請檢閱的結果`str`函式，以查看 R 如何處理輸入的資料。

**結果**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- 已使用 R 資料類型處理日期時間資料行**POSIXct**。
- 「 ProductSeries"已被識別為文字資料行**因素**，這表示類別變數。 預設會作為因素處理字串值。 如果您將字串傳遞至 R 時，它會轉換成整數，供內部使用，並接著對應回輸出上的字串。

## <a name="summary"></a>總結

即使這些簡短的範例中，您可以看到不必做為輸入傳遞 SQL 查詢時，請檢查資料轉換的效果。 由於 R 不支援某些 SQL 資料類型的請考慮這些方式來避免發生錯誤：

- 事先測試您的資料，並確認資料行或您可能有問題時傳遞至 R 程式碼的結構描述中的值。
- 個別指定輸入的資料來源中的資料行，而不是使用`SELECT *`，而且知道如何處理每個資料行。
- 準備您的輸入的資料，以避免意外情況時，請執行明確的轉型為必要。
- 避免傳遞的資料行 （例如 GUID 或 rowguids 的資料） 會導致錯誤，而且不適合用於模型化。

如需有關支援與不支援的 R 資料類型的詳細資訊，請參閱 < [R 程式庫和資料類型](/sql/advanced-analytics/r/r-libraries-and-data-types)。
