---
title: 使用 Azure Data Factory 同步處理 Azure SQL Database Edge 的資料 | Microsoft Docs
description: 瞭解如何在 Azure SQL Database Edge 與 Azure Blob 儲存體之間同步處理資料
keywords: sql database edge,同步處理 sql database edge 的資料, sql database edge 資料處理站
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509202"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>教學課程：使用 Azure Data Factory 將 SQL Database Edge 中的資料同步至 Azure Blob 儲存體

在本教學課程中，您會使用 Azure Data Factory 將 Azure SQL Database Edge 其執行個體中資料表的資料，以累加方式同步至 Azure Blob 儲存體。

## <a name="before-you-begin"></a>開始之前

如果您尚未在 Azure SQL Database Edge 部署中建立資料庫或資料表，請使用下列其中一種方法建立：

* 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) 或 [Azure Data Studio](/sql/azure-data-studio/download/) 連線至 SQL Database Edge，然後執行 SQL 指令碼建立資料庫和資料表。
* 透過直接連線至 SQL Database Edge 模組，使用 [SQLCMD](/sql/tools/sqlcmd-utility/) 建立 SQL 資料庫和資料表。 如需詳細資訊，請參閱[使用 sqlcmd 連線至資料庫引擎](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)。
* 使用 SQLPackage.exe 將 dacpac 檔案部署至 SQL Database Edge 容器。 可透過將 SQLPackage 檔案 URI 指定為模組所需屬性設定之一，或直接使用 SqlPackage.exe 用戶端工具將 dacpac 部署至 SQL Database Edge，來自動進行此作業。

    若要下載 sqlpackage，請參閱[下載並安裝 sqlpackage](/sql/tools/sqlpackage-download/)。 其中提供 SqlPackage.exe 的下列範例命令，但請查看 sqlpackage 文件以取得詳細資訊。

    **建立 dacpac**：

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **套用 dacpac**：

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>建立 SQL 資料表及用於儲存和更新浮水印層級的程序

浮水印資料表用於儲存最後一個時間戳記，亦即資料已與 Azure 儲存體同步的截止時間。 Transact-SQL (T-SQL) 儲存的程序用於在每次同步後更新浮水印資料表。 

在 SQL Database Edge 執行個體上執行下列命令：

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>建立 Data Factory 工作流程

在本節中，您會建立 Azure Data Factory 管線將 Azure SQL Database Edge 內資料表中的資料同步至 Azure Blob 儲存體。

### <a name="create-data-factory-using-the-data-factory-ui"></a>使用 Data Factory 使用者介面建立 Data Factory

使用此[教學課程](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)中的指示建立 Data Factory。

### <a name="create-a-data-factory-pipeline"></a>建立 Data Factory 管線

