---
title: 使用 SQL API 建置採用 Azure Cosmos DB 的 .NET 應用程式
description: 在本快速入門中，使用 Azure Cosmos DB SQL API 和 Azure 入口網站來建立 .NET Web 應用程式
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/15/2019
ms.openlocfilehash: 1ef414b2de2acbf5b92661c8b5f1e249549b14df
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259137"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-db-sql-api-account"></a>快速入門：使用 Azure Cosmos DB SQL API 帳戶建置 .NET Web 應用程式

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (預覽)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

此快速入門示範如何使用 Azure 入口網站建立 Azure Cosmos DB [SQL API](sql-api-introduction.md) 帳戶、文件資料庫、集合，以及將範例資料新增至集合。 接著，您會建置和部署以 [SQL .NET SDK](sql-api-sdk-dotnet.md) 建置的待辦事項清單 Web 應用程式，以在集合內新增更多管理資料。 

## <a name="prerequisites"></a>必要條件

如果尚未安裝 Visual Studio 2017，您可以下載並使用**免費的** [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-account"></a>建立帳戶

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>新增資料庫與集合

您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立資料庫和集合。 

1. 按一下 [資料總管] > [新增集合]。 
    
    [新增集合] 區域會顯示在最右邊，您可能需要向右捲動才能看到它。

    ![Azure 入口網站資料總管，[新增集合] 窗格](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)

2. 在 [新增集合] 頁面上，輸入新集合的設定。

    設定|建議的值|說明
    ---|---|---
    **資料庫識別碼**|ToDoList|輸入 *ToDoList* 作為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 `/, \\, #, ?` 或尾端空格。
    **集合識別碼**|項目|輸入 *Items* 作為新集合的名稱。 集合識別碼與資料庫名稱具有相同的字元需求。
    **分割區索引鍵**| `<your_partition_key>`| 輸入分割區索引鍵。 本文中所述的範例使用 */category* 作為分割區索引鍵。
    **輸送量**|400 RU|將輸送量變更為每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以相應增加輸送量。 
    
    除了上述的設定，您可以選擇性地為集合新增 [唯一索引鍵]。 在此範例中，讓我們將欄位保留空白。 唯一索引鍵可讓開發人員在資料庫中新增一層資料完整性。 您可在建立集合時建立唯一索引鍵原則，以確保每個資料分割索引鍵一或多個值的唯一性。 若要深入了解，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)一文。
    
    按一下 [確定]。

    [資料總管] 會顯示新的資料庫和集合。

    ![Azure 入口網站 [資料總管]，顯示新的資料庫和集合](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>新增範例資料

您現在可以使用 [資料總管] 將資料新增至您的新集合。

1. 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 依序展開 [工作] 資料庫、[項目] 集合，按一下 [文件]，然後按一下 [新增文件]。 

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
  
2. 現在將文件新增至具有下列結構的集合。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. 將 json 新增至 [文件] 索引標籤後，按一下 [儲存]。

    ![將 json 資料複製在 Azure 入口網站的 [資料總管] 中並按一下 [儲存]](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)

4. 多建立並儲存一份文件，以便在其中插入 `id` 屬性的唯一值，並適當變更其他屬性。 新文件可擁有您想要的任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。

## <a name="query-your-data"></a>查詢資料

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 我們將[從 GitHub 複製 SQL API 應用程式](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟命令提示字元，建立名為 git-samples 的新資料夾，然後關閉命令提示字元。

    ```bash
    md "C:\git-samples"
    ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

    ```bash
    cd "C:\git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

4. 然後在 Visual Studio 中開啟待辦事項方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。 在本快速入門中，您可以使用 Azure 入口網站建立資料庫和集合，以及使用 .NET 範例新增範例資料。 不過，您也可以使用的.NET 範例建立資料庫和集合。 

下列程式碼片段全部取自 DocumentDBRepository.cs 檔案。

* 將 DocumentClient 初始化，如下列程式碼所示：

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* 使用 `CreateDatabaseAsync` 方法建立新的資料庫，如下列程式碼所示：

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 使用 `CreateDocumentCollectionAsync` 方法建立新的集合，如下列程式碼所示：

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，選取 [金鑰]，然後選取 [讀寫金鑰]。 在下一個步驟中，您將使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 web.config 檔案。

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-sql-api-dotnet/keys.png)

2. 在 Visual Studio 2017 中，開啟 web.config 檔案。 

3. 從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，並使它成為 web.config 中的端點金鑰值。 

    `<add key="endpoint" value="FILLME" />`

4. 然後，從入口網站複製您的主要金鑰值，並使它成為 web.config 中的 authKey 值。 

    `<add key="authKey" value="FILLME" />`
    
5. 然後更新資料庫和集合值，以符合您稍早建立的資料庫名稱。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
## <a name="run-the-web-app"></a>執行 Web 應用程式
1. 在 Visual Studio 中，於 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [管理 NuGet 套件]。 

2. 在 NuGet [瀏覽] 方塊中，輸入 *DocumentDB*。

3. 從結果中，安裝 **Microsoft.Azure.DocumentDB** 程式庫。 這會安裝 Microsoft.Azure.DocumentDB 套件以及所有相依性。

4. 選取 CTRL + F5 以執行應用程式。 您的應用程式會顯示在瀏覽器中。 

5. 選取瀏覽器中的 [新建]，然後在您的待辦事項應用程式中建立一些新工作。

   ![具有範例資料的待辦事項應用程式](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

您現在可以返回 [資料總管]，以查看、查詢、修改及使用這項新資料。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立集合，以及如何執行 Web 應用程式。 您現在可以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)


