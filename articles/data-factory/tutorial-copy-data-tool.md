---
title: "使用 Azure 複製資料工具複製資料 | Microsoft Docs"
description: "建立 Azure 資料處理站，然後使用「複製資料」工具將資料從 Azure Blob 儲存體複製到 Azure SQL Database。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>使用複製資料工具將資料從 Azure Blob 複製到 Azure SQL Database
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [第 2 版 - 預覽](tutorial-copy-data-tool.md)

在這個教學課程中，您會使用 Azure 入口網站來建立資料處理站。 接著，您會使用複製資料工具來建立管線，以將資料從 Azure Blob 儲存體複製到 Azure SQL 資料庫。 

> [!NOTE]
> - 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。
>
> - 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線
> * 監視管線和活動執行。

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。
* **Azure 儲存體帳戶**。 您會使用 Blob 儲存體作為**來源**資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文，按照步驟來建立帳戶。
* **Azure SQL Database**。 您會使用資料庫作為**接收**資料存放區。 如果您沒有 Azure SQL Database，請參閱[建立 Azure SQL 資料庫](../sql-database/sql-database-get-started-portal.md)一文，按照步驟建立資料庫。

### <a name="create-a-blob-and-a-sql-table"></a>建立 Blob 和 SQL 資料表

現在，請執行下列步驟，以準備本教學課程的 Azure Blob 和 Azure SQL Database：

#### <a name="create-a-source-blob"></a>建立來源 Blob

1. 啟動 [記事本]。 複製下列文字，並在磁碟上儲存為 **inputEmp.txt** 檔案。

    ```
    John|Doe
    Jane|Doe
    ```

