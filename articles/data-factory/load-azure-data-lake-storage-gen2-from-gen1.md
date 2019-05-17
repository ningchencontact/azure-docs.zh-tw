---
title: 使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2
description: 使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560647"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2

Azure Data Lake Storage Gen2 是一組巨量資料分析的專屬功能，內建於 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。 此功能可讓您使用檔案系統和物件儲存範例連接您的資料。

如果您目前使用 Azure Data Lake Storage Gen1，您可以藉由使用 Azure Data Factory 將資料從 Data Lake Storage Gen1 複製到 Gen2 來評估 Gen2 的新功能。

Azure Data Factory 是完全受控的雲端式資料整合服務。 您可以使用此服務，在建置分析解決方案時，於 Lake 中置入來自豐富的內部部署集合和雲端式資料存放區的資料，並節省時間。 如需受支援連接器的詳細清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)資料表。

Azure Data Factory 提供可向外延展的受控資料移動解決方案。 由於 ADF 具有相應放大架構，因此能以高輸送量來內嵌資料。 如需詳細資料，請參閱[複製活動效能](copy-activity-performance.md)。

本文示範如何使用 Data Factory 複製資料工具，將資料從 Azure Data Lake Storage Gen1 複製到 Azure Data Lake Storage Gen2。 您可以依照類似的步驟，從其他類型的資料存放區複製資料。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* 內有資料的 Azure Data Lake Storage Gen1 帳戶。
* 啟用 Data Lake Storage Gen2 的 Azure 儲存體帳戶：如果您沒有儲存體帳戶[建立帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表上，選取 [建立資源] > [資料 + 分析] > [資料處理站]：
   
   ![在 [新增] 窗格中選取資料處理站](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 [新增資料處理站] 頁面中，為下圖所示的欄位提供值： 
      
   ![新增資料處理站頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名稱**：輸入 Azure 資料處理站的全域唯一名稱。 如果您收到「資料處理站名稱 \"LoadADLSDemo\" 無法使用」的錯誤，請為資料處理站輸入其他名稱。 例如，您可以使用_**您的名稱**_**ADFTutorialDataFactory**。 請嘗試再次建立資料處理站。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
    * 訂用帳戶：選取用來在其中建立資料處理站的 Azure 訂用帳戶。 
    * **資源群組**：從下拉式清單中選取現有的資源群組，或選取 [新建] 選項，然後輸入資源群組的名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：選取 [V2]。
    * **位置**：選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區可位於其他位置和區域。 

3. 選取 [建立] 。
4. 建立完成後，請移至資料處理站。 您會看到如下圖所示的 [Data Factory] 首頁： 
   
   ![Data Factory 首頁](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   選取 [編寫與監視] 圖格，以在另一個索引標籤中啟動資料整合應用程式。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>將資料載入 Azure Data Lake Storage Gen2 中

1. 在 [開始使用] 頁面中，選取 [複製資料] 圖格以啟動複製資料工具： 

   ![複製資料工具圖格](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 在 [屬性] 頁面中，對 [工作名稱] 欄位指定 [CopyFromADLSGen1ToGen2]，然後選取 [下一步]：

    ![屬性頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] 頁面中，按一下 [+ Create new connection] \(+ 建立新連線\)：

    ![來源資料存放區頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    從連接器資源庫選取 [Azure Data Lake Storage Gen1]，然後選取 [繼續]
    
    ![來源資料存放區 Azure Data Lake Storage Gen1 頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. 在 [指定 Azure Data Lake Storage Gen1 連線] 頁面中，執行下列步驟：
   1. 選取 [Data Lake Storage Gen1] 作為帳戶名稱，並指定或驗證 [租用戶]。
   2. 按一下 [測試連線] 以驗證設定，然後選取 [完成]。
   3. 您會看到新的連線隨即建立。 選取 [下一步] 。
   
   > [!IMPORTANT]
   > 在此逐步解說中，您會使用 Azure 資源的受控識別來驗證您的 Data Lake Storage Gen1。 請務必遵循[這些指示](connector-azure-data-lake-store.md#managed-identity)，以對 MSI 授與 Azure Data Lake Storage Gen1 中的適當權限。
   
   ![指定 Azure Data Lake Storage Gen1 帳戶](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. 在 [選擇輸入檔案或資料夾] 頁面中，瀏覽至您要複製過去的資料夾和檔案。 選取資料夾/檔案，選取 [選擇]：

    ![選擇輸入檔案或資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. 勾選 [以遞迴方式複製檔案] 和 [二進位複製] 選項來選取複製行為。 選取 [下一步]：

    ![指定輸出資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. 在 [目的地資料存放區] 頁面中，按一下 [+ 建立新連線]，然後選取 [Azure Data Lake Storage Gen2]，並選取 [繼續]：

    ![目的地資料存放區頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. 在 [指定 Azure Data Lake Storage Gen2 連線] 頁面中，執行下列步驟：

   1. 從 [儲存體帳戶名稱] 下拉式清單中選取您的 Data Lake Storage Gen2 可使用帳戶。
   2. 選取 [完成] 以建立連線。 然後，選取 [下一步]。
   
   ![指定 Azure Data Lake Storage Gen2 帳戶](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. 在 [**選擇輸出檔案或資料夾**頁面上，輸入**copyfromadlsgen1**作為輸出資料夾名稱，然後選取**下一步]**。 ADF 會建立子資料夾與對應的 ADLS Gen2 檔案系統複製期間，如果不存在。

    ![指定輸出資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. 在 [設定] 頁面中選取 [下一步]，以使用預設設定。

11. 在 [摘要] 頁面中檢閱設定，然後選取 [下一步]：

    ![摘要頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. 在 [部署] 頁面中選取 [監視] 來監視管線：

    ![部署頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. 請注意，系統會自動選取左側的 [監視] 索引標籤。 [動作] 資料行中會有連結可供檢視活動執行詳細資料，以及重新執行管線：

    ![監視管線回合](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作] 資料行中的 [檢視活動執行] 連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回 [管線執行] 檢視，請選取頂端的 [管線] 連結。 選取 [重新整理] 即可重新整理清單。 

    ![監視活動回合](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. 若要監視每個複製活動的執行詳細資料，請選取活動監控檢視中 [動作] 底下的 [詳細資料] 連結 (眼鏡圖示)。 您可以監視的詳細資料包括從來源複製到接收的資料量、資料輸送量、執行步驟與對應的持續期間，以及所使用的設定：

    ![監視活動執行詳細資料](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. 確認資料已複製到 Data Lake Storage Gen2 帳戶中。

## <a name="best-practices"></a>最佳做法

若要評估升級從 Azure Data Lake 儲存體 (ADLS) Gen1 至 Gen2 一般情況下，請參閱[從 Azure Data Lake 儲存體 Gen1 的巨量資料分析解決方案升級至 Azure Data Lake 儲存體 Gen2](../storage/blobs/data-lake-storage-upgrade.md)。 下列各節會介紹使用 ADF 從 Gen1 的資料升級至 Gen2 的最佳作法。

### <a name="data-partition-for-historical-data-copy"></a>資料分割的歷程記錄資料的複本

- 如果在 ADLS Gen1 中的總資料大小小於**30 TB**的檔案數目，而且小於**1 百萬個**，您可以執行的單一複製活動中複製所有的資料。
- 如果您有要複製的資料的大小時，或您想管理批次中的資料移轉並使每個 windows 特定的時間內完成的彈性，建議您的資料分割，在此情況下它也會降低任何非預期的 iss 的風險ue。

強烈建議 PoC （概念證明），以驗證端對端解決方案，並測試您的環境中的複製輸送量。 主要的 PoC 步驟： 

1. 使用單一複製活動從 ADLS Gen1 將數 Tb 的資料複製到 ADLS Gen2 以取得複製效能基準線，開始建立一個 ADF 管線[資料整合單位 (DIUs)](copy-activity-performance.md#data-integration-units)為 128。 
2. 根據您在步驟 1 中取得的複製輸送量，計算整個資料移轉所需的預估的時間。 
3. （選擇性）建立一個控制資料表，並定義檔案篩選器來分割要移轉的檔案。 讓您分割的檔案，依照下列方式： 

    - 萬用字元篩選條件 （建議） 使用分割的資料夾名稱或資料夾名稱 
    - 分割檔案的上次修改時間 

### <a name="network-bandwidth-and-storage-io"></a>網路頻寬和儲存體 I/O 

以便您可以管理儲存體 I/O 上的使用量，以便在移轉期間不會影響 ADLS Gen1 的一般商務工作，您可以控制從 ADLS Gen1 讀取資料，並將資料寫入至 ADLS Gen2 ADF 複製作業的並行存取。

### <a name="permissions"></a>權限 

在 Data Factory [ADLS Gen1 連接器](connector-azure-data-lake-store.md)服務主體和受管理的身分識別支援 Azure 資源驗證;[ADLS Gen2 連接器](connector-azure-data-lake-storage.md)支援帳戶金鑰，服務主體，並管理 Azure 資源驗證身分識別。 若要能夠瀏覽的 Data Factory 和複製所有檔案/Acl 視需要都請確定您授與最高之帳戶的權限不足您提供給存取/讀取/寫入所有的檔案，並設定 Acl，如果您選擇。 若要授與其做 super-使用者/擁有者角色，在移轉期間的建議。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留從 Data Lake 儲存體 Gen1 的 Acl

如果您想要複寫的 Acl，以及資料檔案，從 Data Lake 儲存體 Gen1 升級至 Gen2 時，請參閱[保留的 Acl，從 Data Lake 儲存體 Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。 

### <a name="incremental-copy"></a>增量複製 

數種方法可用來從 ADLS Gen1 載入只將新的或更新檔案：

- 載入新的或更新的檔案依時間分割資料夾或檔案名稱，例如/2019年/05/13 / *;
- 載入新的或更新檔案： 依 LastModifiedDate;
- 任何第 3 方工具/解決方案，來識別新的或更新的檔案，然後將 ADF 管線中的檔案或資料夾名稱，透過參數或資料表/檔案。  

若要執行累加式載入適當的頻率取決於 ADLS Gen1 中的檔案總數以及要載入每次新的或更新檔案的磁碟區。  

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [複製活動概觀](copy-activity-overview.md)
> [Azure Data Lake 儲存體 Gen1 連接器](connector-azure-data-lake-store.md)
> [Azure Data Lake 儲存體 Gen2 連接器](connector-azure-data-lake-storage.md)