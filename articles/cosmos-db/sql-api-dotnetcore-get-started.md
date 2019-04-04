---
title: 教學課程：建置 .NET Core 應用程式來管理 Azure Cosmos DB 的 SQL API 帳戶中儲存的資料
description: 本教學課程會使用 Azure Cosmos DB 的 SQL API .NET Core SDK，建立線上資料庫和 C# 主控台應用程式。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 81c80dbd7ffa8e5c4e7b2ebb1c9d17eb6007ae9f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802346"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>教學課程：建置 .NET Core 應用程式來管理 SQL API 帳戶中儲存的資料

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (預覽)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (預覽)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [非同步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

身為開發人員，您可能有使用 NoSQL 文件資料的應用程式。 您可以在 Azure Cosmos DB 中使用 SQL API 帳戶來儲存及存取此文件資料。 本教學課程說明如何建置 .NET Core 應用程式來建立及查詢 Azure Cosmos DB 的 SQL API 帳戶中儲存的資料。 

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立及連線至 Azure Cosmos 帳戶
> * 設定 Visual Studio 解決方案
> * 建立線上資料庫
> * 建立集合
> * 建立 JSON 文件
> * 對項目、容器和資料庫執行 CRUD 作業

沒有時間建立應用程式嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)上找到完整的方案。 