1. 在 Data Factory 使用者介面的 [開始使用]  頁面上，選取 [建立管線]  圖格。

    ![Data Factory - 建立管線](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. 在管線 [屬性]  視窗的 [一般]  頁面上，輸入 **PeriodicSync** 名稱。

3. 新增 [查閱]  活動以取得舊浮水印值。 在 [活動]  工具箱中展開 [一般]  ，並將 [查閱]  活動拖放至管線設計工具介面。 將活動名稱變更為 *OldWatermark*。

    ![舊浮水印查閱](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. 切換至 [設定]  索引標籤，然後選取 [+ 新增]  以新增**來源資料集**。 在此步驟中，您會建立資料集來代表浮水印資料表中的資料。 此資料表包含先前複製作業中所使用的舊浮水印。

5. 在 [新增資料集]  視窗中選取 [Azure SQL Server]  ，然後選取 [繼續]  。  

6. 在該資料集的 [設定屬性]  視窗中，輸入 *WatermarkDataset* 作為 [名稱]。

7. 在 [已連結的服務]  視窗中，選取 [新增]  ，然後執行下列步驟：

    1. 輸入 *SQLDBEdgeLinkedService* 作為 [名稱]  。

    2. 輸入您的 SQL Database Edge 伺服器詳細資料作為 [伺服器名稱]  。

    3. 從下拉式清單中輸入您的 [資料庫名稱]  。

    4. 輸入您的 [使用者名稱]  和 [密碼]  。

    5. 若要測試與 SQL Database Edge 執行個體的連線，請選取 [測試連線]  。

    6. 選取 [建立]  。

    ![建立已連結的服務](media/tutorial-sync-data-factory/create-linked-service.png)

    7. 選取 [確定] 

8. 在 [設定]  索引標籤中，選取 [編輯]  。

9. 在 [連線]  索引標籤中，為 [資料表]  選取 [[dbo].[watermarktable]]  。 如果您想要預覽資料表中的資料，請選取 [預覽資料]  。

10. 選取頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。 在 [查閱]  活動的 [屬性] 視窗中，確認已為 [來源資料集]  欄位選取 **WatermarkDataset**。

11. 在 [活動]  工具箱中展開 [一般]  ，並將另一個 [查閱]  活動拖放至管線設計工具介面，然後在 [屬性] 視窗的 [一般]  索引標籤中，將名稱設為 **NewWatermark**。 此查閱活動會從資料表取得新浮水印值，該資料表具備要複製到目的地的來源資料。

12. 在第二個 [查閱]  活動的屬性視窗中，切換至 [設定]  索引標籤，然後選取 [新增]  建立一個資料集以指向包含新浮水印值的來源資料表。

13. 在 [新增資料集]  視窗中，選取 SQL Database Edge 執行個體，然後選取 [繼續]  。

    1. 在 [設定屬性]  視窗中，輸入 [SourceDataset]  作為 [名稱]  。 選取 [SQLDBEdgeLinkedService]  作為 [已連結的服務]。

    2. 為 [資料表] 選取***您要同步化的資料表***。 您也可以指定此資料集的查詢，如本教學課程稍後所述。 查詢會優先於您在此步驟中指定的資料表。

    3. 選取 [確定]  。

14. 選取頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器。 在 [查閱]  活動的 [屬性] 視窗中，確認已為 [來源資料集]  欄位選取 [SourceDataset]  。

15. 為 [使用查詢]  欄位選取 [查詢]  ，然後在更新查詢中的資料表名稱後輸入下列查詢：您從資料表中僅選取時間戳記的最大值。 請確定您也已勾選 [僅限第一列]  。

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![選取查詢](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. 在 [活動]  工具箱中，展開 [移動和轉換]  ，然後從 [活動] 工具箱中拖放 [複製]  活動，並將名稱設定為 **IncrementalCopy**。

17. 透過將 [查閱]  活動所附加的**綠色按鈕**拖曳至 [複製]  活動，即可將兩個 [查閱]  活動同時連線至 [複製]  活動。 當您看到 [複製] 活動的框線顏色變為藍色時，即鬆開滑鼠按鍵。

18. 選取 [複製]  活動並確認您在 [屬性]  視窗中看到活動的屬性。

19. 在 [屬性]  視窗中切換至 [來源]  索引標籤，並執行下列步驟：

    1. 為 [來源資料集]  欄位選取 [SourceDataset]  。

    2. 為 [使用查詢]  欄位選取 [查詢]  。

    3. 為 [查詢]  欄位輸入 SQL 查詢。 下方的範例查詢

    4. SQL 查詢：

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. 切換至 [接收]  索引標籤，然後選取 [接收資料集]  欄位的 [+ 新增]  。

21. 在本教學課程中，接收資料存放區的類型是 [Azure Blob 儲存體]  。 選取 [Azure Blob 儲存體]  ，然後選取 [新增資料集]  視窗中的 [繼續]  。

22. 在 [選取格式]  視窗中，選取您資料的格式類型，然後選取 [繼續]  。

23. 在 [設定屬性]  視窗中，輸入 **SinkDataset** 作為 [名稱]。 為 [已連結的服務] 選取 [+ 新增]  。 在此步驟中，您將建立與 **Azure Blob 儲存體**的連線 (連結的服務)。

24. 在 [新增已連結的服務 (Azure Blob 儲存體)]  視窗中，執行下列步驟：

    1. 輸入 *AzureStorageLinkedService* 作為 [名稱]。

    2. 針對含您 Azure 訂用帳戶的 [儲存體帳戶名稱]  選取您的 Azure 儲存體帳戶。

    3. 測試 [連線]  ，然後選取 [完成]  。

25. 在 [設定屬性]  視窗中，確認已為 [已連結的服務]  選取 [AzureStorageLinkedService]  。 然後選取 [建立]  ，再選取 [確定]  。

26. 在 [接收]  索引標籤中，選取 [編輯]  。

27. 移至 [SinkDataset]  的 [連線]  索引標籤，然後執行下列步驟：

    1. 對於 [檔案路徑]  欄位輸入 *asdedatasync/incrementalcopy*，其中 **adftutorial** 是 blob 容器名稱，**incrementalcopy** 是資料夾名稱。 建立容器 (若不存在)，或設為現有容器的名稱。 如果輸出資料夾 *incrementalcopy* 不存在，Azure Data Factory 將會自動建立。 您也可以對**檔案路徑**使用 [瀏覽]  按鈕來瀏覽至 blob 容器中的資料夾。

    2. 為 [檔案路徑]  欄位的 [檔案]  部分選取 [新增動態內容 [Alt+P]]  ，然後在開啟的視窗中*輸入 @CONCAT('Incremental-', pipeline().RunId, '.txt')* 。 然後選取 [完成]  。 系統會使用運算式來動態產生此檔案名稱。 每個管線執行都有唯一的識別碼。 複製活動會使用執行識別碼來產生檔案名稱。

28. 選取頂端的 [管線] 索引標籤或左側樹狀檢視中的管線名稱，即可切換到管線編輯器  。

29. 在 [活動]  工具箱中展開 [一般]  ，並將 [預存程序]  活動從 [活動]  工具箱拖放至管線設計工具介面。 將 [複製]  活動的綠色 (成功) 輸出**連線**至 [預存程序]  活動。

30. 選取管線設計工具中的 [預存程序活動]  ，將其名稱變更為 *SPtoUpdateWatermarkActivity*。

31. 切換至 [SQL 帳戶]  索引標籤，然後選取 [SQLDBEdgeLinkedService]  作為 [已連結的服務]  。

32. 切換至 [預存程序]  索引標籤，然後執行下列步驟：

    1. 針對 [預存程序名稱]  ，選取  。

    2. 若要指定預存程序參數的值，請選取 [匯入參數]，然後輸入參數的下列值：

    |名稱|類型|值|
    |-----|----|-----|
    |LastModifiedtime|Datetime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|字串|@{activity('OldWaterMark').output.firstRow.TableName}|

33. 若要驗證管線設定，請選取工具列上的 [驗證]  。 確認沒有任何驗證錯誤。 若要關閉 [管線驗證報告]  視窗，請選取 **>>** 。

34. 選取 [全部發佈]  按鈕，將實體 (連結的服務、資料集和管線) 發佈至 Azure Data Factory 服務。 請等候直至您看見成功發佈的訊息。

## <a name="trigger-a-pipeline-on-schedule"></a>依排程觸發管線

1. 選取管線工具列上的 [新增觸發程序]  ，然後選取 [新增/編輯]  ，再選取 [+ 新增]  。

2. 將您的觸發程序命名為 *HourlySync*，選擇 [類型]  作為 [排程]，然後將 [週期]  設定為每隔 1 小時。

3. 選取 [確定]  。

4. 選取 [全部發佈]  。

5. 選取 [立即觸發]  。

6. 切換至左側的 [監視]  索引標籤。 您可以看到手動觸發程序所觸發的管線執行狀態。 選取 [重新整理]  按鈕重新整理清單。

## <a name="next-steps"></a>後續步驟

本教學課程中的 Azure Data Factory 管線會以每小時一次的頻率，將 SQL Database Edge 執行個體上資料表的資料複製到 Azure Blob 儲存體中的某個位置。 若想了解使用 Data Factory 的更多案例，請瀏覽這些[教學課程](../data-factory/tutorial-copy-data-portal.md)。
