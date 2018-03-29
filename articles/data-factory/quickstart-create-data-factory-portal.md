---
title: 使用 Azure Data Factory UI 建立 Azure 資料處理站 | Microsoft Docs
description: 使用會將資料從 Azure Blob 儲存體中的某個位置複製到另一個位置的管線建立資料處理站。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 02/01/2018
ms.author: jingwang
ms.openlocfilehash: ea70d6053f12c33d9c3ff4c69f22a41130c783e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-data-factory-by-using-the-azure-data-factory-ui"></a>使用 Azure Data Factory UI 建立資料處理站
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [第 2 版 - 預覽](quickstart-create-data-factory-portal.md)

本快速入門說明如何使用 Azure Data Factory UI 建立及監視資料處理站。 在此資料處理站中建立的管線會將資料從 Azure Blob 儲存體中的一個資料夾「複製」到其他資料夾。 如需如何使用 Azure Data Factory「轉換」資料的教學課程，請參閱[教學課程︰使用 Spark 轉換資料](tutorial-transform-data-spark-portal.md)。 


> [!NOTE]
> 如果您不熟悉 Azure Data Factory，在執行此快速入門之前，請先參閱 [Azure Data Factory 簡介](data-factory-introduction.md)。 
>
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的服務 (也就是正式推出版 (GA))，請參閱 [Data Factory 第 1 版教學課程](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>影片 
觀賞這段影片，可協助您瞭解 Data Factory UI： 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
2. 移至 [Azure 入口網站](https://portal.azure.com)。 
3. 選取左側功能表上的 [新增]、[資料 + 分析]，然後選取 [資料處理站]。 
   
   ![在 [新增] 窗格中選取資料處理站](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面上，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
   ![[新增資料處理站] 頁面](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 *全域唯一的*。 如果您看到下列錯誤，請變更資料處理站的名稱 (例如 **&lt;yourname&gt;ADFTutorialDataFactory**)，然後試著重新建立。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory - 命名規則](naming-rules.md)一文。
  
   ![名稱無法使用時的錯誤](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. 針對 [訂用帳戶]，選取您要用來建立資料處理站的 Azure 訂用帳戶。 
4. 針對 [資源群組]，使用下列其中一個步驟︰
     
   - 選取 [使用現有的]，然後從清單中選取現有的資源群組。 
   - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
   若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 針對 [版本]，選取 [V2 (預覽版)]。
5. 針對 [位置]，選取資料處理站的位置。

   此清單只會顯示 Data Factory 支援的位置。 Data Factory 所使用的資料存放區 (Azure 儲存體和 Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他位置。
6. 選取 [釘選到儀表板]。     
7. 選取 [建立] 。
8. 在儀表板上，您會看到 [正在部署 Data Factory] 狀態的下列圖格︰ 

   ![[部署 Data Factory] 圖格](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. 建立完成之後，您會看到 [Data Factory] 頁面。 選取 [編寫與監視] 圖格，以在個別的索引標籤上啟動 Azure Data Factory 使用者介面 (UI) 應用程式。
   
   ![資料處理站的首頁，具有 [編寫與監視] 圖格](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. 在 [現在就開始吧] 頁面上，切換至左面板中的 [編輯] 索引標籤。 

    ![[現在就開始吧] 頁面](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>建立連結的服務
在此程序中，您會建立連結服務，將 Azure 儲存體帳戶連結到資料處理站。 連結的服務具有連線資訊，可供 Data Factory 服務在執行階段中用來連線。

1. 選取 [連線]，然後選取工具列上的 [新增] 按鈕。 

   ![用於建立新連線的按鈕](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
2. 在 [新增連結服務] 頁面上，選取 [Azure Blob 儲存體]，然後選取 [繼續]。 

   ![選取 [Azure Blob 儲存體] 圖格](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
3. 完成下列步驟： 

   a. 針對 [名稱]，輸入 **AzureStorageLinkedService**。

   b. 針對 [儲存體帳戶名稱] ，選取您 Azure 儲存體帳戶的名稱。

   c. 選取 [測試連線]，以確認 Data Factory 服務可連線至儲存體帳戶。 

   d. 選取 [儲存] 以儲存連結服務。 

   ![Azure 儲存體連結服務設定](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
4. 確認您在連結服務清單中看到 **AzureStorageLinkedService**。 

   ![清單中的 Azure 儲存體連結服務](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>建立資料集
在此程序中，您會建立兩個資料集：**InputDataset** 和 **OutputDataset**。 這些資料集的類型為 **AzureBlob**。 其會參考您在前一節中建立的 Azure 儲存體連結服務。 

輸入資料集代表輸入資料夾中的來源資料。 在輸入資料集定義中，您可以指定 Blob 容器 (**adftutorial**)、資料夾 (**input**) 以及包含來源資料的檔案 (**emp.txt**)。 

此輸出資料集代表已複製到目的地的資料。 在輸出資料集定義中，您可以指定要將資料複製過去的 Blob 容器 (**adftutorial**)、資料夾 (**output**) 和檔案。 每個管線執行都有其相關聯的唯一識別碼。 您可以使用系統變數 **RunId** 來存取此識別碼。 輸出檔案的名稱會根據管線的執行識別碼進行動態評估。   

在連結服務設定中，您已指定包含來源資料的 Azure 儲存體帳戶。 在來源資料集設定中，您可以指定來源資料確切的所在位置 (Blob 容器、資料夾和檔案)。 在接收資料集設定中，您可以指定要將資料複製過去的位置 (Blob 容器、資料夾和檔案)。 
 
1. 選取 **+** (加號) 按鈕，然後選取 [資料集]。

   ![用於建立資料集的功能表](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. 在 [新增資料集] 頁面上，選取 [Azure Blob 儲存體]，然後選取 [完成]。 

   ![選取 [Azure Blob 儲存體]](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. 在資料集的 [屬性] 視窗中輸入 **InputDataset**，作為 [名稱]。 

   ![資料集一般設定](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. 切換至 [連線] 索引標籤並完成下列步驟： 

   a. 針對 [連結服務]，選取 [AzureStorageLinkedService]。

   b. 針對 [檔案路徑]，選取 [瀏覽] 按鈕。

      ![[連線] 索引標籤和 [瀏覽] 按鈕](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. 在 [選擇檔案或資料夾] 視窗中，瀏覽至 **adftutorial** 容器中的 **input** 資料夾，選取 **emp.txt** 檔案，然後選取 [完成]。

      ![瀏覽輸入檔案](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
   d. (選擇性) 選取 [預覽資料]，以預覽 emp.txt 檔案中的資料。     
5. 重複前述步驟，以建立輸出資料集：  

   a. 選取 **+** (加號) 按鈕，然後選取 [資料集]。

   b. 在 [新增資料集] 頁面上，選取 [Azure Blob 儲存體]，然後選取 [完成]。

   c. 指定 **OutputDataset** 作為名稱。

   d. 輸入 **adftutorial/output** 作為資料夾。 如果 **output** 資料夾不存在，則複製活動會在執行階段進行建立。

   e. 針對檔案名稱輸入 `@CONCAT(pipeline().RunId, '.txt')`。 
   
      每當您執行管線時，管線執行都有其相關聯的唯一識別碼。 運算式會串連管線的執行識別碼與 **.txt**，以評估輸出檔案名稱。 如需支援的系統變數和運算式清單，請參閱[系統變數](control-flow-system-variables.md)和[運算式語言](control-flow-expression-language-functions.md)。

   ![輸出資料集設定](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>建立管線 
在此程序中，您會建立並驗證管線，其中含有使用輸入和輸出資料集的複製活動。 複製活動會將資料從您在輸入資料集設定中指定的檔案，複製到您在輸出資料集設定中指定的檔案。 如果輸入資料集僅指定資料夾 (而不是檔案名稱)，則複製活動會將來源資料夾中的所有檔案複製到目的地。 

1. 選取 **+** (加號) 按鈕，然後選取 [管線]。 

   ![用於建立新管線的功能表](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. 在 [屬性] 視窗中，指定 **CopyPipeline** 作為 [名稱]。 

   ![管線一般設定](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. 在 [活動] 工具箱中，展開 [資料流程]。 將 [活動] 工具箱中的 [複製] 活動拖到管線設計工具介面。 您也可以在 [活動] 工具箱中搜尋活動。 指定 **CopyFromBlobToBlob** 作為 [名稱]。

   ![複製活動一般設定](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. 在複製活動設定中切換至 [來源] 索引標籤，然後選取 **InputDataset** 作為 [來源資料集]。

   ![複製活動來源設定](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. 在複製活動設定中切換至 [接收] 索引標籤，然後選取 **OutputDataset** 作為 [接收資料集]。

   ![複製活動接收設定](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. 選取 [驗證] 以驗證管線設定。 確認管線已驗證成功。 若要關閉驗證輸出，請選取 **>>** (向右箭號) 按鈕。 

   ![驗證管線](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>對管線執行測試
在此步驟中，您將在管線部署至 Data Factory 之前對其執行測試。 

1. 在管線的工具列上，選取 [測試執行]。 
    
   ![管線測試執行](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. 確認您在管線設定的 [輸出] 索引標籤上，可檢視管線執行的狀態。 

   ![測試執行輸出](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. 確認您在 **adftutorial** 容器的 **output** 資料夾中看到輸出檔案。 如果 output 資料夾不存在，Data Factory 服務會自動加以建立。 
    
   ![驗證輸出](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>手動觸發管線
在此程序中，您會將實體 (連結服務、資料集和管線) 部署至 Azure Data Factory。 然後，您會手動觸發管線執行。 您也可以將實體發佈至自己的 Visual Studio Team Services Git 存放庫，相關說明請見[另一個教學課程](tutorial-copy-data-portal.md?#configure-code-repository)。

1. 觸發管線之前，您必須將實體發佈至 Data Factory。 若要發佈，請選取左窗格中的 [全部發佈]。 

   ![發佈按鈕](./media/quickstart-create-data-factory-portal/publish-button.png)
2. 若要手動觸發管線，請選取工具列上的 [觸發程序]，然後選取 [立即觸發]。 
    
   ![[立即觸發] 命令](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>監視管線

1. 切換至左側的 [監視] 索引標籤。 使用 [重新整理] 按鈕可重新整理清單。

   ![用來監視管線執行的索引標籤，具有 [重新整理] 按鈕](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. 選取 [動作] 下方的 [檢視活動執行] 連結。 在此頁面上，您會看到複製活動執行的狀態。 

   ![管線活動執行](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. 若要檢視關於複製作業的詳細資訊，請選取 [動作] 資料行中的 [詳細資料] (眼鏡圖片) 連結。 如需屬性的詳細資訊，請參閱[複製活動概觀](copy-activity-overview.md)。 

   ![複製作業詳細資料](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. 確認您在 **output** 資料夾中看到新的檔案。 
5. 您可以選取 [管線] 連結，從 [活動執行] 檢視切換回 [管線執行] 檢視。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>觸發排程上的管線
此程序是本教學課程的選擇性程序。 您可以建立*排程器觸發程序*，將管線排定為定期執行 (每小時、每天等)。 在此程序中，您會建立一個觸發程序，在您指定的結束日期和日期以前，該觸發程序會每分鐘執行一次。 

1. 切換至 [編輯] 索引標籤。 

   ![[編輯] 按鈕](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. 選取功能表上的 [觸發程序]，然後選取 [新增/編輯]。 

   ![新觸發程序的功能表](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. 在 [新增觸發程序] 頁面上，選取 [選擇觸發程序]，然後選取 [新增]。 

   ![用於新增觸發程序的選取項目](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. 在 [新增觸發程序] 頁面上，在 [結束] 之下選取 [日期]，將結束時間指定為目前時間之後的幾分鐘，然後選取 [套用]。 

   每次執行管線都會產生相關成本，因此，請將結束時間指定為開始時間的數分鐘之後。 請確定兩者是在同一天。 不過，請確定發佈時間與結束時間之間有足夠的時間可供管線執行。 在您將方案發佈至 Data Factory 之後，觸發程序才會生效，而不是在您將觸發程序儲存在 UI 時生效。 

   ![觸發程序設定](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. 在 [新增觸發程序] 頁面上，選取 [已啟動] 核取方塊，然後選取 [下一步]。 

   ![[已啟動] 核取方塊和 [下一步] 按鈕](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. 檢閱警告訊息，然後選取 [完成]。

   ![警告和 [完成] 按鈕](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. 選取 [全部發佈]，將變更發佈至 Data Factory。 

   ![發佈按鈕](./media/quickstart-create-data-factory-portal/publish-button.png)
8. 切換至左側的 [監視] 索引標籤。 選取 [重新整理] 即可重新整理清單。 在發佈時間到結束時間之間，您會看到管線每隔一分鐘執行一次。 

   請留意 [觸發方式] 資料行中的值。 手動觸發程序執行來自於您先前完成的步驟 (**立即觸發**)。 

   ![已觸發的執行清單](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. 選取 [管線執行] 旁的向下箭號，切換至 [觸發程序執行] 檢視。 

   ![切換至 [觸發程序執行] 檢視](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. 確認在指定的結束日期和時間之前，每次執行管線時都會在 **output** 資料夾中建立一個輸出檔案。 

## <a name="next-steps"></a>後續步驟
此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 若想了解使用 Data Factory 的更多案例，請瀏覽[教學課程](tutorial-copy-data-portal.md)。 