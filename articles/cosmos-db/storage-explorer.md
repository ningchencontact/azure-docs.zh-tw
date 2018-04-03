---
title: 在 Azure 儲存體總管中管理 Azure Cosmos DB
description: 學習如何在 Azure 儲存體總管中管理 Azure Cosmos DB。
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 18f580f1eae31c9bf3626e100217467bb48ca881
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>在 Azure 儲存體總管 (預覽版本) 中管理 Azure Cosmos DB

在 Azure 儲存體總管中使用 Azure Cosmos DB 可讓使用者管理 Azure Cosmos DB 實體、操縱資料、更新預存程序及觸發程序，以及其他 Azure 實體 (例如儲存體 Blob 及佇列)。 現在您可以使用同一個工具在同一處管理您不同的 Azure 實體。 目前，Azure 儲存體總管支援 SQL、MongoDB、圖表和資料表帳戶。

在本文中，您將會了解如何使用儲存體總管來管理 Azure Cosmos DB。


## <a name="prerequisites"></a>先決條件

SQL API <!--or MongoDB API--> 的 Azure Cosmos DB 帳戶。 若您還沒有帳戶，您可以根據[Azure Cosmos DB：使用 .NET 及 Azure 入口網站建置 SQL API Web 應用程式](create-sql-api-dotnet.md)中的說明，在 Azure 入口網站中建立帳戶。

## <a name="installation"></a>安裝

