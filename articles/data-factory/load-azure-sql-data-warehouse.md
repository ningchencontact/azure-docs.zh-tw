---
title: "使用 Azure Data Factory 將資料載入 Azure SQL 資料倉儲中 | Microsoft Docs"
description: "使用 Azure Data Factory 將資料複製到 Azure SQL 資料倉儲中"
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
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>使用 Azure Data Factory 將資料載入 Azure SQL 資料倉儲中

[Azure SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)是一種雲端架構、相應放大的資料庫，可處理巨量關聯式與非關聯式資料。  SQL 資料倉儲是以巨量平行處理 (MPP) 架構為基礎，最適用於企業資料倉儲工作負載。  它透過單獨調整儲存體和計算的彈性，來提供雲端彈性。

現在，使用 Azure SQL 資料倉儲比之前使用 **Azure Data Factory** 還要簡單。  Azure Data Factory 是一個完全受控的雲端架構資料整合服務，可用來將現有系統的資料填入 SQL 資料倉儲，並節省評估 SQL 資料倉儲及建置分析解決方案的寶貴時間。 以下是使用 Azure Data Factory 將資料載入至 Azure SQL 資料倉儲的主要優點：

* **容易設定**：不需要編寫指令碼的 5 步驟直覺式精靈。
* **豐富的資料存放區支援**︰一組豐富內部部署和雲端架構資料存放區的內部支援，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)資料表中的詳細清單。
* **安全並符合標準**：資料是透過 HTTPS 或 ExpressRoute 傳輸，並且具有全域服務可確保資料絕不會離開地理界限
* **PolyBase 的無與倫比使用效能**：使用 Polybase 是將資料移至 Azure SQL 資料倉儲的最有效方式。 您可以使用暫存 Blob 功能來高速載入所有類型的資料存放區 (除了 Azure Blob 儲存體和 Data Lake Store 之外)，而 Polybase 預設支援這項作業。 請參閱[複製活動效能](copy-activity-performance.md)以了解詳細資料。

本文將示範如何使用 Data Factory 複製資料工具，**將資料從 Azure SQL Database 載入 Azure SQL 資料倉儲中**。 您可以依照類似的步驟，從其他類型的資料存放區複製資料。

