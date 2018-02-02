---
title: "使用 Azure Data Factory 將資料載入 Azure Data Lake Store 中 | Microsoft Docs"
description: "使用 Azure Data Factory 將資料複製到 Azure Data Lake Store 中"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>使用 Azure Data Factory 將資料載入 Azure Data Lake Store 中

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 是容納巨量資料分析工作負載的企業級超大規模存放庫。 Azure Data Lake 可讓您在單一位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

Azure Data Factory 是完全受控的雲端架構資料整合服務，可用來將現有系統的資料填入 Lake，並在您建置分析解決方案時，為您節省寶貴的時間。 以下是使用 Azure Data Factory 將資料載入 Azure Data Lake Store 中的主要優點：

* **容易設定**：不需要編寫指令碼的 5 步驟直覺式精靈。
* **豐富的資料存放區支援**︰一組豐富內部部署和雲端架構資料存放區的內部支援，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)資料表中的詳細清單。
* **安全並符合標準**：資料是透過 HTTPS 或 ExpressRoute 傳輸，並且具有全域服務可確保資料絕不會離開地理界限
* **高效能**：將資料載入 Azure Data Lake Store 中的速度高達 1 GBps。 請參閱[複製活動效能](copy-activity-performance.md)以了解詳細資料。

本文將示範如何使用 Data Factory 複製資料工具，**將資料從 Amazon S3 載入 Azure Data Lake Store 中**。 您可以依照類似的步驟，從其他類型的資料存放區複製資料。

> [!NOTE]
>  如需關於 Data Factory 在 Azure Data Lake Store 中來回複製資料功能的一般資訊，請參閱[使用 Azure Data Factory 將資料複製到 Azure Data Lake Store 或從該處複製資料](connector-azure-data-lake-store.md)一文。
>
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。
* **Azure Data Lake Store**。 如果您沒有 Data Lake Store 帳戶，請參閱[建立 Data Lake Store 帳戶](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)一文，以了解建立步驟。
* **Amazon S3**。 本文示範如何從 Amazon S3 複製資料。 您可以依照類似的步驟來使用其他資料存放區。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 按一下左側功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，提供如以下螢幕擷取畫面所示的值： 
      
     ![新增資料處理站頁面](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **名稱。** 輸入資料處理站的全域唯一名稱。 如果您收到錯誤，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
            `Data factory name "LoadADLSDemo" is not available`

    * **訂用帳戶。** 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
    * **資源群組。** 從下拉式清單中選取現有的資源群組，或選取 [新建] 選項，然後輸入資源群組的名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本。** 選取 [V2 (預覽)]。
    * **位置。** 選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (Azure Data Lake Store、Azure 儲存體、Azure SQL Database 等) 可位於其他位置/區域。

3. 按一下頁面底部的 [新增] 。
4. 建立完成之後，請前往資料處理站，您就會看到如圖中所示的 [Data Factory] 頁面。 按一下 [編寫與監視] 圖格，以在另一個索引標籤中啟動資料整合應用程式。 
   
   ![Data Factory 首頁](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>將資料載入 Azure Data Lake Store 中

1. 在 [開始使用] 頁面中，按一下 [複製資料] 圖格以啟動複製資料工具。 

   ![複製資料工具圖格](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. 在複製資料工具的 [屬性] 頁面中，指定 [CopyFromAmazonS3ToADLS] 作為 [工作名稱]，然後按 [下一步]。 

    ![複製資料工具 - 屬性頁面](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] 頁面中，選取 [Amazon S3]，然後按 [下一步]。

    ![來源資料存放區頁面](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. 在 [指定 Amazon S3 連線] 頁面中，執行下列步驟： 
    1. 指定 [存取金鑰識別碼]。
    2. 指定 [祕密存取金鑰]。
    3. 按 [下一步] 。 

        ![指定 Amazon S3 帳戶](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. 在 [選擇輸入檔案或資料夾] 頁面中，瀏覽至您要複製過去的資料夾/檔案，選取並按一下 [選擇]，然後按 [下一步]。 

    ![選擇輸入檔案或資料夾](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. 在 [目的地資料存放區] 頁面上，選取 [Azure Data Lake Store]，然後按 [下一步]。 

    ![目的地資料存放區頁面](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. 在此頁面中，核取 [以遞迴方式複製檔案] 和 [二進位複製] (依原樣複製檔案) 選項來選擇複製行為。 按 [下一步] 。

    ![指定輸出資料夾](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. 在 [指定 Data Lake Store 連線] 頁面中，執行下列步驟： 

    1. 選取您的 Data Lake Store 作為 [Data Lake Store 帳戶名稱]。
    2. 指定服務主體資訊，包括 [租用戶]、[服務主體識別碼] 和 [服務主體金鑰]。
    3. 按 [下一步] 。 

    > [!IMPORTANT]
    > 在本逐步解說中，您會使用**服務主體**來驗證 Data Lake Store。 請遵循[這裡](connector-azure-data-lake-store.md#using-service-principal-authentication)的指示，確定您已對服務主體 授與 Azure Data Lake Store 中的適當權限。

    ![指定 Azure Data Lake Store 帳戶](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. 在 [選擇輸出檔案或資料夾] 頁面中，指定 [copyfroms3]，然後按 [下一步]。 

    ![指定輸出資料夾](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. 在 [設定] 頁面上，按 [下一步]。 

    ![設定頁面](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. 在 [摘要] 頁面中，檢閱設定，然後按 [下一步]。

    ![摘要頁面](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. 在 [部署] 頁面上，按一下 [監視] 來監視管線 (工作)。

    ![部署頁面](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 請注意，系統會自動選取左側的 [監視] 索引標籤。 您會在 [動作] 資料行中看到有連結可供檢視活動執行詳細資料，以及可供重新執行管線。 

    ![監視管線回合](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. 若要檢視與此管線執行相關聯的活動執行，按一下 [動作] 資料行中的 [檢視活動執行] 連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回 [管線執行] 檢視，按一下頂端的 [管線] 連結。 按一下 [重新整理] 即可重新整理清單。 

    ![監視活動回合](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. 您可以按一下活動監控檢視中 [動作] 底下的 [詳細資料] 連結，以進一步監視每個複製活動的執行詳細資料。 其中顯示的資訊包括從來源複製到接收端的資料量、輸送量、複製活動進行的步驟以及對應的持續期間和所使用的設定。

    ![監視活動執行詳細資料](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. 確認資料已複製到 Azure Data Lake Store 中。 

    ![確認 Data Lake Store 輸出](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>後續步驟

前往下列文章，以了解 Azure Data Lake Store 支援： 

> [!div class="nextstepaction"]
>[Azure Data Lake Store 連接器](connector-azure-data-lake-store.md)