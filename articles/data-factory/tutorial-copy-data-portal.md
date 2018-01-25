---
title: "使用 Azure 入口網站建立 Data Factory 管線 | Microsoft Docs"
description: "本教學課程提供逐步指示，說明如何使用 Azure 入口網站建立具有管線的資料處理站。 管線會使用複製活動將資料從 Azure Blob 儲存體複製到 Azure SQL 資料庫。 "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 424a5ec49018e969edbf90c374a9da7e1d22395d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從 Azure Blob 複製到 Azure SQL Database
在本教學課程中，您會使用 Azure Data Factory 使用者介面 (UI) 建立資料處理站。 此資料處理站中的管線會將資料從 Azure Blob 儲存體複製到 Azure SQL Database。 本教學課程中的設定模式從以檔案為基礎的資料存放區複製到關聯式資料存放區。 如需支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

> [!NOTE]
> - 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。
>
> - 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立具有複製活動的管線。
> * 對管線執行測試
> * 手動觸發管線
> * 觸發排程上的管線
> * 監視管線和活動執行。

## <a name="prerequisites"></a>先決條件
* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。
* **Azure 儲存體帳戶**。 您會使用 Blob 儲存體作為**來源**資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文，按照步驟來建立帳戶。
* **Azure SQL Database**。 您會使用資料庫作為**接收**資料存放區。 如果您沒有 Azure SQL Database，請參閱[建立 Azure SQL 資料庫](../sql-database/sql-database-get-started-portal.md)一文，按照步驟建立資料庫。

### <a name="create-a-blob-and-a-sql-table"></a>建立 Blob 和 SQL 資料表

現在，請執行下列步驟，以準備本教學課程的 Azure Blob 和 Azure SQL Database：

#### <a name="create-a-source-blob"></a>建立來源 Blob

1. 啟動 [記事本]。 複製下列文字，並在磁碟上將其儲存為 **emp.txt** 檔案。

    ```
    John,Doe
    Jane,Doe
    ```

