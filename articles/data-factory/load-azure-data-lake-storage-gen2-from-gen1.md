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
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068982"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2

Azure Data Lake 儲存體 Gen2 是一組的功能已內建的巨量資料分析的專用[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。 您可以使用它來與您的資料使用這兩個檔案系統 」 和 「 物件儲存體的範例。

如果您目前使用 Azure Data Lake 儲存體 Gen1，您可以使用 Azure Data Factory 從 Data Lake 儲存體 Gen1 複製資料至 Gen2 來評估 Azure Data Lake 儲存體 Gen2。

Azure Data Factory 是完全受控的雲端式資料整合服務。 您可以使用服務來填入 lake 來自一組豐富內部部署資料和雲端為基礎的資料存放區和儲存當您建置分析解決方案的時間。 如需支援的連接器的清單，請參閱的資料表[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

Azure Data Factory 提供可向外延展的受控資料移動解決方案。 因為 Data Factory 的向外延展架構，它可以內嵌在高輸送量的資料。 如需詳細資訊，請參閱 <<c0> [ 複製活動效能](copy-activity-performance.md)。

本文說明如何使用 Data Factory 複製資料工具將資料從 Azure Data Lake 儲存體 Gen1 複製到 Azure Data Lake 儲存體 Gen2。 您可以依照類似的步驟，從其他類型的資料存放區複製資料。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* 內有資料的 Azure Data Lake Storage Gen1 帳戶。
* Data Lake 儲存體 Gen2 啟用 azure 儲存體帳戶。 如果您沒有儲存體帳戶[建立帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 在左側功能表中，選取**建立資源** > **資料 + 分析** > **Data Factory**。
   
   ![在 [新增] 窗格中選取資料處理站](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 在 **新的 data factory**頁面上，提供值的欄位，如下圖所示： 
      
   ![新的 Data factory 頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **名稱**：輸入 Azure 資料處理站的全域唯一名稱。 如果您收到「資料處理站名稱 \"LoadADLSDemo\" 無法使用」的錯誤，請為資料處理站輸入其他名稱。 例如，使用**您的名稱**  **ADFTutorialDataFactory**。 建立資料處理站。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。
    * 訂用帳戶  ：選取用來在其中建立資料處理站的 Azure 訂用帳戶。 
    * **資源群組**：從下拉式清單中選取現有的資源群組。 您也可以選取**新建**選項，然後輸入資源群組的名稱。 若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。 
    * **版本**：選取 [V2]  。
    * **位置**：選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 Data factory 所使用的資料存放區可位於其他地區和區域。 

3. 選取 [建立]  。
4. 建立完成之後，請移至您的 data factory。 您會看到如下圖所示的 [Data Factory]  首頁： 
   
   ![Data Factory 首頁](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 選取 **編寫與監視**圖格以啟動資料整合應用程式，在個別的索引標籤。

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>將資料載入 Azure Data Lake Storage Gen2 中

1. 在 **開始**頁面上，選取**複製資料**圖格以啟動複製資料工具。 

   ![複製資料工具圖格](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 在 **屬性**頁面上，指定**CopyFromADLSGen1ToGen2** for**工作名稱**欄位。 選取 [下一步]  。

    ![屬性頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 在 **來源資料存放區**頁面上，選取 **+ 建立新的連接**。

    ![來源資料存放區頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 選取  **Azure Data Lake 儲存體 Gen1**從連接器資源庫中，選取**繼續**。
    
    ![來源資料存放區 Azure Data Lake Storage Gen1 頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. 在 **指定 Azure Data Lake 儲存體 Gen1 連接**頁面上，請遵循下列步驟：

   a. 選取 [Data Lake Storage Gen1] 作為帳戶名稱，並指定或驗證 [租用戶]  。
  
   b. 選取 **測試連接**驗證設定。 然後選取 [完成]  。
  
   c. 您會看到已建立新的連接。 選取 [下一步]  。
   
   > [!IMPORTANT]
   > 在此逐步解說中，您可以使用適用於 Azure 資源的受管理身分識別來驗證您的 Azure Data Lake 儲存體 Gen1。 若要授與受管理的身分識別在 Azure Data Lake 儲存體 Gen1 中適當的權限，請遵循[這些指示](connector-azure-data-lake-store.md#managed-identity)。
   
   ![指定 Azure Data Lake Storage Gen1 帳戶](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 在 **選擇輸入的檔案或資料夾**頁面上，瀏覽至您想要複製的檔案與資料夾。 選取資料夾或檔案，然後選取**選擇**。

    ![選擇輸入檔案或資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 選取以指定複製行為**以遞迴方式複製檔案**並**二進位複製**選項。 選取 [下一步]  。

    ![指定輸出資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 在 **目的地資料存放區**頁面上，選取 **+ 建立新的連接** > **Azure Data Lake 儲存體 Gen2**  >  **繼續**。

    ![目的地資料存放區頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. 在 **指定 Azure Data Lake 儲存體 Gen2 連接**頁面上，請遵循下列步驟：

   a. 選取您的 Data Lake 儲存體 Gen2 支援帳戶，從**儲存體帳戶名稱**下拉式清單。
   
   b. 選取 [完成]  以建立連線。 然後，選取 [下一步]  。
   
   ![指定 Azure Data Lake Storage Gen2 帳戶](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 在 [**選擇輸出檔案或資料夾**頁面上，輸入**copyfromadlsgen1**作為輸出資料夾名稱，然後選取**下一步]** 。 Data Factory 建立子資料夾與對應的 Azure Data Lake 儲存體 Gen2 檔案系統複製期間如果不存在。

    ![指定輸出資料夾](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. 在 [**設定**頁面上，選取**下一步]** 以使用預設設定。

12. 在 [**摘要**頁面上，檢閱設定，然後選取**下一步]** 。

    ![摘要頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 在 **部署頁面**，選取**監視器**來監視管線。

    ![部署頁面](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 請注意，系統會自動選取左側的 [監視]  索引標籤。 [動作]  資料行中會有連結可供檢視活動執行詳細資料，以及重新執行管線。

    ![監視管線回合](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作]  資料行中的 [檢視活動執行]  連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回 [管線執行] 檢視，請選取頂端的 [管線]  連結。 選取 [重新整理]  即可重新整理清單。 

    ![監視活動回合](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 若要監視每個複製活動的執行詳細資料，請選取活動監控檢視中 [動作]  底下的 [詳細資料]  連結 (眼鏡圖示)。 您可以監視詳細資料，例如的大量資料從來源複製到接收資料輸送量、 執行步驟與對應的持續期間和所使用的設定。

    ![監視活動執行詳細資料](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 確認資料已複製到您的 Azure Data Lake 儲存體 Gen2 帳戶。

## <a name="best-practices"></a>最佳作法

若要評估升級從 Azure Data Lake 儲存體 Gen1 至 Azure Data Lake 儲存體 Gen2 一般情況下，請參閱[從 Azure Data Lake 儲存體 Gen1 的巨量資料分析解決方案升級至 Azure Data Lake 儲存體 Gen2](../storage/blobs/data-lake-storage-upgrade.md)。 下列各節會介紹使用 Data Factory 從 Data Lake 儲存體 Gen1 資料升級至 Data Lake 儲存體 Gen2 的最佳作法。

### <a name="data-partition-for-historical-data-copy"></a>資料分割的歷程記錄資料的複本

- 如果您在 Data Lake 儲存體 Gen1 中的總資料大小會小於 30 TB 的檔案數目會少於 1 百萬個，您就可以執行單一複製活動中複製所有資料。
- 如果您有大量的資料複製，或您想要管理批次中的資料移轉並使每個特定的時間範圍內完成的彈性，資料分割。 資料分割時，也會減少任何未預期的問題的風險。

使用概念證明，來確認端對端解決方案，並測試您的環境中的複製輸送量。 主要概念的步驟： 

1. 使用單一複製活動從 Data Lake 儲存體 Gen1 將數 Tb 的資料複製到 Data Lake 儲存體 Gen2 以取得複製效能基準線中建立一個 Data Factory 管線。 開頭[資料整合單位 (DIUs)](copy-activity-performance.md#data-integration-units)為 128。 
2. 根據您在步驟 1 中取得的複製輸送量，計算整個資料移轉具有所需的預估的時間。 
3. （選擇性）建立一個控制資料表，並定義檔案篩選器來分割要移轉的檔案。 分割檔案的方式是： 

    - 依資料夾名稱或資料夾名稱使用萬用字元篩選條件的資料分割。 我們建議您使用這個方法。
    - 資料分割檔案的上次修改時間。

### <a name="network-bandwidth-and-storage-io"></a>網路頻寬和儲存體 I/O 

您可以控制的並行讀取 Data Lake 儲存體 Gen1 中的資料和將資料寫入至 Data Lake 儲存體 Gen2 的 Data Factory 複製作業。 如此一來，您可以管理該存放裝置 I/O，避免在移轉期間影響一般商務工作，在 Data Lake 儲存體 Gen1 上的使用。

### <a name="permissions"></a>權限 

在 Data Factory [Data Lake 儲存體 Gen1 連接器](connector-azure-data-lake-store.md)支援服務主體和受管理的身分識別，為 Azure 資源驗證。 [Data Lake 儲存體 Gen2 連接器](connector-azure-data-lake-storage.md)支援帳戶金鑰、 服務主體和為 Azure 資源驗證的受管理身分識別。 若要讓 Data Factory 能夠瀏覽並將所有檔案都複製或存取控制清單 (Acl) 您需要最高足夠權限授與您提供來存取、 讀取或寫入的所有檔案和設定的 Acl，如果您選擇的帳戶。 在移轉期間，授與其的超級使用者 」 或 「 擁有者角色。 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留從 Data Lake 儲存體 Gen1 的 Acl

如果您想要複寫的 Acl，以及資料檔案，從 Data Lake 儲存體 Gen1 升級至 Data Lake 儲存體 Gen2 時，請參閱[保留的 Acl，從 Data Lake 儲存體 Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。 

### <a name="incremental-copy"></a>增量複製 

您可以使用數種方法，從 Data Lake 儲存體 Gen1 載入只將新的或更新檔案：

- 依時間分割的資料夾或檔案名稱載入新的或更新的檔案。 例如，/2019年/05/13 / *。
- 載入新的或更新檔案： 依 LastModifiedDate。
- 找出新的或更新的檔案，由任何協力廠商工具或解決方案。 透過參數、 資料表或檔案，然後到 Data Factory 管線傳遞的檔案或資料夾名稱。 

若要執行累加式載入適當的頻率取決於在 Azure Data Lake 儲存體 Gen1 中的檔案總數和要載入每次新的或更新檔案的磁碟區。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [複製活動概觀](copy-activity-overview.md)
> [Azure Data Lake 儲存體 Gen1 連接器](connector-azure-data-lake-store.md)
> [Azure Data Lake 儲存體 Gen2 連接器](connector-azure-data-lake-storage.md)