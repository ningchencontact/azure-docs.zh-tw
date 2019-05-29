---
title: 建置 .NET 主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶中的資料
description: 此教學課程將使用 SQL API 來建立線上資料庫，以及 C# 主控台應用程式。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 7574985dbcc502d03bc886c7651c859b22968c5f
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596102"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>建置 .NET 主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶中的資料

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (預覽)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (預覽)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [非同步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

歡迎使用 Azure Cosmos DB SQL API 入門教學課程。 完成此教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 Azure Cosmos DB 資源。

本教學課程說明如何：

> [!div class="checklist"]
>
> - 建立並連線至 Azure Cosmos DB 帳戶
> - 建立 Visual Studio 解決方案
> - 建立資料庫
> - 建立集合
> - 建立 JSON 文件
> - 查詢集合
> - 更新 JSON 文件
> - 刪除文件
> - 刪除資料庫

## <a name="prerequisites"></a>必要條件

安裝具有 Azure 開發工作流程的 Visual Studio 2017：
- 您可以下載並使用**免費**的 [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。 

Azure 訂用帳戶或免費的 Cosmos DB 試用帳戶：
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- 如果您是使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器。 然後開始[設定 Visual Studio 解決方案](#SetupVS)的教學課程。
  
## <a name="get-the-completed-solution"></a>取得完整的解決方案

如果您沒有時間完成此教學課程，或只想要程式碼範例，您可以從 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) 下載完整的解決方案。 

若要執行已下載的完整解決方案： 

1. 確認您已安裝這些[必要條件](#prerequisites)。 
1. 在 Visual Studio 中開啟下載的 *GetStarted.sln* 解決方案檔案。
1. 在 [方案總管]  中，以滑鼠右鍵按一下 [GetStarted]  專案，然後選取 [管理 NuGet 套件]  。
1. 在 [NuGet]  索引標籤上，選取 [還原]  來還原 Azure Cosmos DB.NET SDK 的參考。
1. 接下來，在 *App.config* 檔案中更新 `EndpointUrl` 和 `PrimaryKey` 值，如[連線至 Azure Cosmos DB 帳戶](#Connect)中所述。
1. 選取 [偵錯]   > [啟動但不偵錯]  或按 **Ctrl**+**F5** 建置並執行應用程式。

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

在 Azure 入口網站中建立 Azure Cosmos DB 帳戶。 如果您已使用 Azure Cosmos DB 帳戶，請跳至[設定 Visual Studio 解決方案](#SetupVS)。 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>設定 Visual Studio 解決方案

1. 在 Visual Studio 2017 中，選取 [檔案]   > [新增]   > [專案]  。
   
1. 在 [新增專案]  對話方塊中，選取 [Visual C#]   > [主控台應用程式 (.NET Framework)]  、將專案命名為 *AzureCosmosDBApp*，然後按一下 [確定]  。
   
   ![[新增專案] 視窗的螢幕擷取畫面](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. 在 [方案總管]  中，以滑鼠右鍵按一下 **AzureCosmosDBApp** 專案，然後選取 [管理 NuGet 套件]  。
   
   ![專案操作功能表](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. 在 [NuGet]  索引標籤中，選取 [瀏覽]  ，然後在搜尋方塊中輸入 *azure documentdb*。
   
1. 尋找並選取 [Microsoft.Azure.DocumentDB]  ，如果尚未安裝，請選取 [安裝]  。
   
   「Azure Cosmos DB SQL API 用戶端程式庫」的套件識別碼是 [Microsoft Azure Cosmos DB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) \(英文\)。

   > [!NOTE]
   > 如果您使用 .NET Core，請參閱 [.NET Core 文件](./sql-api-dotnetcore-get-started.md)。

   ![用於尋找 Azure Cosmos DB 用戶端 SDK 的 NuGet 功能表螢幕擷取畫面](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   如果您收到關於檢閱方案變更的訊息，請選取 [確定]  。 如果您收到關於接受授權的訊息，請選取 [我接受]  。

## <a id="Connect"></a>連線至 Azure Cosmos DB 帳戶

現在，開始撰寫一些程式碼。 此教學課程的完整 *Project.cs* 檔案位於 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) 中。

1. 在 [方案總管]  中，選取 [Program.cs]  ，然後在程式碼編輯器中，將下列參考加入至檔案的開頭：
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. 接下來，將下列兩個常數和 `client` 變數新增至 `public class Program`。
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. 端點 URL 和主要金鑰可讓您的應用程式連線到您的 Azure Cosmos DB 帳戶，Azure Cosmos DB 帳戶才會信任該連線。 從 [Azure 入口網站](https://portal.azure.com)複製金鑰，並將其貼到您的程式碼中。 

   
   1. 在 Azure Cosmos DB 帳戶的左側導覽中，選取 [金鑰]  。
      
      ![在 Azure 入口網站的 [金鑰] 頁面中，檢視並複製存取金鑰](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. 在 [讀寫金鑰]  底下，使用右側的 [複製] 按鈕複製 **URI** 值，然後將其貼到 *Program.cs* 中的 `<your endpoint URL>`。 例如︰ 
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. 複製**主要金鑰**值，然後將其貼到 *Program.cs* 中的 `<your primary key>`。 例如︰ 
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. 在 `Main` 方法之後，新增一個名為 `GetStartedDemo` 的新非同步工作，該工作具現化一個名為 `client` 的新 `DocumentClient`。
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```

   如果您使用 Proxy 物件來連線到 Azure Cosmos DB，您應該改用下列程式碼區塊來建立 DocumentClient 物件。 這份文件中的範例不是使用 Proxy 物件，因此下列範例僅供您參考：

   ```csharp
   HttpClientHandler handler = new HttpClientHandler()
   {
     Proxy = proxyObject
     UseProxy = true,
   };

   //Pass handler to the constructor of DocumentClient.
   DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey, handler);
   
   ```
   
1. 將下列程式碼新增至 `Main` 方法以執行 `GetStartedDemo` 工作。 `Main` 方法會攔截例外狀況並將它們寫入主控台。
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. 按 **F5** 鍵執行您的應用程式。 
   
1. 當您在主控台視窗中看到訊息 [示範結束，按任意鍵退出]  ，表示連線成功。 按任意鍵關閉主控台視窗。 

您已成功連線至您的 Azure Cosmos DB 帳戶。 現在，使用一些 Azure Cosmos DB 資源。  

## <a name="create-a-database"></a>建立資料庫

Azure Cosmos DB [資料庫](databases-containers-items.md#azure-cosmos-databases)是分割給多個集合之 JSON 文件儲存體的邏輯容器。 您可以使用 `DocumentClient` 類別的 [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) 方法建立資料庫。 

1. 在您新增用於建立資料庫的程式碼之前，請新增用於寫入主控台的協助程式方法。 在程式碼中的 `GetStartedDemo` 方法之後複製並貼上下列 `WriteToConsoleAndPromptToContinue` 方法。
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. 將下列這一行複製並貼上到 `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` 行之後的 `GetStartedDemo` 方法中。 此程式碼會建立名為 `FamilyDB` 的資料庫。
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

您已成功建立 Azure Cosmos DB 資料庫。 透過在 Azure Cosmos DB 帳戶左側導覽中選取 [資料總管]  ，可以在 [Azure 入口網站](https://portal.azure.com)中查看資料庫。 

## <a id="CreateColl"></a>建立集合

集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。 您可以使用 `DocumentClient` 類別的 [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) 方法建立集合。 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** 會建立含有保留輸送量且具有價格含意的新集合。 如需詳細資訊，請造訪[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。
> 

1. 將下列程式碼複製並貼上到 `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` 行之後的 `GetStartedDemo` 方法中。 此程式碼會建立名為 `FamilyCollection` 的文件集合。
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

您已成功建立 Azure Cosmos DB 文件集合。 您可以在 Azure 入口網站中，於 [資料總管]  中的 **FamilyDB** 資料庫下查看該集合。  

## <a id="CreateDoc"></a>建立 JSON 文件

文件會是使用者定義的任意 JSON 內容。 文件必須將 ID 屬性序列化為 JSON 中的 `id`。 您可以使用 `DocumentClient` 類別的 [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) 方法建立文件。 

> [!TIP]
> 如果您已經有想要儲存於資料庫中的資料，就可以使用 Azure Cosmos DB 的[資料移轉工具](import-data.md)匯入資料。
>

下列程式碼會建立兩個文件，並插入至您的資料庫集合中。 首先，建立一個 `Family` 類別，以及 `Parent`、`Child`、`Pet` 和 `Address` 子類別，以便在 `Family` 中使用。 然後，建立一個 `CreateFamilyDocumentIfNotExists` 方法，然後建立並插入兩個文件。 

1. 在程式碼中的 `WriteToConsoleAndPromptToContinue` 方法之後複製並貼上下列 `Family`、`Parent`、`Child`、`Pet` 和 `Address` 類別。
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. 在您剛剛新增的 `Address` 類別之後複製並貼上下列 `CreateFamilyDocumentIfNotExists` 方法。
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. 在 `GetStartedDemo` 方法結尾處的 `await client.CreateDocumentCollectionIfNotExistsAsync` 這一行之後，複製並貼上下列程式碼。 此程式碼會建立並插入兩個文件，分別用於 Andersen 和 Wakefield 系列。
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

您已成功建立兩個 Azure Cosmos DB 文件。 您可以在 Azure 入口網站中，於 [資料總管]  中的 **FamilyDB** 資料庫和 **FamilyCollection** 集合下查看該這些文件。   

![說明帳戶、線上資料庫、集合和文件之間階層式關聯性的圖表](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>查詢 Azure Cosmos DB 資源

Azure Cosmos DB 支援針對儲存於集合中的 JSON 文件進行豐富[查詢](how-to-sql-query.md)。 下列範例程式碼會使用 LINQ 和 Azure Cosmos DB SQL 語法對範例文件執行查詢。

1. 在程式碼中的 `CreateFamilyDocumentIfNotExists` 方法之後複製並貼上下列 `ExecuteSimpleQuery` 方法。
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. 在 `GetStartedDemo` 方法結尾處的 `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` 這一行之後，複製並貼上下列程式碼。
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

上述查詢傳回 Andersen 系列的完整項目。 您已成功查詢 Azure Cosmos DB 集合。

下圖說明 Azure Cosmos DB SQL 查詢語法如何針對集合呼叫。 相同邏輯也適用於 LINQ 查詢。

![說明 NoSQL 教學課程用來建立 C# 主控台應用程式之查詢的範圍和意義的圖表](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

因為 Azure Cosmos DB 查詢已侷限於單一集合，所以 SQL 查詢中的 [FROM](how-to-sql-query.md#FromClause) 關鍵字是選擇性的。 您可以將 `FROM Families f` 與 `FROM root r` 或您選擇的任何其他變數名稱交換。 依預設，Azure Cosmos DB 會推斷該 `Families`、`root` 或您選擇的變數名稱來參考目前的集合。

## <a id="ReplaceDocument"></a>更新 JSON 文件

Azure Cosmos DB SQL API 支援更新和更換 JSON 文件。  

1. 在程式碼中的 `ExecuteSimpleQuery` 方法之後複製並貼上下列 `ReplaceFamilyDocument` 方法。
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. 在 `GetStartedDemo` 方法結尾處的 `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` 這一行之後，複製並貼上下列程式碼。 程式碼會更新其中一個文件中的資料，然後再次執行查詢以顯示已變更的文件。
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

查詢輸出顯示 Andersen 系列的子系 `Grade` 從 `5` 更新為 `6`。 您已成功更新並取代 Azure Cosmos DB 文件。 

## <a id="DeleteDocument"></a>刪除 JSON 文件

Azure Cosmos DB SQL API 支援刪除 JSON 文件。  

1. 在 `ReplaceFamilyDocument` 方法之後複製並貼上下列 `DeleteFamilyDocument` 方法。
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. 在 `GetStartedDemo` 方法結尾處的第二個 `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` 行之後，複製並貼上下列程式碼。
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

您已成功刪除 Azure Cosmos DB 文件。 

## <a id="DeleteDatabase"></a>刪除資料庫

刪除您所建立的資料庫以移除它及其所有子資源，包括集合和文件。 

1. 在 `GetStartedDemo` 方法結尾處的 `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` 這一行之後，複製並貼上下列程式碼。 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. 按 **F5** 鍵執行您的應用程式。

您已成功刪除 Azure Cosmos DB 資料庫。 您可以在 [資料總管]  中看到 Azure Cosmos DB 帳戶中已刪除 FamilyDB 資料庫。 

## <a id="Run"></a>執行整個 C# 主控台應用程式

在 Visual Studio 中，按 **F5** 鍵即可在偵錯模式下建置和執行完整的 C# 主控台應用程式。 您應該會在主控台視窗中看到下列輸出：

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

恭喜！ 您已完成此教學課程，並擁有一個運作中的 C# 主控台應用程式，用於建立、查詢、更新和刪除 Azure Cosmos DB 資源。  

## <a name="next-steps"></a>後續步驟
* 若要深入了解 Azure Cosmos DB，請參閱[歡迎使用 Azure Cosmos DB](introduction.md)。
* 如需更複雜的 ASP.NET MVC 教學課程，請參閱 [ASP.NET MVC 教學課程：使用 Azure Cosmos DB 進行 Web 應用程式開發](sql-api-dotnet-application.md)。
* 若要執行 Azure Cosmos DB 的相關規模和效能測試，請參閱 [Azure Cosmos DB 的相關效能和規模測試](performance-testing.md)。
* 若要了解如何監視 Azure Cosmos DB 要求、使用量及儲存體，請參閱[監視帳戶](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo) \(英文\) 中，針對範例資料集執行查詢。

