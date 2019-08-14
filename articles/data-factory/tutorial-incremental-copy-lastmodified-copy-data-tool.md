---
title: 使用資料複製工具, 根據 LastModifiedDate, 以累加方式複製新的和已變更的檔案 |Microsoft Docs
description: 建立 Azure data factory, 然後使用 [資料複製] 工具, 以累加方式根據 LastModifiedDate 載入新檔案。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 9f6fd57586603d0d987faa674d40a7e4678530a1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933868"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>使用資料複製工具, 根據 LastModifiedDate 以累加方式複製新的和變更的檔案

在本教學課程中, 您將使用 Azure 入口網站來建立資料處理站。 然後, 您將使用資料複製工具來建立管線, 根據其從 Azure Blob 儲存體**LastModifiedDate**至 azure blob 儲存體, 以累加方式複製新的和已變更的檔案。

藉由執行此動作, ADF 會掃描來源存放區的所有檔案, 並依其 LastModifiedDate 套用檔案篩選器, 並只在上次到達目的地存放區之後, 才複製新的和更新的檔案。  請注意, 如果您讓 ADF 掃描大量的檔案, 但是只將幾個檔案複製到目的地, 您仍然會預期長時間, 因為檔案掃描也非常耗時。   

> [!NOTE]
> 如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。

在本教學課程中, 您將執行下列工作:

> [!div class="checklist"]
> * 建立資料處理站。
> * 使用複製資料工具建立管線。
> * 監視管線和活動執行。

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* **Azure 儲存體帳戶**：使用 Blob 儲存體作為_來源_和_接收_資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-quickstart-create-account.md)中的指示。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 儲存體中建立兩個容器

執行下列步驟, 為教學課程準備您的 Blob 儲存體。