## <a name="prerequisites"></a>必要條件

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 下載並使用免費的 [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 如果您正在開發通用 Windows 平台 (UWP) 應用程式，則應該使用 **15.4 版或更高版本的 Visual Studio 2017**。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

    * 對於 MacOS 或 Linux，您可以針對所選的平台安裝 [.NET Core SDK](https://www.microsoft.com/net/core#macos)，以從命令列開發 .NET Core 應用程式。 

    * 對於 Windows，您可以安裝 [.NET Core SDK](https://www.microsoft.com/net/core#windows) 以從命令列開發 .NET Core 應用程式。 

## <a name="create-an-azure-cosmos-account"></a>建立 Azure Cosmos 帳戶

使用下列步驟來建立 Azure Cosmos 帳戶：

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>設定您的 Visual Studio 方案

1. 在電腦上開啟 **Visual Studio 2017**。

2. 從 [檔案] 功能表中，選取 [新增]，然後選擇 [專案]。

3. 在 [新增專案] 對話方塊中，依序選取 [範本] > [Visual C#] > [.NET Core] > [主控台應用程式 (.NET Core)]、將專案命名為 **DocumentDBGettingStarted**，然後選取 [確定]。

   ![[新增專案] 視窗的螢幕擷取畫面](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. 在 [方案總管] 中，以滑鼠右鍵按一下 [DocumentDBGettingStarted]。

5. 在相同功能表上，選取 [管理 NuGet 套件]。

   ![專案的滑鼠右鍵功能表螢幕擷取畫面](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. 在 [NuGet] 索引標籤上，選取視窗頂端的 [瀏覽]，然後在搜尋方塊中輸入 **azure documentdb**。 確定已核取 [包含發行前版本] 核取方塊。

7. 在結果中尋找 [Microsoft.Azure.DocumentDB.Core]，然後選取 [安裝]。

   程式庫的封裝識別碼為 [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)。 如果您是以此 .NET Core NuGet 套件不支援的 .NET Framework 版本 (例如 net461) 為目標，則使用 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)，其支援 .NET Framework 4.5 以後的所有 .NET Framework 版本。

8. 出現提示時，接受 NuGet 套件安裝和授權合約。

現在安裝程式已完成，讓我們開始撰寫一些程式碼。 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) 上找到本教學課程的完整程式碼專案。

## <a id="Connect"></a>連線至 Azure Cosmos 帳戶

匯入必要的相依項目，以連線到 Azure Cosmos 帳戶。 若要匯入必要的相依項目，請將下列程式碼新增到 Program.cs 檔案的開頭：

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

接著，在公用類別 Program 之下新增下列兩個常數和您的 client 變數。

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

接下來，前往 [Azure 入口網站](https://portal.azure.com)來擷取您的 URI 和主要金鑰。 您必須提供 Azure Cosmos DB URI 和主要金鑰，您的應用程式才能了解要連接的位置，而 Azure Cosmos DB 才會信任您的應用程式連接。

在 Azure 入口網站中，移至 Azure Cosmos 帳戶，然後選取 [金鑰]。

從入口網站複製 URI，並將它貼到 program.cs 檔案的 `<your endpoint URI>` 中。 然後從入口網站複製主要金鑰，並將它貼到 `<your key>`中。 請務必移除 < 和 > 但保留端點和金鑰的雙引號。

![從 Azure 入口網站取得金鑰][keys]

讓我們建立 **DocumentClient**的新執行個體，啟動快速入門應用程式。

在 **Main** 方法底下，新增名為 **GetStartedDemo** 的新非同步工作，以將新的 **DocumentClient** 具現化。

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

加入下列程式碼，以從 **Main** 方法執行非同步工作。 **Main** 方法會攔截例外狀況並將它們寫入主控台。

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

選取 [DocumentDBGettingStarted] 按鈕以建置並執行應用程式。

## <a id="CreateDatabase"></a>建立資料庫

在您新增用於建立資料庫的程式碼之前，請新增用於寫入主控台的協助程式方法。 複製 **WriteToConsoleAndPromptToContinue** 方法並貼到 **GetStartedDemo** 方法之下。

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

使用 **DocumentClient** 類別的 `CreateDatabaseAsync` 方法來建立 Azure Cosmos DB 資料庫。 資料庫是分割給多個集合之 JSON 文件儲存體的邏輯容器。 複製下列程式碼並貼到 **GetStartedDemo** 方法的用戶端建立之下。 這會建立名為 *FamilyDB* 的資料庫。

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

## <a id="CreateColl"></a>建立集合

使用 **DocumentClient** 類別的 `CreateDocumentCollectionAsync` 方法來建立集合。 集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。

> [!WARNING]
> **CreateDocumentCollectionAsync** 會建立含有保留輸送量且具有價格含意的新集合。 如需詳細資訊，請移至[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。

複製下列程式碼並貼到 **GetStartedDemo** 方法的資料庫建立之下。 此程式碼會建立名為 *FamilyCollection_oa* 的文件集合。

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

## <a id="CreateDoc"></a>建立 JSON 文件

使用 **DocumentClient** 類別的 `CreateDocumentAsync` 方法來建立文件。 文件會是使用者定義的 (任意) JSON 內容。 您現在可以插入一或多份文件。 

首先，建立 **Family** 類別，以代表在 Azure Cosmos DB 內儲存的物件。 您也會建立 **Family** 內使用的 **Parent**、**Child**、**Pet** 和 **Address** 子類別。 文件必須將 **Id** 屬性序列化為 JSON 中的**識別碼**。 藉由在 **GetStartedDemo** 方法之後加入下列內部子類別來建立這些類別。 複製 **Family**、**Parent**、**Child**、**Pet** 和 **Address** 類別並貼到 **WriteToConsoleAndPromptToContinue** 方法之下。

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

複製 **CreateFamilyDocumentIfNotExists** 方法並貼到 **CreateDocumentCollectionIfNotExists** 方法之下。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

插入兩個文件，一個給 Andersen 家族，另一個給 Wakefield 家族。 複製 `// ADD THIS PART TO YOUR CODE` 後面的程式碼並貼到 **GetStartedDemo** 方法的文件集合建立之下。

```csharp
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

![帳戶、線上資料庫和集合之間的階層式關聯性](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>查詢 Azure Cosmos DB 資源

Azure Cosmos DB 支援針對儲存於每個集合的 JSON 文件進行豐富查詢。 下列範例程式碼示範各種查詢，同時使用可在我們於前一個步驟中所插入文件上執行的 Azure Cosmos DB SQL 語法和 LINQ。

複製 **ExecuteSimpleQuery** 方法並貼到 **CreateFamilyDocumentIfNotExists** 方法之下。

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

複製下列程式碼並貼到 **GetStartedDemo** 方法的次要文件建立之下。

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

下圖說明如何針對您所建立的集合呼叫 Azure Cosmos DB SQL 查詢語法。 相同邏輯也適用於 LINQ 查詢。

![說明 NoSQL 教學課程用來建立 C# 主控台應用程式之查詢的範圍和意義的圖表](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

因為 Azure Cosmos DB 查詢已侷限於單一集合，所以查詢中的 `FROM` 關鍵字是選擇性的。 因此，"FROM Families f" 可以換成 "FROM root r"，或您選擇的任何其他變數名稱。 依預設，Azure Cosmos DB 會推斷 Family、Root 或您選擇的變數名稱來參考目前的集合。

## <a id="ReplaceDocument"></a>取代 JSON 文件

Azure Cosmos DB 支援取代 JSON 文件。  

複製 **ReplaceFamilyDocument** 方法並貼到 **ExecuteSimpleQuery** 方法之下。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

複製下列程式碼並貼到 **GetStartedDemo** 方法的查詢執行之下。 取代文件後，此程式碼會再次執行相同的查詢以檢視變更後的文件。

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

## <a id="DeleteDocument"></a>刪除 JSON 文件

Azure Cosmos DB 支援刪除 JSON 文件。  

複製 **DeleteFamilyDocument** 方法並貼到 **ReplaceFamilyDocument** 方法之下。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

複製下列程式碼並貼到 **GetStartedDemo** 方法的次要查詢執行之下。

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

## <a id="DeleteDatabase"></a>刪除資料庫

刪除已建立的資料庫將會移除資料庫和所有子系資源 (集合、文件等)。 將下列程式碼複製並貼到文件之下的 **GetStartedDemo** 方法，以刪除整個資料庫和所有子資源。

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

選取 [DocumentDBGettingStarted] 按鈕以執行應用程式。

## <a id="Run"></a>執行您的 C# 主控台應用程式

在 Visual Studio 中選取 [DocumentDBGettingStarted] 按鈕，以在偵錯模式中建置應用程式。 您應該可以看到在主控台視窗中開始使用應用程式的輸出。 輸出將會顯示新增的查詢結果，而且應該符合以下的範例文字。

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

您現在已經完成本教學課程，並擁有運作中的 C# 主控台應用程式。

## <a name="clean-up-resources"></a>清除資源

若不再需要資源，您可以刪除資源群組、Azure Cosmos 帳戶和所有相關資源。 請選取虛擬機器的資源群組，選取 [刪除]，然後確認要刪除的資源群組名稱，即可刪除資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建置 .NET Core 應用程式來管理 Azure Cosmos DB 的 SQL API 帳戶中儲存的資料。 您現在可以繼續進行下一篇文章：

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 的 SQL API 帳戶建置 Java 主控台應用程式](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
