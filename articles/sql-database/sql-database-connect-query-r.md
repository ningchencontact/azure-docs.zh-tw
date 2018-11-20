---
title: 在 Azure SQL Database (預覽) 中使用 Microsoft 機器學習服務 (搭配 R) 的快速入門 | Microsoft Docs
description: 本主題說明如何在 Azure SQL Database 中使用機器學習服務和如何執行 R 指令碼以提供大規模的進階分析，以及如何能在資料所在之處導入計算和處理功能，而無須透過網路提取資料。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 11/07/2018
ms.openlocfilehash: 382ac23ea4c8e0ec54314bb754c00a8e6e43e9f6
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300960"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>快速入門：在 Azure SQL Database (預覽) 中使用機器學習服務 (搭配 R)

本文說明如何在 Azure SQL Database 中使用公開預覽版的機器學習服務 (搭配 R)。 本文將引導您了解在 SQL 資料庫與 R 之間移動資料的基本概念。此外也會說明如何將格式正確的 R 程式碼包裝在預存程序 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 中，以在 SQL 資料庫中建置、定型和使用機器學習模型。

SQL Database 中的機器學習服務可用來執行 R 程式碼和函式，且程式碼完全可供關聯式資料作為預存程序、作為包含 R 陳述式的 T-SQL 指令碼，或作為包含 T-SQL 的 R 程式碼。 使用企業 R 套件的強大功能可提供大規模的進階分析，並可讓您在資料所在之處導入計算和處理功能，而無須透過網路提取資料。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="sign-up-for-the-preview"></a>註冊預覽版

依預設不會在 SQL Database 中啟用公開預覽版的機器學習服務 (搭配 R)。 請經由 [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) 將電子郵件傳送至 Microsoft，以註冊公開預覽版。

當您在計畫中註冊後，Microsoft 即會將您加入公開預覽版，並移轉您現有的資料庫，或在已啟用 R 的服務上建立新的資料庫。

SQL Database 中的機器學習服務 (搭配 R) 目前僅適用於單一和集區資料庫的**一般用途**和**商務關鍵**服務層中以虛擬核心為基礎的購買模型。 在此初始公開預覽版中，**超大規模**服務層和**受控執行個體**均不受支援。 在公開預覽期間，您不應將搭配 R 的機器學習服務用於生產工作負載。

為您的 SQL 資料庫啟用機器學習服務 (搭配 R) 後，請返回此頁面，以即可了解如何在預存程序的內容中執行 R 指令碼。

目前，R 是唯一支援的語言。 目前不支援 Python。

## <a name="prerequisites"></a>必要條件

若要在這些練習中執行範例程式碼，您必須具有已啟用機器學習服務 (搭配 R) 的 SQL 資料庫。 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務，如上所述。

您可以連線至 SQL Database，並透過任何資料庫管理或查詢工具來執行 R 指令碼，只要該工具可連線至 SQL Database 並執行 T-SQL 查詢或預存程序即可。 本快速入門將使用 [SQL Server Management Studio](sql-database-connect-query-ssms.md)。