1. 建立名為**source**的容器。 您可以使用各種工具來執行這項工作, 例如[Azure 儲存體總管](https://storageexplorer.com/)。

2. 建立名為**destination**的容器。 

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表上，選取 [建立資源] > [資料 + 分析] > [資料處理站]： 
   
   ![在 [新增] 窗格中選取資料處理站](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站] 頁面的 [名稱] 下，輸入 **ADFTutorialDataFactory**。 
 
   資料處理站的名稱必須是「全域唯一」的名稱。 您可能會收到下列錯誤訊息：
   
   ![新增資料處理站錯誤訊息](./media/doc-common-process/name-not-available-error.png)

   如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 例如，使用**您的名稱** **ADFTutorialDataFactory**。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
3. 選取您要在其中建立新資料處理站的 Azure**訂**用帳戶。 
4. 針對 [資源群組]，採取下列其中一個步驟︰
     
    * 選取 [使用現有的]，然後從下拉式清單選取現有的資源群組。

    * 選取 [建立新的]，然後輸入資源群組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。

5. 在 [**版本**] 底下, 選取 [ **V2**]。
6. 在 [位置] 下，選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 您的資料處理站所使用的資料存放區 (例如, Azure 儲存體和 SQL Database) 和計算 (例如 Azure HDInsight) 可位於其他位置和區域中。
7. 選取 [釘選到儀表板]。 
8. 選取 [建立]。
9. 在儀表板上, 請參閱 [**部署 Data Factory** ] 磚, 以查看處理狀態。

    ![部署 Data Factory 磚](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 建立完成後，隨即會顯示 **Data Factory** 首頁。
   
    ![Data Factory 首頁](./media/doc-common-process/data-factory-home-page.png)
11. 若要在不同的索引標籤上開啟 Azure Data Factory 使用者介面 (UI), 請選取 [**作者 & 監視器**] 磚。 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用複製資料工具建立管線

1. 在 [**現在就開始**吧] 頁面上, 選取 [**資料複製**] 標題以開啟 [資料複製] 工具。 

   ![複製資料工具圖格](./media/doc-common-process/get-started-page.png)
   
2. 在 [**屬性**] 頁面上, 採取下列步驟:

    a. 在 [工作**名稱**] 下, 輸入**DeltaCopyFromBlobPipeline**。

    b. 在 [工作**步調**] 或 [工作**排程**] 底下, 選取 [依**排程定期執行**]。

    c. 在 [**觸發程式類型**] 底下, 選取 [**輪轉視窗]** 。
    
    d. 在 **[週期**] 底下, 輸入**15 分鐘**。 
    
    e. 選取 [下一步]。 
    
    Data Factory 使用者介面會使用指定的工作名稱建立管線。 

    ![屬性頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. 在 [來源資料存放區] 頁面上，完成下列步驟：

    a. 選取 [ **+ 建立新**連線] 以新增連接。
    
    ![來源資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. 從資源庫選取 [Azure Blob 儲存體]，然後選取 [繼續]。
    
    ![來源資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. 在 [**新增連結服務**] 頁面上, 從 [**儲存體帳戶名稱**] 清單中選取您的儲存體帳戶, 然後選取 **[完成]** 。
    
    ![來源資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. 選取新建立的連結服務, 然後選取 **[下一步]** 。 
    
   ![來源資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. 在 [選擇輸入檔案或資料夾] 頁面上，完成下列步驟︰
    
    a. 流覽並選取 [**源**] 資料夾, 然後選取 [**選擇**]。
    
    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. 在 [檔案**載入行為**] **底下, 選取 [累加式載入]:LastModifiedDate**。
    
    ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. 檢查**二進位複製**並選取 **[下一步]** 。
    
     ![選擇輸入檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. 在 [**目的地資料存放區**] 頁面上, 選取 [ **AzureBlobStorage**]。 這是與來源資料存放區相同的儲存體帳戶。 然後，選取 [下一步]。

    ![目的地資料存放區頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. 在 [選擇輸出檔案或資料夾] 頁面上，完成下列步驟︰
    
    a. 流覽並選取 [**目的地**] 資料夾, 然後選取 **[選擇**]。
    
    ![選擇輸出檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. 選取 [下一步]。
    
     ![選擇輸出檔案或資料夾](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. 在 [設定] 頁面上，選取 [下一步]。 

    ![設定頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. 在 [**摘要**] 頁面上, 檢查設定, 然後選取 **[下一步]** 。

    ![摘要頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. 在**部署頁面**上選取 [監視] 來監視管線 (工作)。

    ![部署頁面](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. 請注意，系統會自動選取左側的 [監視] 索引標籤。 [動作] 資料行中會有連結可供檢視活動執行詳細資料，以及重新執行管線。 選取 [重新整理] 以重新整理清單, 然後選取 [**動作**] 資料行中的 [ **View Activity 執行**] 連結。 

    ![重新整理清單並選取 [觀看活動執行]](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. 管線中只有一個活動 (複製活動), 因此您只會看到一個專案。 如需關於複製作業的詳細資料，請選取 [動作] 資料行中的 [詳細資料] 連結 (眼鏡圖示)。 

    ![複製活動在管線中](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    因為 Blob 儲存體帳戶的**來源**容器中沒有任何檔案, 所以您不會看到任何複製到 blob 儲存體帳戶中**目的地**容器的檔案。
    
    ![來源容器或目的地容器中沒有檔案](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. 建立空的文字檔, 並將其命名為**file1 .txt**。 將此文字檔上傳至儲存體帳戶中的**來源**容器。 您可以使用各種工具來執行這些工作，例如 [Azure 儲存體總管](https://storageexplorer.com/)。   

    ![建立 file1 .txt 並上傳至來源容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. 若要返回 [**管線執行**] 視圖, 請選取 [**所有管線執行**], 並等候相同的管線自動觸發。  

    ![選取所有管線執行](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. 當您看到第二個管線執行時, 選取 [**查看活動執行**]。 然後以您對第一個管線執行的相同方式來檢查詳細資料。  

    ![選取 [查看活動執行] 和 [查看詳細資料]](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    您會看到一個檔案 (file1 .txt) 已從**來源**容器複製到 Blob 儲存體帳戶的**目的地**容器。
    
    ![File1 已從來源容器複製到目的地容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. 建立另一個空的文字檔, 並將它命名為**file2 .txt**。 將此文字檔上傳至 Blob 儲存體帳戶中的**來源**容器。   
    
16. 針對第二個文字檔重複步驟13和14。 在下一個管線執行中, 您會看到只有新的檔案 (file2) 已從**來源**容器複製到儲存體帳戶的**目的地**容器。  
    
    ![已將 File2 從來源容器複製到目的地容器](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    您也可以使用[Azure 儲存體總管](https://storageexplorer.com/)掃描檔案來確認此項。
    
    ![使用 Azure 儲存體總管掃描檔案](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>後續步驟
請繼續進行下列教學課程, 以瞭解如何使用 Azure 上的 Apache Spark 叢集來轉換資料:

> [!div class="nextstepaction"]
>[使用 Apache Spark 叢集來轉換雲端中的資料](tutorial-transform-data-spark-portal.md)