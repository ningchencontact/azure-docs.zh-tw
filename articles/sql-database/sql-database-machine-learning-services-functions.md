---
title: 撰寫進階的 R 函式
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 了解如何使用 Machine Learning 服務 （預覽） 的 Azure SQL Database 中撰寫適用於進階統計運算的 R 函式。
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013772"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>使用 Machine Learning 服務 （預覽） 的 Azure SQL Database 中撰寫進階的 R 函式

這篇文章說明如何內嵌 R 數學和公用程式函式中的 SQL 預存程序。 在 T-SQL 中實作複雜的進階統計函式可以在 R 中使用只有一行程式碼。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

- 若要執行這些練習的範例程式碼，您必須先啟用使用機器學習服務 （使用 R) Azure SQL database。 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務。 請遵循[註冊預覽版](sql-database-machine-learning-services-overview.md#signup)中的步驟。

- 請確定您已安裝最新[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以執行 R 指令碼使用其他資料庫管理或查詢工具，但在本快速入門中，您將使用 SSMS。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>建立預存程序來產生隨機數字

為了簡單起見，讓我們使用 R`stats`安裝並載入預設使用 Machine Learning 服務 （預覽） 的 Azure SQL Database 的封裝。 封裝包含數百個函式進行一般統計工作的詳細資訊，在它們之間`rnorm`函式。 此函式會產生隨機數字，使用常態分佈，指定標準差，表示指定的數目。

例如，下列 R 程式碼會傳回 100 的數字，平均值為 50，指定標準差為 3。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

若要從 T-SQL 呼叫此行的 R，執行`sp_execute_external_script`並新增 R 函式中的 R 指令碼參數，就像這樣：

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

如果您想要輕鬆地產生一組不同的隨機數字？

這是結合 SQL 時則容易。 您定義從使用者取得的引數，然後將這些引數當做變數傳遞至 R 指令碼的預存程序。

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 第一行會定義每個 SQL 輸入參數的預存程序執行時所需。

- 開頭的行會`@params`定義 R 程式碼和對應的 SQL 資料類型所使用的所有變數。

- 緊接的行會將 SQL 參數名稱對應至對應的 R 變數的名稱。

既然您已在預存程序中包裝 R 函數，可以輕鬆地呼叫函式，並傳入不同的值，就像這樣：

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>使用 R 公用程式函式進行疑難排解

`utils`根據預設，安裝的封裝提供了各種用於調查目前 R 環境的公用程式函式。 這些函式可以是很有用，如果您發現不一致的方式，在 SQL 中和外部環境中，您的 R 程式碼會執行。 例如，您可以使用 R`memory.limit()`函式來取得目前 R 環境的記憶體。

因為`utils`安裝套件，但不是會載入預設值，您必須使用 `library()`函式先載入它。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> 許多使用者想要在 R 中使用系統時間函式，例如`system.time`和`proc.time`，以擷取 R 處理序所使用的時間，並分析效能問題。
