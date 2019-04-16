---
title: 使用 SQL API 建置採用 Azure Cosmos DB 的 .NET 應用程式
description: 在本快速入門中，您會使用 Azure 入口網站和 .NET Web 應用程式來建立和管理 Azure Cosmos DB 中的 SQL API 帳戶資源。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 7ecb2269243ae96b629a20a26956e6220a2e616c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280837"
---
# <a name="quickstart-build-a-net-web-app-using-sql-api-account-in-azure-cosmos-db"></a>快速入門：在 Azure Cosmos DB 中使用 SQL API 帳戶建置 .NET Web 應用程式

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (預覽)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以使用 Azure Cosmos DB 來快速建立及查詢索引鍵/值資料庫、文件資料庫和圖形資料庫，這些資料庫全都受益於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門示範如何使用 Azure 入口網站來建立 Azure Cosmos DB [SQL API](sql-api-introduction.md) 帳戶、建立文件資料庫和集合，以及在集合中新增資料。 然後，您會使用 [SQL .NET SDK](sql-api-sdk-dotnet.md) Web 應用程式在集合中新增更多資料。 

在本快速入門中，您會使用 Azure 入口網站中的資料總管來建立資料庫和集合。 您也可以使用 .NET 程式碼範例來建立資料庫和集合。 若要深入了解，請檢閱[檢閱 .NET 程式碼](#review-the-net-code)。 

## <a name="prerequisites"></a>必要條件

安裝具有 Azure 開發工作流程的 Visual Studio 2017
- 您可以下載並使用**免費**的 [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。 

Azure 訂用帳戶或免費的 Azure Cosmos DB 試用帳戶
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>新增資料庫與集合 

您可以在 Azure 入口網站中使用資料總管，建立資料庫和集合。 

1.  在 Azure Cosmos DB 帳戶頁面上的左側導覽中選取 [資料總管]，然後選取 [新增集合]。 
    
    您可能需要向右捲動才能看到 [新增集合] 區域。
    
    ![Azure 入口網站資料總管，[新增集合] 窗格](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  在 [新增集合] 頁面上，輸入新集合的設定。
    
    |設定|建議的值|說明
    |---|---|---|
    |**資料庫識別碼**|ToDoList|輸入 *ToDoList* 作為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 `/, \\, #, ?` 或尾端空格。|
    |**集合識別碼**|項目|輸入 *Items* 作為新集合的名稱。 集合識別碼與資料庫名稱具有相同的字元需求。|
    |**資料分割索引鍵**| /類別| 本文中所述的範例使用 */category* 作為分割區索引鍵。|
    |**Throughput**|400|讓輸送量保持在每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以相應增加輸送量。| 
    
    在此範例中，請勿新增**唯一索引鍵**。 唯一索引鍵可讓您藉由確保每個分割索引鍵有一或多個唯一值，來對資料庫新增一層資料完整性。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)。
    
1.  選取 [確定] 。 
    [資料總管] 會顯示新的資料庫和集合。
    
    ![Azure 入口網站 [資料總管]，顯示新的資料庫和集合](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

## <a name="add-data-to-your-database"></a>將資料新增至資料庫

使用資料總管將資料新增至新的資料庫。

1. 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 依序展開 [ToDoList] 資料庫和 [項目] 集合，然後依序選取 [文件] 和 [新增文件]。 
   
   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. 在 [文件] 窗格右邊的文件中新增下列結構：

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. 選取 [ **儲存**]。
   
   ![將 json 資料複製在 Azure 入口網站的 [資料總管] 中並選取 [儲存]](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. 再次選取 [新增文件]，然後使用唯一 `id` 以及任何其他您想要的屬性和值來建立和儲存另一個文件。 文件可擁有任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。

## <a name="query-your-data"></a>查詢資料

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="use-the-net-web-app-to-manage-data"></a>使用 .NET Web 應用程式來管理資料

若要了解以程式設計方式處理 Azure Cosmos DB 資料有多麼簡單，請從 GitHub 複製 SQL API .NET Web 應用程式範例、更新連接字串，然後執行應用程式以更新資料。 

您也可以使用 .NET 程式碼範例來建立資料庫和集合。 若要深入了解，請檢閱[檢閱 .NET 程式碼](#review-the-net-code)。

### <a name="clone-the-sample-app"></a>複製範例應用程式

首先，請從 GitHub 複製 C# [SQL API 應用程式](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)。 

1. 開啟 git 終端機視窗 (例如 Git Bash)、建立名為「git-samples」的新目錄，並將其變更為： 
   
   ```bash
   mkdir /c/git-samples/
   cd /c/git-samples/
   ```
   
1. 執行下列命令來複製存放庫範例，然後在電腦上建立應用程式範例複本：
   
   ```bash
   git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
   ```

### <a name="update-the-connection-string"></a>更新連接字串 

1. 在 Visual Studio 中瀏覽至所複製應用程式的「todo.sln」檔案，然後加以開啟。 

1. 在 Visual Studio 的**方案總管**中，開啟「web.config」檔案。 

1. 返回 Azure 入口網站來複製連接字串資訊，以便貼到「web.config」中。
   
   1. 在 Azure Cosmos DB 帳戶的左側導覽中，選取 [金鑰]。
      
      ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-sql-api-dotnet/keys.png)
      
   1. 在 [讀寫金鑰] 底下，使用右側的 [複製] 按鈕複製 **URI** 值，然後將其貼到「web.config」中的 `endpoint` 金鑰。例如︰ 
      
      `<add key="endpoint" value="https://mysqlapicosmosdb.documents.azure.com:443/" />`
      
   1. 複製**主要金鑰**值，然後將其貼到「web.config」中的 `authKey` 金鑰。例如︰
      
      `<add key="authKey" value="19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==" />`

       
1. 確定「web.config」中的資料庫和集合值符合您稍早建立的名稱。 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
1. 儲存「web.config」。您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。

### <a name="run-the-web-app"></a>執行 Web 應用程式

1. 在 Visual Studio 中，於 [方案總管] 中的 **todo** 專案上按一下滑鼠右鍵，然後選取 [管理 NuGet 套件]。 

1. 在 NuGet [瀏覽] 方塊中，輸入 *DocumentDB*。

1. 從結果中，安裝 **Microsoft.Azure.DocumentDB** 程式庫 (若尚未安裝)。 這會安裝 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 套件以及所有相依性。
   
   如果 NuGet 套件管理員顯示訊息指出方案中遺漏某些套件，則請選取 [還原] 以從內部來源進行安裝。 

1. 選取 **Ctrl**+**F5** 以在瀏覽器中執行應用程式。 

1. 在 to-do 應用程式中選取 [新建]，然後建立一些新工作。

   ![具有範例資料的待辦事項應用程式](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

您可以返回 Azure 入口網站中的資料總管來查看、查詢、修改及使用新資料。 

## <a name="review-the-net-code"></a>檢閱 .NET 程式碼

此為選用步驟。 在本快速入門中，您已在 Azure 入口網站中建立資料庫和集合，並使用 .NET 範例新增資料範例。 不過，您也可以使用的.NET 範例建立資料庫和集合。 如果您想要了解程式碼會如何建立資料庫資源，請檢閱下列程式碼片段。 程式碼片段全部取自 **todo** 專案中的「DocumentDBRepository.cs」檔案。

* 此程式碼會初始化 `DocumentClient`： 

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* 此程式碼會使用 `CreateDatabaseAsync` 方法建立新的資料庫：

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 下列程式碼會使用 `CreateDocumentCollectionAsync` 方法建立新的集合：

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

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立資料庫和集合，以及如何執行 .NET Web 應用程式來更新資料。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)

