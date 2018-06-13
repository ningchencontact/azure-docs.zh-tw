---
title: 使用 Azure 複製資料工具複製資料 | Microsoft Docs
description: 建立 Azure 資料處理站，然後使用「複製資料」工具將資料從 Azure Blob 儲存體複製到 SQL 資料庫。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
ms.locfileid: "30171792"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>使用複製資料工具將資料從 Azure Blob 儲存體複製到 SQL 資料庫
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [第 1 版 - 正式運作](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [第 2 版 - 預覽](tutorial-copy-data-tool.md)

在這個教學課程中，您會使用 Azure 入口網站來建立資料處理站。 接著，您會使用複製資料工具來建立管線，以將資料從 Azure Blob 儲存體複製到 SQL 資料庫。 

> [!NOTE]
> 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。
>
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory (正式推出版本)，請參閱[開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存體帳戶**：使用 Blob 儲存體作為「來源」資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)中的指示。
* **Azure SQL Database**：使用 Azure SQL 資料庫作為「接收」資料存放區。 如果您沒有 SQL 資料庫，請參閱[建立 SQL 資料庫](../sql-database/sql-database-get-started-portal.md)中的指示。

### <a name="create-a-blob-and-a-sql-table"></a>建立 Blob 和 SQL 資料表

請執行下列步驟，為本教學課程準備 Blob 儲存體和您的 SQL 資料庫。

#### <a name="create-a-source-blob"></a>建立來源 Blob

1. 啟動 [記事本]。 複製下列文字，並將其儲存在磁碟上名為 **inputEmp.txt** 的檔案：

    ```
    John|Doe
    Jane|Doe
    ```

2. 建立名為 **adfv2tutorial** 的容器，並將 inputEmp.txt 檔案上傳至該容器。 您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](http://storageexplorer.com/)。

#### <a name="create-a-sink-sql-table"></a>建立接收 SQL 資料表

1. 使用以下 SQL 指令碼，在您的 SQL 資料庫中建立名為 **dbo.emp** 的資料表：

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

2. 允許 Azure 服務存取 SQL Server。 確認您已為執行 SQL Server 的伺服器啟用 [允許存取 Azure 服務] 設定。 此設定可讓 Data Factory 將資料寫入 SQL Server 執行個體。 若要確認並開啟此設定，請採取下列步驟：

    a. 從左側選取 [更多服務]，然後選取 [SQL Server]。

    b. 選取您的伺服器，然後選取 [設定] > [防火牆]。

    c. 在 [防火牆設定] 頁面上，將 [允許存取 Azure 服務] 選項設定為 [開啟]。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表中，選取 [+ 新增] > [資料 + 分析] > [資料處理站]： 
   
   ![新資料處理站的建立](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面的 [名稱] 下，輸入 **ADFTutorialDataFactory**。 
      
     ![新增 Data Factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   資料處理站的名稱必須是「全域唯一」的名稱。 您可能會收到下列錯誤訊息：
   
   ![新增資料處理站錯誤訊息](./media/tutorial-copy-data-tool/name-not-available-error.png)

   如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 例如，使用**您的名稱****ADFTutorialDataFactory**。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
3. 選取要在其中建立新資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，採取下列其中一個步驟︰
     
    a. 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。

    b. 選取 [建立新的] ，然後輸入資源群組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。

5. 在 [版本] 下，選取 [V2 (預覽)] 作為版本。
6. 在 [位置] 下，選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (例如 Azure 儲存體和 SQL Database) 和計算 (例如 Azure HDInsight) 可位於其他地區和區域。
7. 選取 [釘選到儀表板]。 
8. 選取 [建立] 。
9. 儀表板上的 [部署資料處理站] 圖格會顯示程序狀態。

    ![部署資料處理站圖格](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 建立完成後，隨即會顯示 **Data Factory** 首頁。
   
    ![Data Factory 首頁](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 選取 [編寫與監視] 圖格，可在另一個索引標籤中啟動 Azure Data Factory 使用者介面 (UI)。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在 [現在就開始吧] 頁面上選取 [複製資料] 圖格，以啟動複製資料工具。 

   ![複製資料工具圖格](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. 在 [屬性] 頁面的 [工作名稱] 下，輸入 **CopyFromBlobToSqlPipeline**。 然後，選取 [下一步]。 Data Factory 使用者介面會使用指定的工作名稱建立管線。 

    ![屬性頁面](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] 頁面上選取 [Azure Blob 儲存體]，然後選取 [下一步]。 來源資料位於 Blob 儲存體中。 

    ![來源資料存放區頁面](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. 在 [指定 Azure Blob 儲存體帳戶] 頁面上，採取以下步驟︰

    a. 在 [連線名稱] 下輸入 **AzureStorageLinkedService**。

    b. 從 [儲存體帳戶名稱] 下拉式清單中選取您的儲存體帳戶名稱。

    c. 選取 [下一步] 。 

    ![指定儲存帳號](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    已連結的服務會將資料存放區或計算連結到資料處理站。 在此案例中，您會建立儲存體連結服務，將儲存體帳戶連結到資料存放區。 連結服務具有連線資訊，可供 Data Factory 在執行階段中用來連線到 Blob 儲存體。 資料集會指定包含來源資料的容器、資料夾和檔案 (選擇性)。 

5. 在 [選擇輸入檔案或資料夾] 頁面上，執行以下步驟︰
    
    a. 瀏覽至 **adfv2tutorial/input** 資料夾。

    b. 選取 **inputEmp.txt** 檔案。

    c. 選取 [選擇]。 或者，您可以按兩下 **inputEmp.txt** 檔案。

    d. 選取 [下一步] 。 

    ![選擇輸入檔案或資料夾](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. 在 [檔案格式設定] 頁面上，留意到此工具會自動偵測資料行和資料列分隔符號。 選取 [下一步] 。 您也可以預覽資料，並檢視這個頁面上的輸入資料結構描述。 

    ![檔案格式設定](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. 在 [目的地資料存放區] 頁面上，選取 [Azure SQL Database]，然後選取 [下一步]。

    ![目的地資料存放區](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. 在 [指定 Azure SQL 資料庫] 頁面上，採取以下步驟︰ 

    a. 在 [連線名稱] 下輸入 **AzureSqlDatabaseLinkedService**。

    b. 在 [伺服器名稱] 下，選取您的 SQL Server 執行個體。

    c. 在 [資料庫名稱] 下，選取您的 SQL 資料庫。

    d. 在 [使用者名稱] 下，輸入使用者名稱。

    e. 在 [密碼] 下，輸入使用者的密碼。

    f. 選取 [下一步] 。 

    ![指定 SQL 資料庫](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    資料集必須與連結的服務相關聯。 連結服務具有連接字串，可供 Data Factory 在執行階段中用來連線到 SQL 資料庫。 資料集會指定要作為資料複製目的地的容器、資料夾和檔案 (選擇性)。

9. 在 [資料表對應] 頁面上，選取 **[dbo].[emp]** 資料表，然後選取 [下一步]。 

    ![資料表對應](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. 在 [結構描述對應] 頁面上，請注意系統會將輸入檔案中的第一個和第二個資料行對應至 **emp** 資料表的 **FirstName** 和 **LastName** 資料行。

    ![結構描述對應頁面](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. 在 [設定] 頁面上，選取 [下一步]。 

    ![設定頁面](./media/tutorial-copy-data-tool/settings-page.png)
12. 在 [摘要] 頁面上檢閱設定，然後選取 [下一步]。

    ![摘要頁面](./media/tutorial-copy-data-tool/summary-page.png)
13. 在**部署頁面**上選取 [監視] 來監視管線 (工作)。

    ![部署頁面](./media/tutorial-copy-data-tool/deployment-page.png)
14. 請注意，系統會自動選取左側的 [監視] 索引標籤。 [動作] 資料行中會有連結可供檢視活動執行詳細資料，以及重新執行管線。 選取 [重新整理] 即可重新整理清單。 

    ![監視管線回合](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作] 資料行中的 [檢視活動執行] 連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 如需關於複製作業的詳細資料，請選取 [動作] 資料行中的 [詳細資料] 連結 (眼鏡圖示)。 若要回到 [管線執行] 檢視，請選取頂端的 [管線] 連結。 若要重新整理檢視，請選取 [重新整理]。 

    ![監視活動回合](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 選取左側的 [編輯] 索引標籤以切換至編輯器模式。 您可以使用編輯器更新透過此工具建立的連結服務、資料集和管線。 選取 [程式碼] 以檢視目前編輯器中開啟的實體 JSON 程式碼。 如需在 Data Factory 使用者介面中編輯這些實體的詳細資訊，請參閱[本教學課程的 Azure 入口網站版本](tutorial-copy-data-portal.md)。

    ![[編輯器] 索引標籤](./media/tutorial-copy-data-tool/edit-tab.png)
17. 確認資料有插入 SQL 資料庫中的 **emp** 資料表。

    ![確認 SQL 輸出](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>後續步驟
在此範例中，管線會將資料從 Blob 儲存體複製到 SQL 資料庫。 您已了解如何︰ 

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

若要了解如何將資料從內部部署複製到雲端，請進入下列教學課程： 

> [!div class="nextstepaction"]
>[將資料從內部部署複製到雲端](tutorial-hybrid-copy-data-tool.md)