在這裡安裝最新的 Azure 儲存體總管位元：[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，我們現在支援 Windows、Linux 及 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

1. 在安裝 **Azure 儲存體總管**之後，按一下左邊的**外掛程式**圖示，如下圖所示：
       
   ![外掛程式圖示](./media/storage-explorer/plug-in-icon.png)
 
2. 選取 [新增 Azure 帳戶]，然後按一下 [登入]。

   ![連線到 Azure 訂用帳戶](./media/storage-explorer/connect-to-azure-subscription.png)

2. 在 [Azure 登入] 對話方塊中，選取 [登入]，然後輸入您的 Azure 認證。

    ![登入](./media/storage-explorer/sign-in.png)

3. 從清單中選取您的訂用帳戶，然後按一下 [套用]。

    ![套用](./media/storage-explorer/apply-subscription.png)

    [總管] 窗格會更新，並顯示選取之訂閱中的帳戶。

    ![帳戶清單](./media/storage-explorer/account-list.png)

    您已成功將您的 **Cosmos DB 帳戶**連線到您的 Azure 訂用帳戶。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB

另外一種連線到 Azure Cosmos DB 的方式為使用連接字串。 使用下列步驟來使用連接字串進行連線。

1. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)，以滑鼠右鍵按一下 [Cosmos DB 帳戶]，選擇 [連線到 Cosmos DB...]

    ![透過連接字串連線到 Cosmos DB](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. 目前僅支援 SQL 和資料表 API。 選擇 API、貼上**連接字串**、輸入**帳戶標籤**、按 [下一步] 以檢查摘要，然後按一下 [連線] 以連線到 Azure Cosmos DB 帳戶。 如需擷取連接字串的資訊，請參閱[取得連接字串](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![連接字串](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>使用本機模擬器連線到 Azure Cosmos DB
若要使用模擬器連線到 Azure Cosmos DB，請使用下列步驟 (目前僅支援 SQL 帳戶)。
1. 安裝模擬器並啟動。 如需了解如何安裝模擬器，請參閱 [Cosmos DB 模擬器](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)
2. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)，以滑鼠右鍵按一下 [Cosmos DB 帳戶]，選擇 [連線到 Cosmos DB 模擬器...]

    ![使用模擬器連線到 Cosmos DB](./media/storage-explorer/emulator-entry.png)

3. 目前僅支援 SQL API。 貼上**連接字串**、輸入**帳戶標籤**、按 [下一步] 以檢查摘要，然後按一下 [連線] 以連線到 Azure Cosmos DB 帳戶。 如需擷取連接字串的資訊，請參閱[取得連接字串](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![使用模擬器對話方塊連線到 Cosmos DB](./media/storage-explorer/emulator-dialog.png)



## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 資源管理

您可以透過執行下列作業來管理 Azure Cosmos DB 帳戶：
* 在 Azure 入口網站中開啟帳戶
* 將資源新增至快速存取清單
* 搜尋和重新整理資源
* 建立和刪除資料庫
* 建立和刪除集合
* 建立、編輯、刪除和篩選文件
* 管理預存程序、觸發程序和使用者定義函式

### <a name="quick-access-tasks"></a>快速存取工作

以滑鼠右鍵按一下 [總管] 窗格中的訂用帳戶，您可以執行許多快速動作工作：

* 以滑鼠右鍵按一下 Azure Cosmos DB 帳戶或資料庫，您可以選擇 [在入口網站中開啟] 並使用瀏覽器在 Azure 入口網站上管理資源。

     ![在入口網站中開啟](./media/storage-explorer/open-in-portal.png)

* 您也可以將 Azure Cosmos DB 帳戶、資料庫及集合新增至 [快速存取]。
* [Search from Here] \(從這裡搜尋) 可在選取的路徑下使用關鍵字搜尋。

    ![從這裡搜尋](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>資料庫與集合管理
#### <a name="create-a-database"></a>建立資料庫 
-   以滑鼠右鍵按一下 Azure Cosmos DB 帳戶，選擇 [建立資料庫]，輸入資料庫名稱，然後按  **ENTER 鍵**以完成。
       
    ![建立資料庫](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>刪除資料庫
- 以滑鼠右鍵按一下資料庫，按一下 [刪除資料庫]，然後在快顯視窗中按一下 [是]。 資料庫節點隨即會刪除，而 Azure Cosmos DB 帳戶會自動重新整理。

    ![刪除 database1](./media/storage-explorer/delete-database1.png)  

    ![刪除 database2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>建立集合
1. 以滑鼠右鍵按一下您的資料庫，選擇 [建立集合]，然後提供下列資訊 (例如**集合識別碼****儲存體容量**等)。按一下 [確定] 以完成。 

    ![建立 collection1](./media/storage-explorer/create-collection.png)

    ![建立 collection2](./media/storage-explorer/create-collection2.png) 

2. 選取 [無限制] 即可指定分割區索引鍵，然後按一下 [確定] 以完成。

    若在建立集合時使用了分割區索引鍵，則一旦建立過程完成後，該分割區索引鍵的值便不能在集合上進行變更。 如需分割區索引鍵設定的資訊，請參閱[設計資料分割](partition-data.md#designing-for-partitioning)。

    ![資料分割索引鍵](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>刪除集合
- 以滑鼠右鍵按一下集合，按一下 [刪除集合]，然後在快顯視窗中按一下 [是]。 

    集合節點隨即刪除，且資料庫會自動重新整理。

    ![刪除集合](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>文件管理

#### <a name="create-and-modify-documents"></a>建立及修改文件
- 若要建立新文件，請在左邊視窗中開啟 [文件]，按一下 [新增文件]，在右邊窗格中編輯內容，然後按一下 [儲存]。 您可以也更新現有的文件，然後按一下 [儲存]。 按一下 [捨棄] 以捨棄變更。

    ![文件](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>刪除文件
- 按一下 [刪除] 按鈕來刪除選取的文件。

#### <a name="query-for-documents"></a>查詢文件
- 輸入 [SQL 查詢](sql-api-sql-query.md)，然後按一下 [套用] 來編輯文件篩選。

    ![文件篩選器](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>圖表管理

#### <a name="create-and-modify-vertex"></a>建立及修改頂點
1. 若要建立新的頂點，請從左側視窗中開啟 [圖表]，按一下 [新增頂點] 並編輯內容，然後按一下 [確定]。    
2. 若要修改現有的頂點，按一下右側窗格中的畫筆圖示。   

    ![圖形](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>建立圖表
- 若要刪除頂點，按一下頂點名稱旁的 [資源回收筒] 圖示。

#### <a name="filter-for-graph"></a>篩選圖表
- 輸入 [Gremlin 查詢](gremlin-support.md) 來編輯圖表篩選器，然後按一下 [套用篩選條件]。

    ![圖表篩選器](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>資料表管理

#### <a name="create-and-modify-table"></a>建立及修改資料表
1. 若要建立新的資料表，請從左側視窗開啟 [實體]，按一下 [新增]、編輯 [新增實體] 對話方塊中的內容、按一下 [新增屬性] 按鈕來新增屬性，然後按一下 [插入]。
2. 若要修改資料表，請按一下 [編輯] 並修改內容，然後按一下 [更新]。

    ![資料表](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>匯入和匯出資料表
1. 若要匯入，請按一下 [匯入] 按鈕，並選擇現有的資料表。
2. 若要匯出，請按一下 [匯出] 按鈕，並選擇目的地。

    ![資料表匯入和匯出](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>刪除實體
- 選取實體，然後按一下 [刪除] 按鈕。

    ![資料表刪除](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>查詢資料表
- 按一下 [查詢] 按鈕、輸入查詢條件，然後按一下 [執行查詢] 按鈕。 按一下 [關閉查詢] 按鈕即可關閉 [查詢] 窗格。

    ![資料表查詢](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理預存程序、觸發程序和 UDF
* 若要建立預存程序，請在左邊的樹狀目錄中，以滑鼠右鍵按一下 [預存程序]，選擇 [建立預存程序]，在左邊輸入名稱，在右邊的視窗中輸入預存程序指令碼，然後按一下 [建立]。 
* 您也可以透過按兩下現有的預存程序，進行更新，然後按一下 [更新] 以儲存，或按一下 [捨棄] 以取消變更，來編輯現有的預存程序。

    ![預存程序](./media/storage-explorer/stored-procedure.png)
* **觸發程序**及 **UDF** 的作業與**預存程序**雷同。

## <a name="next-steps"></a>後續步驟

* 請觀看下列影片以了解如何在 Azure 儲存體總管中使用 Azure Cosmos DB：[在 Azure 儲存體總管中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[開始使用儲存體總管 (預覽)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) 中深入了解儲存體總管並連線更多服務。