2. 使用 [Azure 儲存體總管](http://storageexplorer.com/)之類的工具建立 **adfv2tutorial** 容器，然後將 **inputEmp.txt** 檔案上傳至該容器。

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

2. 允許 Azure 服務存取 SQL 伺服器。 確定已為 Azure SQL 伺服器啟用 [允許存取 Azure 服務] 設定，讓 Data Factory 服務可以將資料寫入您的 Azure SQL 伺服器。 若要確認並開啟此設定，請執行下列步驟：

    1. 按一下左邊的 [更多服務] 中樞，然後按一下 [SQL Server]。
    2. 選取您的伺服器，然後按一下 [設定] 下的 [防火牆]。
    3. 在 [防火牆設定] 頁面中，對 [允許存取 Azure 服務] 按一下 [開啟]。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
     ![新增資料處理站頁面](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您在 [名稱] 欄位看到下列錯誤，請變更資料處理站的名稱 (例如 yournameADFTutorialBulkCopyDF)。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
     ![新增資料處理站頁面](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，請執行下列其中一個步驟︰
     
      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。 
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
      若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 對 [版本] 選取 [V2 (預覽)]。
5. 選取 Data Factory 的 [位置]  。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他位置/區域。
6. 選取 [釘選到儀表板]。     
7. 按一下頁面底部的 [新增] 。
8. 在儀表板上，您會看到狀態如下的下列圖格︰**正在部署資料處理站**。 

    ![部署資料處理站圖格](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。
   
   ![Data Factory 首頁](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. 按一下 [撰寫與監視] 圖格，以在另一個索引標籤中啟動 Azure Data Factory 使用者介面 (UI)。 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在 [開始使用] 頁面中，按一下 [複製資料] 圖格以啟動複製資料工具。 

   ![複製資料工具圖格](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. 在複製資料工具的 [屬性] 頁面中，指定 [CopyFromBlobToSqlPipeline] 作為 [工作名稱]，然後按 [下一步]。 Data Factory 使用者介面會使用您為工作名稱指定的名稱建立管線。 

    ![複製資料工具 - 屬性頁面](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] 頁面上，選取 [Azure Blob 儲存體]，然後按 [下一步]。 來源資料位於 Azure Blob 儲存體中。 

    ![來源資料存放區頁面](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. 在 [指定 Azure Blob 儲存體帳戶] 頁面中，執行以下步驟︰ 
    1. 輸入 **AzureStorageLinkedService** 作為 [連線名稱]。
    2. 從下拉式清單選取 [儲存體帳戶名稱]。
    3. 按 [下一步] 。 

        ![指定 Blob 儲存體帳戶](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        已連結的服務會將資料存放區或計算連結到資料處理站。 在此案例中，您會建立 Azure 儲存體連結服務，將 Azure 儲存體帳戶連結到資料處理站。 連結的服務具有連線資訊，可供 Data Factory 服務在執行階段中用來連線到 Blob 儲存體。 資料集會指定包含來源資料的容器、資料夾和檔案 (選擇性)。 
5. 在 [選擇輸入檔案或資料夾] 頁面中，執行以下步驟︰
    
    1. 瀏覽至 **adfv2tutorial/input** 資料夾。
    2. 選取 [inputEmp.txt]
    3. 按一下 [選擇]。 或者，您可以按兩下 [inputEmp.txt]。 
    4. 按 [下一步] 。 

    ![選擇輸入檔案或資料夾](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. 在 [檔案格式設定] 頁面中，請注意此工具會自動偵測資料行和資料列分隔符號，然後按 [下一步]。 您也可以預覽資料，並檢視這個頁面上輸入資料的結構描述。 

    ![檔案格式設定頁面](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. 在 [目的地資料存放區] 頁面上，選取 [Azure SQL Database]，然後按 [下一步]。 

    ![目的地資料存放區頁面](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. 在 [指定 Azure SQL 資料庫] 頁面中，執行以下步驟︰ 

    1. 指定 **AzureSqlDatabaseLinkedService** 作為 [連線名稱]。 
    2. 針對 [伺服器名稱] 選取您的 Azure SQL 伺服器。
    3. 針對 [資料庫名稱] 選取您的 Azure SQL 資料庫。
    4. 針對 [使用者名稱] 指定使用者名稱。
    5. 針對 [密碼] 指定使用者的密碼。
    6. 按 [下一步] 。 

        ![指定 Azure SQL 資料庫](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        資料集必須與連結的服務相關聯。 連結的服務具有連接字串，可供 Data Factory 服務在執行階段中用來連線到 Azure SQL 資料庫。 資料集會指定要作為資料複製目的地的容器、資料夾和檔案 (選擇性)。
9.  在 [資料表對應] 頁面上，選取 [[dbo].[emp]]，然後按 [下一步]。 

    ![資料表對應頁面](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. 在 [結構描述對應] 頁面上，請注意系統會將輸入檔案中的第一個和第二個資料行對應至 **emp** 資料表的 **FirstName** 和 **LastName** 資料行。 

    ![結構描述對應頁面](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. 在 [設定] 頁面上，按 [下一步]。 

    ![設定頁面](./media/tutorial-copy-data-tool/settings-page.png)
12. 在 [摘要] 頁面中，檢閱設定，然後按 [下一步]。

    ![摘要頁面](./media/tutorial-copy-data-tool/summary-page.png)
13. 在 [部署] 頁面上，按一下 [監視] 來監視管線 (工作)。

    ![部署頁面](./media/tutorial-copy-data-tool/deployment-page.png)
14. 請注意，系統會自動選取左側的 [監視] 索引標籤。 您會在 [動作] 資料行中看到有連結可供檢視活動執行詳細資料，以及可供重新執行管線。 按一下 [重新整理] 即可重新整理清單。 

    ![監視管線回合](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. 若要檢視與此管線執行相關聯的活動執行，按一下 [動作] 資料行中的 [檢視活動執行] 連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 如需複製作業的相關詳細資料，請按一下 [動作] 資料行中的 [詳細資料] 連結 (眼鏡圖示)。 若要切換回 [管線執行] 檢視，按一下頂端的 [管線] 連結。 若要重新整理檢視，按一下 [重新整理]。 

    ![監視活動回合](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 按一下左側的 [編輯] 索引標籤以切換至編輯器模式。 您可以使用編輯器更新此工具所建立的連結服務、資料集和管線。 按一下 [程式碼] 以檢視與編輯器中開啟之實體相關聯的 JSON 程式碼。 如需在 Data Factory 使用者介面中編輯這些實體的詳細資訊，請參閱[本教學課程的 Azure 入口網站版本](tutorial-copy-data-portal.md)。

    ![[編輯器] 索引標籤](./media/tutorial-copy-data-tool/edit-tab.png)
17. 確認資料有插入 Azure SQL 資料庫中的 **emp** 資料表。 

    ![確認 SQL 輸出](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>後續步驟
在此範例中，管線會將資料從 Azure Blob 儲存體複製到 Azure SQL 資料庫。 您已了解如何︰ 

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線
> * 監視管線和活動執行。

進入下列教學課程，以了解如何將資料從內部部署複製到雲端： 

> [!div class="nextstepaction"]
>[將資料從內部部署複製到雲端](tutorial-hybrid-copy-data-tool.md)