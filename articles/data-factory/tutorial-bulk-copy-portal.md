---
title: 使用 Azure Data Factory 大量複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 和複製活動，將資料從來源資料存放區大量複製到目的地資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 440b07b494b34db7ff3fcdf5d5ac830b165c339d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173278"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>使用 Azure Data Factory 大量複製多個資料表
本教學課程示範**從 Azure SQL Database 複製一些資料表到 Azure SQL 資料倉儲**。 您也可以在其他複製案例中套用相同模式。 例如，將資料表從 SQL Server/Oracle 複製到 Azure SQL Database/資料倉儲/Azure Blob，將不同的路徑從 Blob 複製到 Azure SQL Database 資料表。

> [!NOTE]
> - 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。
> - 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

概括而言，本教學課程包含下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure SQL Database、Azure SQL 資料倉儲和 Azure 儲存體連結服務。
> * 建立 Azure SQL Database 和 Azure SQL Data Warehouse 資料集。
> * 建立管線來查詢要複製的資料表和其他管線，以執行實際的複製作業。 
> * 啟動管線執行。
> * 監視管線和活動執行。

本教學課程使用 Azure 入口網站。 若要了解如何使用其他工具/SDK 來建立資料處理站，請參閱[快速入門](quickstart-create-data-factory-dot-net.md)。 

## <a name="end-to-end-workflow"></a>端對端工作流程
在此案例中，您在 Azure SQL Database 中有一些想要複製到 SQL 資料倉儲的資料表。 以下是發生在管線中工作流程中步驟的邏輯順序：