在[新增套件](#add-package)練習中，您也必須本機電腦上安裝 [R](https://www.r-project.org/) 和 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

本快速入門也需要您設定伺服器層級的防火牆規則。 如需示範如何執行這項操作的快速入門，請參閱[建立伺服器層級的防火牆規則](sql-database-get-started-portal-firewall.md)。

## <a name="different-from-sql-server"></a>不同於 SQL Server

Azure SQL Database 中的機器學習服務 (搭配 R) 功能類似於 [SQL Server Machine Learning 服務](https://review.docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)。 但是，兩者有一些差異：

- 僅限於 R。 目前不支援 Python。
- 不需要透過 `sp_configure` 設定 `external scripts enabled`。
- 不需要為使用者授與指令碼執行權限。
- 必須透過 **sqlmlutils** 安裝套件。
- 沒有個別的外部資源控管。 R 的資源是特定百分比的 SQL 資源，視服務層而定。

## <a name="verify-r-exists"></a>確認有 R 存在

您可以確認已為 SQL 資料庫啟用機器學習服務 (搭配 R)。 請依照下列步驟進行操作。

1. 開啟 SQL Server Management Studio，然後連線至 SQL 資料庫。

1. 執行下列程式碼。 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    如果一切正常，您應該會看到如下的結果訊息。

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. 如果發生任何錯誤，有可能是因為您的 SQL 資料庫未啟用公開預覽版的機器學習服務 (搭配 R)。 請參閱前述有關於註冊公開預覽版的說明。

## <a name="basic-r-interaction"></a>基本 R 互動

有兩種方式可在 SQL Database 中執行 R 程式碼：

+ 新增 R 指令碼作為系統預存程序 [sp_execute_external_script](https://docs.microsoft.com/sql//relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的引數。
+ 從[遠端 R 用戶端](https://review.docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client)連線至 SQL 資料庫，並使用 SQL Database 作為計算內容來執行程式碼。

下列練習著重於第一個互動模型：如何將 R 程式碼傳至預存程序。

1. 執行簡單的指令碼，以查看 R 指令碼如何在您的 SQL 資料庫中執行。

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. 假設一切都已正確設定，並計算正確的結果，且 R `print` 函式將結果傳回至 [訊息] 視窗。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    在測試程式碼時取得 **stdout** 訊息，將有所幫助，但以表格式格式傳回結果通常更有其必要性，以便您可在應用程式中加以使用，或將其寫入資料表。 如需詳細資訊，請參閱以下的輸入和輸出一節。

請記住，`@script` 引數內的所有項目都必須是有效的 R 程式碼。

## <a name="inputs-and-outputs"></a>輸入和輸出

根據預設，[sp_execute_external_script](https://review.docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 接受單一輸入資料集，這通常是您以有效 SQL 查詢的形式提供的資料集。 其他類型的輸入可傳入作為 SQL 變數。

預存程序會傳回單一 R 資料框架作為輸出，但您也可以輸出純量和模型作為變數。 例如，您可以輸出定型的模型作為二進位變數，並將其傳至 T-SQL INSERT 陳述式，以將該模型寫入資料表。 您也可以產生繪圖 (以二進位格式) 或純量 (個別的值，例如日期和時間、定型模型的經過時間等)。

我們目前僅將討論 sp_execute_external_script 的預設輸入和輸出變數：`InputDataSet` 和 `OutputDataSet`。

1. 執行下列 T-SQL 陳述式，以建立測試資料的小型資料表：

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    建立資料表後，請使用下列陳述式來查詢資料表：
  
    ```sql
    SELECT * FROM RTestData
    ```

    **結果**

    ![RTestData 資料表的內容](./media/sql-database-connect-query-r/select-rtestdata.png)

2. 您可以從資料表中取得資料，作為 R 指令碼的輸入。 請執行下列陳述式。 它會從資料表中取得資料、透過 R 執行階段建立來回行程，並傳回資料行名稱 *NewColName* 的值。

    查詢所傳回的資料會傳至 R 執行階段，而後資料再從該處傳回至 SQL Database 作為資料框架。 WITH RESULT SETS 子句會定義為 SQL Database 傳回的資料表所具備的結構描述。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **結果**

    ![從資料表傳回資料的 R 指令碼輸出](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. 我們將變更輸入或輸出變數的名稱。 上述指令碼使用預設的輸入和輸出變數名稱，即 _InputDataSet_ 和 _OutputDataSet_。 若要定義與 _InputDatSet_ 相關聯的輸入資料，您應使用 *@input_data_1* 變數。

    在此指令碼中，預存程序的輸出和輸入變數名稱已變更為 *SQL_out* 和 *SQL_in*：

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    請注意，R 會區分大小寫，因此 `@input_data_1_name` 和 `@output_data_1_name` 中的輸入和輸出變數必須與 `@script` 中的 R 程式碼使用相同的大小寫。 

    此外，參數的順序也很重要。 您必須先指定必要參數 *@input_data_1* 和 *@output_data_1*，才能使用選用參數 *@input_data_1_name* 和 *@output_data_1_name*。

    只有一個輸入資料集可以傳入作為參數，而且您只能傳回一個資料集。 不過，您可以從 R 程式碼內呼叫其他資料集，而且可以在資料集以外傳回其他類型的輸出。 您也可以為任何參數加上 OUTPUT 關鍵字，使其與結果一起傳回。 

    `WITH RESULT SETS` 陳述式會定義在 SQL Database 中使用的資料結構描述。 您必須為每個從 R 傳回的資料行提供與 SQL 相容的資料類型。您也可以使用結構描述定義來提供新的資料行名稱，因為您不需要使用來自 R 資料框架的資料行名稱。

4. 您也可以使用 R 指令碼來產生值，並將 _@input_data_1_ 中的輸入查詢字串保留為空白。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![使用 @script 作為輸入的查詢結果](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>檢查 R 版本

如果您想要查看安裝在 SQL 資料庫中的 R 版本，請執行下列作業：

1. 在 SQL 資料庫上執行下列指令碼。

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. R `print` 函式會將版本傳回至 [訊息] 視窗。 在下列範例輸出中，您可以看到此案例中的 SQL 資料庫已安裝 R 3.4.4 版。

    **結果**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>列出 R 套件

Microsoft 會在提供多個隨機器學習服務預先安裝在 SQL 資料庫中的 R 套件。 若要查看已安裝的 R 套件清單 (包括版本、相依性、授權和程式庫路徑資訊)，請遵循下列步驟。 若要新增其他套件，請參閱[新增套件](#add-package)一節。

1. 在 SQL 資料庫上執行下列指令碼。

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. 輸出來自於 R 中的 `installed.packages()`，並會以結果集的形式傳回。

    **結果**

    ![R 中已安裝的套件](./media/sql-database-connect-query-r/r-installed-packages.png)


## <a name="create-a-predictive-model"></a>建立預測性模型

您可以使用 R 來定型模型，並將模型儲存至 SQL 資料庫中的資料表。 在此練習中，您將定型一個簡單的迴歸模型，以根據速度預測汽車的煞停距離。 您將使用隨附於 R 的 `cars` 資料集，因為此資料集較小且易於了解。

1. 首先，請建立用來儲存定型資料的資料表。

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    R 執行階段附有許多資料集，大小不一。 若要取得隨 R 安裝的資料集清單，請從 R 命令提示字元輸入 `library(help="datasets")`。

2. 建立迴歸模型。 汽車速度資料包含 `dist` 和 `speed` 兩個資料行，都是數值。 其中包含多個速度觀測值。 您將從這項資料建立線性迴歸模型，來說明車輛速度與煞停所需距離之間的關聯性。

    線性模型的需求很簡單：

    - 定義一個公式來說明應變數 `speed` 與自變數 `distance` 之間的關聯性。

    - 提供定型模型時所使用的輸入資料。

    > [!TIP]
    > 如果您需要複習一下線性模型，建議您使用下列教學課程，其中說明了使用 rxLinMod 來配適模型的程序：[配適線性模型](https://docs.microsoft.com/r-server/r/how-to-revoscaler-linear-model)

    若要建置模型，您必須在 R 程式碼內定義公式，並傳入資料作為輸入參數。

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
        EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
            trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @output_data_1_name = N'trained_model'
        WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

    rxLinMod 的第一個引數是將距離定義為相依於速度的*公式*參數。 輸入資料儲存在變數 `CarsData` 中，會由 SQL 查詢填入。 如果您未將特定名稱指派給輸入資料，預設變數名稱將是 _InputDataSet_。

2. 接下來，建立用來儲存模型的資料表，以便您將模型重新定型或用於預測。 建立模型的 R 套件輸出通常是**二進位物件**。 因此，資料表必須提供 **VARBINARY(max)** 類型的資料行。

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. 若要儲存模型，請執行下列 Transact-SQL 陳述式以呼叫預存程序、產生模型，並將其儲存至資料表。

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    請注意，如果再次執行此程式碼，將會出現下列錯誤：

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    若要避免發生此錯誤，選項之一是更新每個新模型的名稱。 例如，您可以將名稱變得更具描述性，並加入模型類型、模型的建立日期等。

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. 一般而言，預存程序 [sp_execute_external_script](https://review.docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的 R 輸出僅限於單一資料框架。

    但除了資料框架以外，您也可以傳回其他類型的輸出，例如純量。

    例如，假設您想要定型模型，但要立即檢視模型的係數資料表。 您可以將係數資料表建立為主要結果集，並以 SQL 變數的形式輸出定型的模型。 您可以藉由呼叫變數立即重複使用該模型，或將模型儲存至資料表，如下所示。

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **結果**

    ![有其他輸出的定型模型](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>預測

使用在上一節建立的模型對新資料進行預測評分。 若要使用新資料執行_評分_，請從資料表中取得其中一個定型模型，然後呼叫要作為預測基準的一組新資料。 評分一詞有時會在資料科學中用來表示根據送入定型模型中的新資料來產生預測、機率或其他值的動作。

1. 首先，請建立已新增速度資料的資料表。 您是否注意到，原始的定型資料僅止於每小時 25 英哩的速度？ 這是因為原始資料以 1920 年的實驗作為基礎！ 您可能會想知道，1920 年代的汽車如果能開到每小時 60 英哩甚或 100 英哩的速度，需要多久的時間才能煞停？ 若要解答此問題，您必須提供一些新的速度值。

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    在此範例中，由於您的模型以 **RevoScaleR** 套件隨附的 **rxLinMod** 演算法為基礎，因此您應呼叫 [rxPredict](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxpredict) 函式，而非泛型 R `predict` 函式。

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
                current_model <- unserialize(as.raw(speedmodel));
                new <- data.frame(NewCarData);
                predicted.distance <- rxPredict(current_model, new);
                str(predicted.distance);
                OutputDataSet <- cbind(new, ceiling(predicted.distance));
                '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    指令碼會執行下列步驟：

    + 使用 SELECT 陳述式從資料表中取得單一模型，並將其傳入作為輸入參數。

    + 從資料表中擷取模型之後，請對模型呼叫 `unserialize` 函式。

        > [!TIP] 
        > 同時請查看 RevoScaleR 提供的新[序列化函式](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxserializemodel)，這些函式支援即時評分。
    + 將 `rxPredict` 函式搭配適當的引數套用至模型，並提供新的輸入資料。

    + 在此範例中，會在測試階段加入 `str` 函式，以檢查從 R 傳回的資料結構描述。您可於稍後移除陳述式。

    + R 指令碼中使用的資料行名稱不一定要傳至預存程序輸出。 在此我們使用 WITH RESULTS 子句來定義一些新的資料行名稱。

    **結果**

    ![預測煞停距離的結果集](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

    您也可以使用 [Transact-SQL 中的 PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)，根據預存模型產生預測的值或分數。

<a name="add-package"></a>

## <a name="add-a-package"></a>新增套件

如果您需要使用尚未安裝在 SQL 資料庫中的套件，您可以使用 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 加以安裝。 請依照下列步驟安裝套件。

1. 從 [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) 將最新的 **sqlmlutils** Zip 檔案下載到您的本機電腦。 您不需要解壓縮此檔案。

1. 如果您尚未安裝 R，請從 [www.r-project.org](https://www.r-project.org/) 下載 R，並將其安裝在本機電腦上。 R 適用於 Windows、MacOS 和 Linux。 在此範例中，我們使用 Windows。

1. 首先請安裝 **RODBCext** 套件，這是 **sqlmlutils** 的必要條件。 **RODBCext** 也會安裝 **RODBC** 套件的相依性。 開啟**命令提示字元**並執行下列命令：

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    如果您收到類似於 **'R' 未辨識為內部或外部命令、可執行程式或批次檔**的錯誤，可能表示 R.exe 的路徑未包含在 Windows 上的 **PATH** 環境變數中。 您可以將目錄新增至環境變數，或在命令提示字元中瀏覽至該目錄 (例如 `cd C:\Program Files\R\R-3.5.1\bin`)。

1. 使用 **R CMD INSTALL** 命令安裝 **sqlmlutils**。 指定下載 Zip 檔案的目錄路徑，以及 Zip 檔案的名稱。 例如︰

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    您應該會看到如下的輸出：

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. 在此範例中，您將使用 RStudio Desktop 作為 IDE。 您可以使用慣用的其他 IDE。 如果您尚未安裝 RStudio，請從 [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/) 下載並安裝 RStudio Desktop。

1. 開啟 **RStudio** 並建立新的 **R 指令碼**檔案。 使用下列 R 程式碼透過 sqlmlutils 安裝套件。 在下列範例中，您會安裝 [glue](https://cran.r-project.org/web/packages/glue/) 套件，而此套件可格式化及插補字串。

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **scope** 可以是 **PUBLIC** 或 **PRIVATE**。 公用範圍可供資料庫管理員安裝所有使用者皆可使用的套件。 私人範圍會使套件僅可供套件安裝者使用。 若未指定範圍，預設範圍將是 **PRIVATE**。

1. 現在，請確認 **glue** 套件已安裝。

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **結果**

    ![RTestData 資料表的內容](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. 安裝套件之後，您可以在 R 指令碼中透過 **sp_execute_external_script** 使用該套件。 開啟 **SQL Server Management Studio**，然後連線至 SQL 資料庫。 執行下列指令碼：

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    您會在 [訊息] 索引標籤中看到下列結果。

    **結果**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. 如果您想要移除套件，請在本機電腦上透過 **RStudio** 執行下列 R 指令碼。

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> 將 R 套件安裝到 SQL 資料庫的另一個方法，是透過 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 將 R 套件上傳至位元組資料流。

## <a name="next-steps"></a>後續步驟

如需機器學習服務的詳細資訊，請參閱下列有關於 SQL Server 機器學習服務的文章。 雖然這些文章是針對 SQL Server 而撰寫的，但大部分的資訊都適用於 Azure SQL Database 中的機器學習服務 (搭配 R)。

- [SQL Server Machine Learning 服務](https://review.docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [教學課程：了解在 SQL Server 中使用 R 的資料庫內分析](https://review.docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [R 和 SQL Server 的端對端資料科學逐步解說](https://review.docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [教學課程：搭配使用 RevoScaleR R 函式與 SQL Server 資料](https://review.docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