> [!NOTE]
>  如需關於 Data Factory 在 Azure SQL 資料倉儲中來回複製資料功能的一般資訊，請參閱[使用 Azure Data Factory 將資料複製到 Azure SQL 資料倉儲或從該處複製資料](connector-azure-sql-data-warehouse.md)一文。
>
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。
* **Azure SQL 資料倉儲**。 此資料倉儲保存從 SQL Database 複製的資料。 如果您沒有 Azure SQL 資料倉儲，請參閱[建立 SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)文章，以取得建立的步驟。
* **Azure SQL Database**。 本教學課程會從具有 Adventure Works LT 範例資料的 Azure SQL Database 複製資料，您可以遵循[建立 Azure SQL Database](../sql-database/sql-database-get-started-portal.md)一文，來建立一個 Azure SQL Database。 
* **Azure 儲存體帳戶**。 在大量複製作業中，Azure 儲存體會作為**暫存** Blob。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文，按照步驟來建立帳戶。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 按一下左側功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，提供如以下螢幕擷取畫面所示的值：
      
     ![新增資料處理站頁面](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **名稱。** 輸入資料處理站的全域唯一名稱。 如果您收到錯誤，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **訂用帳戶。** 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
    * **資源群組。** 從下拉式清單中選取現有的資源群組，或選取 [新建] 選項，然後輸入資源群組的名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本。** 選取 [V2 (預覽)]。
    * **位置。** 選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 可位於其他位置/區域。 

3. 按一下頁面底部的 [新增] 。
4. 建立完成之後，請前往資料處理站，您就會看到如圖中所示的 [Data Factory] 頁面。 按一下 [編寫與監視] 圖格，以在另一個索引標籤中啟動資料整合應用程式。
   
   ![Data Factory 首頁](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>將資料載入 Azure SQL 資料倉儲

1. 在 [開始使用] 頁面中，按一下 [複製資料] 圖格以啟動複製資料工具。 

   ![複製資料工具圖格](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. 在複製資料工具的 [屬性] 頁面中，指定 [CopyFromSQLToSQLDW] 作為 [工作名稱]，然後按 [下一步]。 

    ![複製資料工具 - 屬性頁面](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] 頁面上，選取 [Azure SQL Database]，然後按 [下一步]。

    ![來源資料存放區頁面](./media/load-azure-sql-data-warehouse/specify-source.png)
4. 在 [指定 Azure SQL 資料庫] 頁面中，執行以下步驟︰ 
    1. 針對 [伺服器名稱] 選取您的 Azure SQL 伺服器。
    2. 針對 [資料庫名稱] 選取您的 Azure SQL 資料庫。
    3. 針對 [使用者名稱] 指定使用者名稱。
    4. 針對 [密碼] 指定使用者的密碼。
    5. 按 [下一步] 。 

        ![指定 Azure SQL DB](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. 在 [選取要從中複製資料的資料表或使用自訂查詢] 頁面中，於輸入方塊中指定 [SalesLT] 來篩選資料表，接著核取 [(全選)] 核取方塊以選取所有資料表，然後按 [下一步]。 

    ![選擇輸入檔案或資料夾](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. 在 [目的地資料存放區] 頁面中，選取 [Azure SQL 資料倉儲]，然後按 [下一步]。 

    ![目的地資料存放區頁面](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. 在 [指定 Azure SQL 資料倉儲] 頁面中，執行以下步驟︰ 

    1. 針對 [伺服器名稱] 選取您的 Azure SQL 伺服器。
    2. 選取您的 Azure SQL 資料倉儲作為 [資料庫名稱]。
    3. 針對 [使用者名稱] 指定使用者名稱。
    4. 針對 [密碼] 指定使用者的密碼。
    5. 按 [下一步] 。 

        ![指定 Azure SQL 資料倉儲](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. 在 [資料表對應] 頁面上，檢閱內容然後按 [下一步]。 出現智慧型資料表對應，它會根據資料表名稱將來源對應至目的地資料表。 如果資料表不存在於目的地中，Azure Data Factory 就會根據預設以相同名稱建立一個資料表。 您也可以選擇對應至現有的資料表。 

    > [!NOTE]
    > 當來源為 SQL Server 或 Azure SQL Database 時，系統會套用自動為 SQL 資料倉儲接收建立資料表的功能。 如果您從其他來源資料存放區複製資料，就必須先在接收 Azure SQL 資料倉儲中預先建立結構描述，才能進行資料複製。

    ![資料表對應頁面](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. 在 [結構描述對應] 頁面上，進行檢閱然後按 [下一步]。 智慧型對應是根據資料行名稱。 如果您選擇讓 Data Factory 自動建立資料表，則必要時會進行適當的資料類型轉換，以修正來源和目的地存放區之間的不相容性。 如果在來源和目的地資料行之間有不支援的資料類型轉換，您會看到一則錯誤訊息與對應的資料表。

    ![結構描述對應頁面](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. 在 [設定] 頁面中，選取 [儲存體帳戶名稱] 下拉式清單中的 Azure 儲存體。 它可用來在使用 PolyBase 載入到 SQL 資料倉儲之前暫存資料。 複製完成之後，將會自動清除儲存體中的暫時資料。 

    在 [進階設定] 下，取消核取 [使用類型預設值] 選項。

    ![設定頁面](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. 在 [摘要] 頁面中，檢閱設定，然後按 [下一步]。

    ![摘要頁面](./media/load-azure-sql-data-warehouse/summary-page.png)
13. 在 [部署] 頁面上，按一下 [監視] 來監視管線 (工作)。

    ![部署頁面](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. 請注意，系統會自動選取左側的 [監視] 索引標籤。 您會在 [動作] 資料行中看到有連結可供檢視活動執行詳細資料，以及可供重新執行管線。 

    ![監視管線回合](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. 若要檢視與此管線執行相關聯的活動執行，按一下 [動作] 資料行中的 [檢視活動執行] 連結。 管線中有 10 個複製活動，每個複製活動都會複製一個資料表的資料。 若要切換回 [管線執行] 檢視，按一下頂端的 [管線] 連結。 按一下 [重新整理] 即可重新整理清單。 

    ![監視活動回合](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. 您可以按一下活動監控檢視中 [動作] 底下的 [詳細資料] 連結，以進一步監視每個複製活動的執行詳細資料。 其中顯示的資訊包括從來源複製到接收端的資料量、輸送量、複製活動進行的步驟以及對應的持續期間和所使用的設定。

    ![監視活動執行詳細資料](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>後續步驟

前往下列文章，以了解 Azure SQL 資料倉儲支援： 

> [!div class="nextstepaction"]
>[Azure SQL 資料倉儲連接器](connector-azure-sql-data-warehouse.md)