![工作流程](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* 第一個管線會查閱需要複製到接收資料存放區的資料表清單。  或者，您可以維護中繼資料資料表，其中列出要複製到接收資料存放區的所有資料表。 然後，管線會觸發另一個管線，它會逐一查看資料庫中的每個資料表，並執行資料複製作業。
* 第二個管線會執行實際的複製。 它會使用資料表的清單作為參數。 對於清單中的每個資料表，使用[透過 Blob 儲存體和 PolyBase 暫存複製](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)將 Azure SQL Database 中的特定資料表複製到 SQL 資料倉儲中的對應資料表，可獲得最佳效能。 在此範例中，第一個管線會將資料表清單傳遞作為參數的值。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>先決條件
* **Azure 儲存體帳戶**。 Azure 儲存體帳戶會在大量複製作業中用做暫存 Blob 儲存體。 
* **Azure SQL Database**。 此資料庫包含來源資料。 
* **Azure SQL 資料倉儲**。 此資料倉儲保存從 SQL Database 複製的資料。 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>準備 SQL Database 和 SQL 資料倉儲

**準備來源 Azure SQL Database**：

遵循[建立 Azure SQL Database](../sql-database/sql-database-get-started-portal.md) 文章來建立具有 Adventure Works LT 範例資料的 Azure SQL Database。 本教學課程會將所有資料表從這個範例資料庫中複製到 SQL 資料倉儲。

**準備接收 Azure SQL 資料倉儲**：

1. 如果您沒有 Azure SQL 資料倉儲，請參閱[建立 SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)文章，以取得建立的步驟。

2. 在 SQL 資料倉儲中建立對應的資料表結構描述。 您可以使用[移轉公用程式](https://www.microsoft.com/download/details.aspx?id=49100)將**結構描述**從 Azure SQL Database 移轉到 Azure SQL 資料倉儲。 在稍後步驟中，您可以使用 Azure Data Factory 來移轉/複製資料。

## <a name="azure-services-to-access-sql-server"></a>Azure 服務存取 SQL Server

針對 SQL Database 和 SQL 資料倉儲，允許 Azure 服務存取 SQL 伺服器。 確保 Azure SQL Server 的 [允許存取 Azure 服務] 設定已 [開啟]。 此設定可允許 Data Factory 服務從您的 Azure SQL Database 讀取資料，並將資料寫入至 Azure SQL 資料倉儲。 若要確認並開啟此設定，請執行下列步驟：

1. 按一下左邊的 [更多服務] 中樞，然後按一下 [SQL Server]。
2. 選取您的伺服器，然後按一下 [設定] 下的 [防火牆]。
3. 在 [防火牆設定] 頁面中，對 [允許存取 Azure 服務] 按一下 [開啟]。

## <a name="create-a-data-factory"></a>建立 Data Factory
1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
1. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，輸入 **ADFTutorialBulkCopyDF** 作為 [名稱]。 
      
     ![新增資料處理站頁面](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您在名稱欄位看到下列錯誤，請變更資料處理站的名稱 (例如 yournameADFTutorialBulkCopyDF)。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，請執行下列其中一個步驟︰
     
      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。 
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
      若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 對 [版本] 選取 [V2 (預覽)]。
5. 選取 Data Factory 的 [位置]  。 目前，Data Factory V2 只允許您在美國東部、美國東部 2 和西歐區域中建立資料處理站。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。
6. 選取 [釘選到儀表板]。     
7. 按一下頁面底部的 [新增] 。
8. 在儀表板上，您會看到狀態如下的下列圖格︰**正在部署資料處理站**。 

    ![部署資料處理站圖格](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。
   
    ![Data Factory 首頁](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. 按一下 [編寫與監視] 圖格，以在另一個索引標籤中啟動 Data Factory 使用者介面應用程式。
11. 在 [開始使用] 頁面中，切換至左面板中的 [編輯] 索引標籤，如下圖所示：  

    ![開始使用頁面](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>建立連結的服務
您建立的連結服務會將您的資料存放區和計算連結到資料處理站。 連結服務具有連線資訊，可供 Data Factory 服務在執行階段中用來連線至資料存放區。 

在本教學課程中，您會將 Azure SQL Database、Azure SQL 資料倉儲、Azure Blob 儲存體資料存放區連結至您的資料處理站。 Azure SQL Database 是來源資料存放區。 Azure SQL 資料倉儲是接收/目的地資料存放區。 而在使用 PolyBase 將資料載入 SQL 資料倉儲之前，則是用 Azure Blob 儲存體暫存資料。 

### <a name="create-the-source-azure-sql-database-linked-service"></a>建立來源 Azure SQL Database 連結服務
在此步驟中，您會建立連結服務來將 Azure SQL 資料庫連結到資料處理站。 

1. 按一下視窗底部的 [連線]，然後按一下工具列上的 [+ 新增]。 

    ![新增連結服務按鈕](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. 在 [新增連結服務] 視窗中，選取 [Azure SQL Database]，然後按一下 [繼續]。 

    ![選取 Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. 在 [新增連結服務] 視窗中，執行下列步驟： 

    1. 輸入 **AzureSqlDatabaseLinkedService** 作為 [名稱]。 
    2. 在 [伺服器名稱] 選取您的 Azure SQL 伺服器。
    3. 在 [資料庫名稱] 選取您的 Azure SQL 資料庫。 
    4. 輸入 [使用者名稱] 以連線到 Azure SQL 資料庫。 
    5. 輸入使用者的 [密碼]。 
    6. 若要使用指定資訊測試與 Azure SQL 資料庫的連線，按一下 [測試連線]。
    7. 按一下 [檔案] 。

        ![Azure SQL Database 設定](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>建立接收 Azure SQL 資料倉儲連結服務

1. 在 [連線] 索引標籤上，再次按一下工具列上的 [+ 新增]。 
2. 在 [新增連結服務] 視窗中，選取 [Azure SQL 資料倉儲]，然後按一下 [繼續]。 
3. 在 [新增連結服務] 視窗中，執行下列步驟： 

    1. 輸入 **AzureSqlDWLinkedService** 作為 **[名稱]**。 
    2. 在 [伺服器名稱] 選取您的 Azure SQL 伺服器。
    3. 在 [資料庫名稱] 選取您的 Azure SQL 資料庫。 
    4. 輸入 [使用者名稱] 以連線到 Azure SQL 資料庫。 
    5. 輸入使用者的 [密碼]。 
    6. 若要使用指定資訊測試與 Azure SQL 資料庫的連線，按一下 [測試連線]。
    7. 按一下 [檔案] 。

### <a name="create-the-staging-azure-storage-linked-service"></a>建立暫存 Azure 儲存體連結服務
在本教學課程中，您會使用 Azure Blob 儲存體作為暫時的暫存區域，讓 PolyBase 獲得更好的複製效能。

1. 在 [連線] 索引標籤上，再次按一下工具列上的 [+ 新增]。 
2. 在 [新增連結服務] 視窗中，選取 [Azure Blob 儲存體]，然後按一下 [繼續]。 
3. 在 [新增連結服務] 視窗中，執行下列步驟： 

    1. 輸入 **AzureStorageLinkedService** 作為 [名稱]。 
    2. 在 [儲存體帳戶名稱] 選取您的 **Azure 儲存體帳戶**。
    4. 按一下 [檔案] 。


## <a name="create-datasets"></a>建立資料集
在本教學課程中，您會建立來源和接收資料集，其指定儲存資料的位置。 

輸入資料集 AzureSqlDatabaseDataset 會參照 AzureSqlDatabaseLinkedService。 連結服務會指定連接字串以連線至資料庫。 資料集會指定資料庫的名稱，以及包含來源資料的資料表。 

輸出資料集 AzureSqlDWDataset 會參照 AzureSqlDWLinkedService。 連結服務會指定連接字串以連線至資料倉儲。 資料集會指定資料庫，以及作為資料複製目的地的資料表。 

在本教學課程中，並沒有在資料集定義中對來源和目的地 SQL 資料表執行硬式編碼。 而是在執行階段由 ForEach 活動將資料表名稱傳遞給複製活動。 

### <a name="create-a-dataset-for-source-sql-database"></a>建立來源 SQL Database 的資料集

1. 按一下左窗格中的 [+] (加號)，然後按一下 [資料集]。 

    ![新增資料集功能表](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. 在 [新增資料集] 視窗中選取 [Azure SQL Database]，然後按一下 [完成]。 您應該會看到標題為 **AzureSqlTable1** 的新索引標籤。 
    
    ![選取 Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. 在屬性視窗底部，輸入 **AzureSqlDatabaseDataset** 作為 [名稱]。

    ![來源資料集名稱](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. 切換至 [連線] 索引標籤，執行下列步驟： 

    1. 選取 [AzureSqlDatabaseLinkedService] 作為 [連結服務]。
    2. 選取任何資料表作為 [資料表]。 此資料表是空的資料表。 建立管線時，您可以指定來源資料集的查詢。 查詢是用來從 Azure SQL 資料庫擷取資料。 或者，您可以按一下 [編輯] 核取方塊，然後輸入 **dummyName** 作為資料表名稱。 

    ![來源資料集連線頁面](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>建立接收 SQL 資料倉儲的資料集

1. 按一下左窗格中的 [+] (加號)，然後按一下 [資料集]。 
2. 在 [新增資料集] 視窗中，選取 [Azure SQL 資料倉儲]，然後按一下 [完成]。 您應該會看到標題為 **AzureSqlDWTable1** 的新索引標籤。 
3. 在屬性視窗底部，輸入 **AzureSqlDWDataset** 作為 [名稱]。
4. 切換至 [連線] 索引標籤，然後選取 [AzureSqlDatabaseLinkedService] 作為 [連結服務]。
5. 切換至 [參數] 索引標籤，然後按一下 [+ 新增]。

    ![來源資料集連線頁面](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. 輸入 **DWTableName** 作為參數名稱。 如果您從頁面複製/貼上此名稱，請確定 **DWTableName** 的結尾處沒有**尾端空白字元**。 
7. 在 [參數化屬性] 區段中，為 **tableName** 屬性輸入 `@{dataset().DWTableName}`。 為資料集的 **tableName** 屬性設定的這個值，會作為引數傳遞給 **DWTableName** 參數。 ForEach 活動會逐一查看資料表清單，並逐一傳遞給複製活動。 
   
    ![參數名稱](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>建立管線
在此教學課程中，您會建立兩個管線：**IterateAndCopySQLTables** 和 **GetTableListAndTriggerCopyData**。 

**GetTableListAndTriggerCopyData** 管線會執行兩個步驟：

* 請查閱 Azure SQL Database 系統資料表，以取得要複製的資料表清單。
* 觸發 **IterateAndCopySQLTables** 管線以進行實際的資料複製。

**GetTableListAndTriggerCopyData** 會使用資料表的清單作為參數。 對於清單中每的個資料表，它會使用暫存的複製和 PolyBase，將來自 Azure SQL Database 資料表中的資料複製到 Azure SQL 資料倉儲。

### <a name="create-the-pipeline-iterateandcopysqltables"></a>建立 IterateAndCopySQLTables 管線

1. 按一下左窗格中的 [+] (加號)，然後按一下 [管線]。

    ![新增管線功能表](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. 在 [屬性] 視窗中，將管線的名稱變更為 **IterateAndCopySQLTables**。 

    ![管線名稱](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. 切換至 [參數] 索引標籤，執行下列動作： 

    1. 按一下 [+ 新增]。 
    2. 輸入 **tableList** 作為參數的 [名稱]。
    3. 選取 [物件] 作為 [類型]。

        ![管線參數](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. 在 [活動] 工具箱中，展開 [反覆項目與條件]，並將 **ForEach** 活動拖放至管線設計工具介面。 您也可以在 [活動] 工具箱中搜尋活動。 在 [屬性] 視窗底部，輸入 **IterateSQLTables** 作為 [名稱]。 

    ![ForEach 活動名稱](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. 切換到 [設定] 索引標籤，然後為 [項目] 輸入 `@pipeline().parameters.tableList`。

    ![ForEach 活動設定](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. 若要為 **ForEach** 活動新增子活動，**按兩下** ForEach 活動，或是按一下[編輯] (鉛筆圖示)。 只有當您選取活動時，才會看到活動的動作連結。 

    ![ForEach 活動名稱](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. 在 [活動] 工具箱中，展開 [資料流程]，將 [複製] 活動拖放至管線設計工具介面，並將 [屬性]視窗中的名稱變更為 **CopyData**。 請注意頂端的階層連結功能表。 IterateAndCopySQLTable 是管線名稱，IterateSQLTables 是 ForEach 活動名稱。 設計工具是在活動範圍內。 若要從 ForEach 編輯器切換回「管線」編輯器，按一下階層連結功能表中的連結。 

    ![ForEach 中的複製](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. 切換至 [來源] 索引標籤，執行下列步驟：

    1. 選取 [AzureSqlDatabaseDataset] 作為 [來源資料集]。 
    2. 為 [使用者查詢] 選取 [查詢] 選項。 
    3. 輸入下列 SQL 查詢作為 [查詢]。

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![複製來源設定](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. 切換至 [接收] 索引標籤，執行下列步驟： 

    1. 選取 [AzureSqlDWDataset] 作為 [接收資料集]。
    2. 展開 [Polybase 設定]，然後選取 [允許 Polybase]。 
    3. 清除 [使用類型預設值] 選項。 
    4. 輸入下列 SQL 指令碼作為 [清除指令碼]： 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![複製接收設定](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. 切換至 [參數] 索引標籤，查看 [接收資料集] 區段中的 **DWTableName** 參數 (如有需要向下捲動)。 將此參數的值設為 `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`。

    ![複製接收參數](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. 切換至 [設定] 索引標籤，執行下列步驟： 

    1. 為 [啟用暫存] 選取 [True]。
    2. 選取 [AzureStorageLinkedService] 作為 [存放區帳戶連結服務]。

        ![啟用暫存](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. 若要驗證管線設定，按一下 [驗證]。 確認沒有任何驗證錯誤。 若要關閉 [管線驗證報告]，按一下 **>>**。

    ![管線驗證報告](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>建立 GetTableListAndTriggerCopyData 管線

這個管線會執行兩個步驟：

* 請查閱 Azure SQL Database 系統資料表，以取得要複製的資料表清單。
* 觸發管線 "IterateAndCopySQLTables" 以進行實際的資料複製。

1. 按一下左窗格中的 [+] (加號)，然後按一下 [管線]。

    ![新增管線功能表](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. 在 [屬性] 視窗中，將管線的名稱變更為 **GetTableListAndTriggerCopyData**。 

    ![管線名稱](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. 在 [活動] 工具箱中展開 [一般]，並將 [查閱] 活動拖放至管線設計工具介面，然後執行下列步驟：

    1. 輸入 [LookupTableList] 作為 [名稱]。 
    2. 在 [描述] 中輸入**從 Azure SQL 資料庫擷取資料表清單**。

        ![查閱活動 - 一般頁面](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. 切換至 [設定] 頁面，執行下列步驟：

    1. 選取 [AzureSqlDatabaseDataset] 作為 [來源資料集]。 
    2. 為 [使用查詢] 選取 [查詢]。 
    3. 輸入下列 SQL 查詢作為 [查詢]。

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. 清除 [僅第一個資料列] 欄位的核取方塊。

        ![查閱活動 - 設定頁面](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. 將 [執行管線] 活動從 [活動] 工具箱拖放至管線設計工具介面，並將名稱設定為 **TriggerCopy**。

    ![執行管線活動 - 一般頁面](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. 切換至 [設定] 頁面，執行下列步驟： 

    1. 選取 [IterateAndCopySQLTables] 作為 [叫用的管線]。 
    2. 展開 [進階] 區段。 
    3. 按一下 [參數] 區段中的 [+ 新增]。 
    4. 輸入 **tableList** 作為參數的 [名稱]。
    5. 輸入 `@activity('LookupTableList').output.value` 作為參數的 [值]。 您正在將查閱活動的結果清單設定為第二個管線的輸入。 結果清單包含資料表清單，這些資料表中的資料必須複製到目的地。 

        ![執行管線活動 - 設定頁面](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. 將**查閱**活動**連線**至**執行管線**活動，做法是將連結查閱活動的**綠色方塊**拖曳至「執行管線」活動的左邊。

    ![將查閱和執行管線活動連線](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. 若要驗證管線，按一下工具列上的 [驗證]。 確認沒有任何驗證錯誤。 若要關閉 [管線驗證報告]，按一下 **>>**。

    ![第二個管線 - 驗證報告](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. 若要將實體 (資料集、管線等等) 發佈至 Data Factory 服務，請按一下 [全部發佈]。 等待發佈成功。 

    ![發佈按鈕](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>觸發管線執行

1. 確認 **GetTableListAndTriggerCopyData** 索引標籤為作用中狀態。 
2. 按一下 [觸發]，然後按一下 [立即觸發]。 

    ![立即觸發](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>監視管道執行

1. 切換至 [監視] 索引標籤。按一下 [重新整理] 直到您看到這兩個管線在您的解決方案中執行。 繼續重新整理清單，直到您看到 [成功] 狀態。 

    ![管線執行](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. 若要檢視與 GetTableListAndTriggerCopyData 管線相關聯的活動執行，按一下該管線 [動作] 連結中的第一個連結。 您應該會看到這個管線執行有兩個活動執行。 

    ![活動執行](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. 若要檢視 [查閱] 活動的輸出，按一下該活動 [輸出] 資料行中的連結。 您可以將 [輸出] 視窗最大化及還原。 檢閱之後，按一下 [X] 可關閉 [輸出] 視窗。

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
4. 若要切換回 [管線執行] 檢視，按一下頂端的 [管線] 連結。 按一下 **IterateAndCopySQLTables** 管線的 [檢視活動執行] 連結 ([動作] 資料行中的第一個連結)。 您應該會看到如下圖的輸出：請注意在**查閱**活動輸出中，每個資料表都有一個**複製**活動執行。 

    ![活動執行](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. 確認資料已複製到您在本教學課程中使用的目標 SQL 資料倉儲。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已執行下列步驟： 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure SQL Database、Azure SQL 資料倉儲和 Azure 儲存體連結服務。
> * 建立 Azure SQL Database 和 Azure SQL Data Warehouse 資料集。
> * 建立管線來查詢要複製的資料表和其他管線，以執行實際的複製作業。 
> * 啟動管線執行。
> * 監視管線和活動執行。

進入下列教學課程，以了解如何將資料累加從來源複製到目的地：
> [!div class="nextstepaction"]
>[以累加方式複製資料](tutorial-incremental-copy-portal.md)