---
title: "使用 Azure Data Factory 將資料從 SQL Server 複製到 Blog 儲存體 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 中的自我裝載整合執行階段，將資料從內部部署資料存放區複製到 Azure 雲端。"
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 64cd758e2f40ff2b18abbff1194a7e57389d8a54
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>教學課程：將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體
在本教學課程中，您會使用 Azure Data Factory 使用者介面 (UI) 來建立資料處理站管線，以將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。 您要建立及使用自我裝載的整合執行階段，其會在內部部署與雲端資料存放區之間移動資料。 

> [!NOTE]
> 本文適用於第 2 版的 Azure Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
> 
> 本文不提供 Data Factory 服務的詳細簡介。 如需詳細資訊，請參閱 [Azure Data Factory 簡介](introduction.md)。 

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立自我裝載整合執行階段。
> * 建立 SQL Server 和 Azure 儲存體連結的服務。 
> * 建立 SQL Server 和 Azure Blob 資料集。
> * 建立具有複製活動的管線來移動資料。
> * 啟動管線執行。
> * 監視管道執行。

## <a name="prerequisites"></a>先決條件
### <a name="azure-subscription"></a>Azure 訂閱
開始之前，如果您還沒有 Azure 訂用帳戶，[請建立免費帳戶](https://azure.microsoft.com/free/)。

### <a name="azure-roles"></a>Azure 角色
若要建立資料處理站執行個體，您用來登入 Azure 的使用者帳戶必須具備「參與者」或「擁有者」角色，或必須是 Azure 訂用帳戶的「管理員」。 

若要檢視您在訂用帳戶中所擁有的權限，請移至 Azure 入口網站，選取右上角的使用者名稱，然後選取 [權限]。 如果您有多個訂用帳戶的存取權，請選取適當的訂用帳戶。 如需將使用者新增至角色的範例指示，請參閱[新增角色](../billing/billing-add-change-azure-subscription-administrator.md)文章。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016 和 2017
在本教學課程中，您會使用內部部署 SQL 資料庫作為「來源」資料存放區。 您在本教學課程中建立於 Data Factory 的管線會將資料從此內部部署 SQL Server 資料庫 (來源) 複製到 Azure Blob 儲存體 (接收)。 然後在 SQL Server 資料庫中建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。 

1. 啟動 SQL Server Management Studio。 如果它尚未安裝在電腦上，請移至[下載 SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。 

2. 使用您的認證連線到 SQL Server 執行個體。 

3. 建立範例資料庫。 在樹狀檢視中，以滑鼠右鍵按一下 [資料庫]，然後選取 [新增資料庫]。 
 
4. 在 [新增資料庫] 視窗中，輸入資料庫的名稱，然後選取 [確定]。 

5. 若要建立 **emp** 資料表並在其中插入一些範例資料，請針對資料庫執行下列查詢指令碼：

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. 在樹狀檢視中，以滑鼠右鍵按一下您建立的資料庫，然後選取 [新增查詢]。

### <a name="azure-storage-account"></a>Azure 儲存體帳戶
在本教學課程中，您可以使用一般用途的 Azure 儲存體帳戶 (特別是 Blob 儲存體) 作為目的地/接收資料存放區。 如果您沒有一般用途的 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)戶。 您在本教學課程中建立於 Data Factory 的管線會將資料從內部部署 SQL Server 資料庫 (來源) 複製到此 Azure Blob 儲存體 (接收)。 

#### <a name="get-storage-account-name-and-account-key"></a>取得儲存體帳戶名稱和帳戶金鑰
您會在此教學課程中使用 Azure 儲存體帳戶的名稱和金鑰。 執行下列作業，以取得儲存體帳戶的名稱和金鑰： 

1. 使用您的使用者名稱和密碼登入 [Azure 入口網站](https://portal.azure.com)。 

2. 在左側功能表中，選取 [更多服務]，使用 **Storage** 關鍵字進行篩選，然後選取 [儲存體帳戶]。

    ![搜尋儲存體帳戶](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. 在儲存體帳戶清單中，篩選您的儲存體帳戶 (如有需要)，然後選取您的儲存體帳戶。 

4. 在 [儲存體帳戶] 視窗中，選取 [存取金鑰]。

    ![取得儲存體帳戶名稱和金鑰](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. 在 [儲存體帳戶名稱] 和 [金鑰1] 方塊中複製值，然後將它們貼到 [記事本] 或另一個編輯器中，以供稍後在教學課程中使用。 

#### <a name="create-the-adftutorial-container"></a>建立 adftutorial 容器 
在這一節中，您會在 Azure Blob 儲存體中建立一個名為 **adftutorial** 的 Blob 容器。 

1. 在 [儲存體帳戶] 視窗中，切換至 [概觀]，然後選取 [Blob]。 

    ![選取 Blob 選項](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. 在 [Blob 服務] 視窗中，選取 [容器]。 

    ![新增容器按鈕](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. 在 [新容器] 視窗的 [名稱] 方塊中，輸入 **adftutorial**，然後選取 [確定]。 

    ![輸入容器名稱](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. 在容器清單中選取 [adftutorial]。  

    ![選取容器](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. 保留 [容器] 視窗以供 **adftutorial** 開啟。 您可以在本快速入門結尾處使用它來確認輸出。 Data Factory 會在此容器中自動建立輸出資料夾，因此您不需要建立輸出資料夾。

    ![容器視窗](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>建立 Data Factory
在此步驟中，您可以建立資料處理站，並啟動 Azure Data Factory 使用者介面，在資料處理站中建立管線。 

1. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
     ![新增資料處理站頁面](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您在 [名稱] 欄位看到下列錯誤，請變更資料處理站的名稱 (例如 yournameADFTutorialBulkCopyDF)。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
     ![新增資料處理站頁面](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![部署資料處理站圖格](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。
   
   ![Data Factory 首頁](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. 按一下 [撰寫與監視] 圖格，以在個別索引標籤中啟動 Azure Data Factory 使用者介面。 


## <a name="create-a-pipeline"></a>建立管線

1. 在 [開始使用] 頁面中，按一下 [建立管線]。 系統會自動為您建立管線。 您會在樹狀檢視中看到管線及其開啟的編輯器。 

   ![開始使用頁面](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. 在底部 [屬性] 視窗的 [一般] 索引標籤中，輸入 **SQLServerToBlobPipeline** 作為 [名稱]。

   ![管線名稱](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. 在 [活動] 工具箱中展開 [資料流程]，並將 [複製] 活動拖曳至管線設計表面。 將這個活動的名稱設定為 **CopySqlServerToAzureBlobActivity**。

   ![活動名稱](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. 在 [屬性] 視窗中，切換至 [來源] 索引標籤，然後按一下 [+ 新增]。

   ![新增來源資料集 - 按鈕](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. 在 [新增資料集] 視窗中，搜尋 **SQL Server**，選取 [SQL Server]，然後按一下 [完成]。 您會看到標題為 **SqlServerTable1** 的新索引標籤。 您也會在左側的樹狀檢視中看到 **SqlServerTable1** 資料集。 

   ![選取 SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. 在 [屬性] 視窗的 [一般] 索引標籤中，輸入 **SqlServerDataset** 作為 [名稱]。
    
   ![來源資料集 - 名稱](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. 切換至 [連線] 索引標籤，然後按一下 [+ 新增]。 您會在此步驟中建立來源資料存放區 (SQL Server 資料庫) 的連線。 

   ![來源資料集 - 新增連線按鈕](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. 在 [新增連結服務] 視窗中，按一下 [新增整合執行階段]。 在本節中，您可以建立自我裝載整合執行階段，並使用 SQL Server 資料庫將它與內部部署電腦產生關聯。 自我裝載的整合執行階段是一項元件，可將資料從您電腦上的 SQL Server 複製到 Azure blob 儲存體。 

   ![新增整合執行階段](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. 在 [整合執行階段安裝] 視窗中，選取 [私人網路]，然後按 [下一步]。 

   ![選取私人網路](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. 輸入整合執行階段的名稱，然後按 [下一步]。  
    
   ![整合執行階段 - 名稱](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. 在 [選項 1：快速安裝] 區段中，按 [按一下這裡啟動此電腦的快速安裝]。 

   ![按一下 [快速安裝] 連結](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. 在 [Integration Runtime (自我裝載) 快速安裝] 視窗中，按一下 [關閉]。 

   ![整合執行階段安裝 - 成功](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. 在網頁瀏覽器的 [整合執行階段安裝] 視窗中，按一下 [完成]。 

   ![整合執行階段安裝 - 完成](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. 在 [新增連結服務] 視窗中，執行下列步驟：

    1. 輸入 **SqlServerLinkedService** 作為 [名稱]。
    2. 確認 [透過整合執行階段連線] 中有出現您稍早建立的自我裝載整合執行階段。
    3. 指定 SQL Server 的名稱作為 [伺服器名稱]。 
    4. 在 [資料庫名稱] 欄位中，指定具有 **emp** 資料表的資料庫名稱。 
    5. 選取適當的**驗證類型**，供 Data Factory 用來連線到 SQL Server 資料庫。 
    6. 輸入 [使用者名稱] 和 [密碼]。 如果您需要在使用者帳戶或伺服器名稱中使用反斜線 (\\)，請在它的前面加上逸出字元 (\\)。 例如，使用 mydomain\\\\myuser。 
    7. 按一下 [測試連線]。 執行此步驟，確認 Data Factory 服務可以使用您建立的自我裝載整合執行階段，連線到 SQL Server 資料庫。 
    8. 若要儲存連結服務，按一下 [儲存]。

       ![SQL Server 連結服務 - 設定](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. 您應該會回到視窗，且此時來源資料集是開啟狀態。 在 [屬性] 視窗的 [連線] 中，執行下列步驟： 

    1. 確認您在 [連結服務] 中看到的是 **SqlServerLinkedService**。 
    2. 針對 [資料表] 選取 [[dbo].[emp]]。

        ![來源資料集 - 連線資訊](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. 切換到 SQLServerToBlobPipeline 的索引標籤 (或) 在樹狀檢視中按一下 [SQLServerToBlobPipeline]。 

    ![管線索引標籤](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. 在 [屬性] 視窗中切換至 [接收] 索引標籤，然後按一下 [+ 新增]。 

    ![接收資料集 - 新增按鈕](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. 在 [新增資料集] 視窗中選取 [Azure Blob 儲存體]，然後按一下 [完成]。 您會看到系統為該資料集開啟了新的索引標籤。 你也會在樹狀檢視中看到該資料集。 

    ![選取 Azure Blob 儲存體](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. 輸入 [AzureBlobDataset] 作為 [名稱]。

    ![接收資料集 - 名稱](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. 在 [屬性] 視窗中切換至 [連線] 索引標籤，針對 [連結服務] 按一下 [+ 新增]。 

    ![新增連結服務 - 按鈕](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. 在 [新增連結服務] 視窗中，執行下列步驟：

    1. 輸入 **AzureStorageLinkedService** 作為 [名稱]。
    2. 針對 [儲存體帳戶名稱] 選取您的 Azure 儲存體帳戶。 
    3. 按一下 [測試連線] 來測試您的 Azure 儲存體帳戶連線。
    4. 按一下 [檔案] 。

        ![Azure 儲存體連結服務 - 設定](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  您應該會回到視窗，且此時接收資料集是開啟狀態。 在 [連線] 索引標籤中，執行下列步驟： 

        1. 確認已為 [連結服務] 選取 [AzureStorageLinkedService]。
        2. 在 [檔案路徑] 的**資料夾**部分中，輸入 **adftutorial/fromonprem**。 如果在 adftutorial 容器中輸出資料夾不存在，Data Factory 服務會自動建立該輸出資料夾。
        3. 在 [檔案路徑] 的**檔案名稱**部分中，輸入 `@CONCAT(pipeline().RunId, '.txt')`。

            ![接收資料集 - 連線](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. 切換至已開啟管線的索引標籤 (或) 按一下 [樹狀檢視] 中的 [管線]。 確認已為 [接收資料集] 選取 [AzureBlobDataset]。 

    ![所選取的接收資料集 ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. 若要驗證管線設定，按一下管線工具列上的 [驗證]。 按一下右上角的 [X] 來關閉 [管道驗證報告]。 

    ![驗證管線](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. 按一下 [發佈]，將您建立的實體發佈至 Azure Data Factory 服務。

    ![發佈按鈕](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. 請等候，直到您看見 [發佈成功] 快顯訊息。 您也可以按一下左側的 [顯示通知] 連結來檢查發佈狀態。 按一下 [X] 來關閉通知視窗。 

    ![發佈成功](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>觸發管線執行
按一下管線工具列上的 [觸發]，然後按一下 [立即觸發]。

![立即觸發](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>監視管道執行

1. 切換至 [監視] 索引標籤。您會看到您在上一個步驟中手動觸發的管線。 

    ![管線執行](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. 若要檢視與此管線執行相關聯的活動執行，按一下 [動作] 資料行中的 [檢視活動執行] 連結。 您只會看到活動執行，因為該管線中只有一個活動。 若要看與關於複製作業的詳細資料，請按一下 [動作] 資料行中的 [詳細資料] 連結 (眼鏡圖示)。 您可以按一下頂端的 [管線]，來切換回到管線執行檢視。

    ![活動執行](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>驗證輸出
管道會自動在 `adftutorial` Blob 容器中建立名為 fromonprem 的輸出資料夾。 確認您在輸出資料夾中看到 *dbo.emp.txt* 檔案。 

1. 在 Azure 入口網站的 [adftutorial] 容器視窗中，選取 [重新整理] 可查看輸出資料夾。

    ![已建立輸出資料夾](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. 在資料夾清單中選取 `fromonprem`。 
3. 確認您看到名為 `dbo.emp.txt` 的檔案。

    ![輸出檔案](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>後續步驟
此範例中的管線會在 Azure Blob 儲存體中的不同位置之間複製資料。 您已了解如何︰

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立自我裝載整合執行階段。
> * 建立 SQL Server 和 Azure 儲存體連結的服務。 
> * 建立 SQL Server 和 Azure Blob 資料集。
> * 建立具有複製活動的管線來移動資料。
> * 啟動管線執行。
> * 監視管道執行。

如需 Data Factory 所支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

若要了解如何將大量資料從來源複製到目的地，請繼續進行以下教學課程：

> [!div class="nextstepaction"]
>[複製大量資料](tutorial-bulk-copy-portal.md)