2. 在 Azure Blob 儲存體中，建立名為 **adftutorial** 的容器。 在此容器中建立名為 **input** 的資料夾。 然後，將 **emp.txt** 檔案上傳至 **input** 資料夾。 請使用 Azure 入口網站或 [Azure 儲存體總管](http://storageexplorer.com/)之類的工具執行這些工作。

#### <a name="create-a-sink-sql-table"></a>建立接收 SQL 資料表

1. 使用下列 SQL 指令碼，在您的 Azure SQL Database 中建立 **dbo.emp** 資料表。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. 允許 Azure 服務存取 SQL 伺服器。 確定**開啟** Azure SQL 伺服器的 [允許存取 Azure 服務] 設定，讓 Data Factory 服務可以將資料寫入您的 Azure SQL 伺服器。 若要確認並開啟此設定，請執行下列步驟：

    1. 按一下左邊的 [更多服務] 中樞，然後按一下 [SQL Server]。
    2. 選取您的伺服器，然後按一下 [設定] 下的 [防火牆]。
    3. 在 [防火牆設定] 頁面中，對 [允許存取 Azure 服務] 按一下 [開啟]。

## <a name="create-a-data-factory"></a>建立 Data Factory
在此步驟中，您可以建立資料處理站，並啟動 Azure Data Factory 使用者介面，在資料處理站中建立管線。 

1. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
     ![新增資料處理站頁面](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您在 [名稱] 欄位看到下列錯誤，請變更資料處理站的名稱 (例如 yournameADFTutorialBulkCopyDF)。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
     ![新增資料處理站頁面](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，請執行下列其中一個步驟︰
     
      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。 
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
        若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 對 [版本] 選取 [V2 (預覽)]。
5. 選取 Data Factory 的 [位置]  。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。
6. 選取 [釘選到儀表板]。     
7. 按一下頁面底部的 [新增] 。      
8. 在儀表板上，您會看到狀態如下的下列圖格︰**正在部署資料處理站**。 

    ![部署資料處理站圖格](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。
   
   ![Data Factory 首頁](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. 按一下 [撰寫與監視] 圖格，以在另一個索引標籤中啟動 Azure Data Factory 使用者介面 (UI)。

## <a name="create-a-pipeline"></a>建立管線
在此步驟中，您會在資料處理站中建立具有「複製」活動的管線。 「複製」活動會將資料從 Azure Blob 儲存體複製到 Azure SQL Database。 在[快速入門教學課程](quickstart-create-data-factory-portal.md)中，您已依照下列步驟建立管線：

1. 建立連結服務。 
2. 建立輸入和輸出資料集。
3. 然後，建立管線。

在本教學課程中，您會從建立管線開始，然後建立連結服務和資料集來設定管線 (如有需要)。 

1. 在 [開始使用] 頁面中，按一下 [建立管線] 圖格。 

   ![建立管線圖格](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. 在管線的 [屬性] 視窗中，將管線的 [名稱] 設為 **CopyPipeline**。

    ![管線名稱](./media/tutorial-copy-data-portal/pipeline-name.png)
4. 在 [活動] 工具箱中展開 [資料流程] 類別，並將 [複製] 活動從工具箱拖放至管線設計工具介面。 

    ![拖放複製活動](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. 在 [屬性] 視窗的 [一般] 索引標籤中，將 **CopyFromBlobToSql** 指定為活動的名稱。

    ![活動名稱](./media/tutorial-copy-data-portal/activity-name.png)
6. 切換至 [來源] 索引標籤。按一下 [+ 新增] 以建立來源資料集。 

    ![新增來源資料集功能表](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. 在 [新增資料集] 視窗中選取 [Azure Blob 儲存體]，然後按一下 [完成]。 來源資料是 Azure Blob 儲存體，因此您可以選取 Azure Blob 儲存體作為來源資料集。 

    ![選取 Azure Blob 儲存體](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. 您會看到應用程式中開啟了新的**索引標籤**，標題為 **AzureBlob1**。

    ![Azure Blob1 索引標籤 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. 在 [屬性] 視窗底部的 [一般] 索引標籤中，將 **SourceBlobDataset** 指定為 [名稱]。

    ![資料集名稱](./media/tutorial-copy-data-portal/dataset-name.png)
10. 在 [屬性] 視窗中切換至 [連線] 索引標籤。   

    ![連線索引標籤](./media/tutorial-copy-data-portal/source-dataset-connection-tab.png)
11. 按一下 [連結服務] 文字方塊旁的 [+ 新增]。 連結服務會將資料存放區或計算連結至資料處理站。 在此案例中，您會建立 Azure 儲存體連結服務，將 Azure 儲存體帳戶連結到資料處理站。 連結的服務具有連線資訊，可供 Data Factory 服務在執行階段中用來連線到 Blob 儲存體。 資料集會指定容器、資料夾和包含來源資料的檔案 (選擇性)。 

    ![新增連結服務按鈕](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. 在 [新增連結服務] 視窗中，執行下列步驟： 

    1. 在 [名稱] 欄位中指定 **AzureStorageLinkedService**。 
    2. 在 [儲存體帳戶名稱] 欄位中，選取您的 Azure 儲存體帳戶。
    3. 按一下 [測試連線]，以測試 Azure 儲存體帳戶的連線。  
    4. 按一下 [儲存] 以儲存連結服務。

        ![新增 Azure 儲存體連結服務](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. 在 [檔案路徑] 欄位中，按一下 [瀏覽]。  

    ![檔案的瀏覽按鈕](./media/tutorial-copy-data-portal/file-browse-button.png)
14. 瀏覽至 **adftutorial/input** 資料夾，選取 **emp.txt** 檔案，然後按一下 [完成]。 或者，您可以按兩下 emp.txt。 

    ![選取輸入檔案](./media/tutorial-copy-data-portal/select-input-file.png)
15. 確認 [檔案格式] 設為 [文字格式]，且 [資料行分隔符號] 設為 [逗號 (`,`)]。 如果來源檔案使用不同的資料列和資料行分隔符號，您可以在 [檔案格式] 欄位中按一下 [偵測文字格式]。 「複製資料」工具會自動為您偵測檔案格式和分隔符號。 您仍可以覆寫這些值。 您可以按一下 [預覽資料]，以預覽此頁面上的資料。

    ![偵測文字格式](./media/tutorial-copy-data-portal/detect-text-format.png)
17. 在 [屬性] 視窗中切換至 [結構描述] 索引標籤，然後按一下 [匯入結構描述]。 請留意應用程式偵測到來源檔案中的兩個資料行。 您將在此處匯入結構描述，以便將資料行從來源資料存放區對應至接收資料存放區。 如果您不需要對應資料行，可以略過此步驟。 在本教學課程中，請匯入結構描述。

    ![偵測來源結構描述](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. 現在，請切換至**含有管線的索引標籤**，或按一下左側**樹狀檢視**中的管線。  

    ![管線索引標籤](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. 在屬性視窗的 [來源資料集] 欄位中，確認已選取 **SourceBlobDataset**。 您可以按一下 [預覽資料]，以預覽此頁面上的資料。 
    
    ![來源資料集](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. 切換至 [接收] 索引標籤，然後按一下 [新增] 以建立接收資料集。 

    ![新增接收資料集功能表](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. 在 [新增資料集] 視窗中選取 [Azure SQL Database]，然後按一下 [完成]。 在本教學課程中，您會將資料複製到 Azure SQL 資料庫。 

    ![選取 Azure SQL Database](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. 在屬性視窗的 [一般] 索引標籤中，將名稱設為 **OutputSqlDataset**。 
    
    ![輸出資料集名稱](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. 切換至 [連線] 索引標籤，然後針對 [連結服務] 按一下 [新增]。 資料集必須與連結服務相關聯。 連結的服務具有連接字串，可供 Data Factory 服務在執行階段中用來連線到 Azure SQL 資料庫。 資料集會指定要將資料複製過去的容器、資料夾和檔案 (選擇性)。 
    
    ![新增連結服務按鈕](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. 在 [新增連結服務] 視窗中，執行下列步驟： 

    1. 在 [名稱] 欄位輸入 **AzureSqlDatabaseLinkedService**。 
    2. 在 [伺服器名稱] 欄位選取您的 Azure SQL 伺服器。
    4. 在 [資料庫名稱] 欄位選取您的 Azure SQL 資料庫。 
    5. 在 [使用者名稱] 欄位輸入使用者的名稱。 
    6. 在 [密碼] 欄位輸入使用者的密碼。 
    7. 按一下 [測試連線] 以測試連線。
    8. 按一下 [儲存] 以儲存連結服務。 
    
        ![新增 Azure SQL Database 連結服務](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. 針對 [資料表] 選取 **[dbo].[emp]**。 

    ![選取 emp 資料表](./media/tutorial-copy-data-portal/select-emp-table.png)
27. 切換至 [結構描述] 索引標籤，然後按一下 [匯入結構描述]。 

    ![匯入目的地結構描述](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. 選取 [識別碼] 資料行，然後按一下 [刪除]。 [識別碼] 資料行是 SQL 資料庫中的身分識別資料行，因此複製活動不需要將資料插入此資料行中。

    ![刪除識別碼資料行](./media/tutorial-copy-data-portal/delete-id-column.png)
30. 切換至含有**管線**的索引標籤，並確認已選取 **OutputSqlDataset** 作為 [接收資料集]。

    ![管線索引標籤](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. 在 [屬性] 視窗中切換至底部的 [對應] 索引標籤，然後按一下 [匯入結構描述]。 請注意，來源檔案中的第一個和第二個資料行會對應至 SQL 資料庫中的 **FirstName** 和 **LastName** 欄位。

    ![對應結構描述](./media/tutorial-copy-data-portal/map-schemas.png)
33. 按一下 [驗證] 按鈕，以驗證管線。 按一下 [向右箭號]，以關閉驗證視窗。

    ![管線驗證輸出](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. 按一下右上角的 [程式碼] 按鈕。 您會看到與管線相關聯的 JSON 程式碼。 

    ![程式碼按鈕](./media/tutorial-copy-data-portal/code-button.png)
35. 您會看到如下列程式碼片段的 JSON 程式碼：  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>對管線執行測試
您可以先對管線執行測試，再將成品 (連結服務、資料集和管線) 發佈至 Data Factory 或您自己的 VSTS GIT 存放庫。 

1. 若要對管線執行測試，請按一下工具列上的 [測試執行]。 您可以在視窗底部的 [輸出] 索引標籤中檢視管線執行的狀態。 

    ![測試執行按鈕](./media/tutorial-copy-data-portal/test-run-output.png)
2. 確認來源檔案中的資料已插入目的地 SQL 資料庫中。 

    ![驗證 SQL 輸出](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. 按一下左窗格中的 [發佈]。 此動作會將您已建立的實體 (連結服務、資料集和管線) 發佈至 Azure Data Factory。

    ![發佈按鈕](./media/tutorial-copy-data-portal/publish-button.png)
4. 請靜待 [發佈成功] 訊息顯示。 若要檢視通知訊息，請按一下左側資訊看板上的 [顯示通知] 索引標籤。 按一下 **X** 以關閉通知視窗。

    ![顯示通知](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>設定程式碼存放庫
您可以將與資料處理站成品相關聯的程式碼發佈至 Visual Studio Team System (VSTS) 程式碼存放庫。 在此步驟中，您會建立程式碼存放庫。 

如果您不想使用 VSTS 程式碼存放庫，您可以略過此步驟，並繼續依照上一個步驟中的程序發佈至 Data Factory。 

1. 按一下位於左側的 [Data Factory] 或其旁邊的向下箭號，然後按一下 [設定程式碼存放庫]。 

    ![程式碼按鈕](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. 在 [存放庫設定] 頁面中，執行下列步驟： 
    1. 在 [存放庫類型] 欄位選取 [Visual Studio Team Services Git]。
    2. 在 [Visual Studio Team Services 帳戶] 欄位選取您的 VSTS 帳戶。
    3. 在 [專案名稱] 欄位選取您 VSTS 帳戶中的專案。
    4. 輸入 **Tutorial2**，作為要與您資料處理站相關聯的 **Git 存放庫名稱**。 
    5. 確認已選取 [將現有的 Data Factory 資源匯入存放庫] 選項。 
    6. 按一下 [儲存]  來儲存這些設定。 

        ![存放庫設定](./media/tutorial-copy-data-portal/repository-settings.png)
3. 確認已選取 **VSTS GIT** 作為存放庫。

    ![已選取 VSTS GIT](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. 在網頁瀏覽器的個別索引標籤中瀏覽至 **Tutorial2** 存放庫，您會看到兩個分支：**master** 和 **adf_publish**。

    ![master 和 adf_publish 分支](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. 確認 Data Factory 實體的 **JSON 檔案**位於 **master** 分支中。

    ![master 分支中的檔案](./media/tutorial-copy-data-portal/master-branch-files.png)
6. 確認 **adf_publish** 分支中還沒有 **JSON 檔案**。 

    ![adf_publish 分支中的檔案](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. 新增**管線**的**說明**，然後按一下工具列上的 [儲存] 按鈕。 

    ![新增管線的說明](./media/tutorial-copy-data-portal/pipeline-description.png)
8. 現在，您應該會在 **Tutorial2** 存放庫中看到具有您使用者名稱的**分支**。 您所做的變更會顯示在您自己的分支中，而不會顯示在 master 分支中。 您只能從 master 分支發佈實體。

    ![您的分支](./media/tutorial-copy-data-portal/your-branch.png)
9. 移至 [同步] 按鈕上方 (先不要點按)，再選取 [認可變更] 按鈕，然後按一下 [同步] 按鈕以將您的變更與 **master** 分支同步。 

    ![認可並同步您的變更](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. 在 [同步您的變更] 視窗中，執行下列動作： 

    1. 確認 **CopyPipeline** 顯示於更新的管線清單中。
    2. 確認已選取 [在同步後發佈變更]。 如果您取消選取此選項，則只會將您分支中的變更與 master 分支同步，而不會將變更發佈至 Data Factory 服務。 您後續可以使用 [發佈] 按鈕加以發佈。 如果您核取此選項，則變更會先同步至 master，然後發佈至 Data Factory 服務。
    3. 按一下 [同步]。 

    ![同步您的變更視窗](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. 現在，您可以檢視 **Tutorial2** 存放庫中位於 **adf_publish** 分支內的檔案。 您也可以在此分支中找到您 Data Factory 方案適用的 Azure Resource Manager 範本。  

    ![adf_publish 分支中的檔案](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>手動觸發管線
在此步驟中，您會手動觸發您在上一個步驟中發佈的管線。 

1. 按一下工具列上的 [觸發程序]，然後按一下 [立即觸發]。 

    ![立即觸發功能表](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. 切換至左側的 [監視] 索引標籤。 您會看到手動觸發程序所觸發的管線執行。 您可以使用 [動作] 資料行中的連結來檢視活動詳細資料，以及重新執行管線。

    ![監視管線執行](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. 若要檢視與管線執行相關聯的活動執行，請按一下 [動作] 資料行中的 [檢視活動執行] 連結。 此範例中只有一個活動，因此您在清單中只會看到一個項目。 若需關於複製作業的詳細資訊，請按一下 [動作] 資料行中的 [詳細資料] 連結 (眼鏡圖示)。 您可以按一下頂端的 [管線]，以切換回 [管線執行] 檢視。 若要重新整理檢視，請按一下 [重新整理]。

    ![檢視活動執行](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. 確認有兩個以上的資料列新增至 Azure SQL 資料庫中的 **emp** 資料表。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>觸發排程上的管線
在此排程中，您會建立管線的排程器觸發程序。 此觸發程序會依照指定的排程 (每小時、每天等) 執行管線。 在此範例中，您會建立每分鐘執行一次的觸發程序，並讓其在指定的結束日期時間停止。 

1. 切換至左側的 [編輯] 索引標籤。 

    ![編輯索引標籤](./media/tutorial-copy-data-portal/edit-tab.png)
2. 按一下 [觸發程序]，然後選取 [新增/編輯]。 如果管線並非作用中，請將其切換過去。 

    ![觸發程序新增/編輯功能表](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. 在 [新增觸發程序] 視窗中按一下 [選擇觸發程序...]，然後按一下 [+ 新增]。 

    ![新增觸發程序 - 新增觸發程序](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. 在 [新增觸發程序] 視窗中，執行下列步驟： 

    1. 將 [名稱] 設為 **RunEveryMinute**。
    2. 選取 [結束] 的 [日期]。 
    3. 按一下 [結束日期] 的下拉式清單。
    4. 選取**當天日期**。 根據預設，結束日期會設為次日。 
    5. 將**分鐘**部分更新為當天日期時間的數分鐘之後。 在您發佈變更之後，才會啟動觸發程序。 因此，如果您將其設為短短幾分鐘之後，且您未在那之前發佈變更，則不會看到觸發程序執行。  
    6. 按一下 [套用]。 

        ![設定觸發程序屬性](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. 核取 [已啟動] 選項。 您可以先將其停用，其後再使用此核取方塊加以啟動。
    8. 按 [下一步] 。

        ![已啟動觸發程序 - 下一步](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 每次執行管線都會產生相關成本。 因此，請適當設定結束日期。 
6. 在 [觸發程序執行參數] 頁面中檢閱警告，然後按一下 [完成]。 此範例中的管線未使用任何參數。 

    ![管線參數](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. 按一下 [發佈]，將變更發佈至存放庫。 在發佈成功之前，觸發程序並未實際啟動。 

    ![發佈觸發程序](./media/tutorial-copy-data-portal/publish-trigger.png) 
8. 切換至左側的 [監視] 索引標籤，以檢視已觸發的管線執行。 

    ![已觸發的管線執行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. 若要從管線執行檢視切換至觸發程序執行檢視，請按一下 [管線執行]，然後選取 [觸發程序執行]。
    
    ![觸發程序執行功能表](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. 您可以在清單中檢視觸發程序執行。 

    ![觸發程序執行清單](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. 確認在指定的結束時間之前，每個管線執行每分鐘會在 **emp** 資料表中插入兩個資料列。 

## <a name="next-steps"></a>後續步驟
在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 您已了解如何︰ 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立具有複製活動的管線。
> * 對管線執行測試
> * 手動觸發管線
> * 觸發排程上的管線
> * 監視管線和活動執行。


進入下列教學課程，以了解如何將資料從內部部署複製到雲端： 

> [!div class="nextstepaction"]
>[將資料從內部部署複製到雲端](tutorial-hybrid-copy-portal.md)
