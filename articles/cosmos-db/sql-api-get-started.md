---
title: 建置 .NET 主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶中的資料
description: 了解如何使用 C# 主控台應用程式建立 Azure Cosmos DB SQL API 資源。
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: kirankk
ms.openlocfilehash: 6fd7efe38aeb1f1094d240cf1675d432f3766229
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985713"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>建置 .NET 主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶中的資料

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [非同步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

歡迎使用 Azure Cosmos DB SQL API 入門教學課程。 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 Azure Cosmos DB 資源。 本教學課程使用 [3.0+ 版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)的 Azure Cosmos DB .NET SDK，其以 [.NET Framework](https://dotnet.microsoft.com/download) 或 [.NET Core](https://dotnet.microsoft.com/download) 作為目標。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> * 建立及連線至 Azure Cosmos 帳戶
> * 在 Visual Studio 中設定專案
> * 建立資料庫和容器
> * 將項目新增至容器
> * 查詢容器
> * 在項目上進行 CRUD 作業
> * 刪除資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)上找到完整的方案。 請跳至[取得完整的教學課程解決方案](#GetSolution)一節，以取得簡要指示。

讓我們開始吧！

## <a name="prerequisites"></a>必要條件

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步驟 1：建立 Azure Cosmos DB 帳戶
讓我們來建立 Azure Cosmos DB 帳戶。 如果您已經擁有想要使用的帳戶，就可以跳到 [設定您的 Visual Studio 方案](#SetupVS)。 如果您是使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Visual Studio 專案](#SetupVS)。

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>步驟 2：設定 Visual Studio 專案
1. 在電腦上開啟 **Visual Studio 2017**。
1. 從 [檔案]  功能表中，選取 [新增]  ，然後選擇 [專案]  。
1. 在 [新增專案]   對話方塊中，選取 [Visual C#]   / [主控台應用程式 (.NET Framework)]  、為專案命名，然後按一下 [確定]  。
    ![[新增專案] 視窗的螢幕擷取畫面](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > 針對 .NET Core 目標，在 [新增專案]   對話方塊中，選取 [Visual C#]   / [主控台應用程式 (.NET Core)]  **、為專案命名，然後按一下 [確定]**

1. 在 [方案總管]  中，以滑鼠右鍵按一下 Visual Studio 方案底下的新主控台應用程式，然後按一下 [管理 NuGet 套件...] 

    ![專案滑鼠右鍵功能表的螢幕擷取畫面](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. 在 [NuGet]  索引標籤中按一下 [瀏覽]  ，然後在搜尋方塊中輸入 **Microsoft.Azure.Cosmos**。
1. 在結果中尋找 **Microsoft.Azure.Cosmos**，然後按一下 [安裝]  。
   「Azure Cosmos DB SQL API 用戶端程式庫」的套件識別碼是 [Microsoft Azure Cosmos DB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) \(英文\)。
   ![用於尋找 Azure Cosmos DB 用戶端 SDK 的 NuGet 功能表螢幕擷取畫面](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    如果您收到關於檢閱方案變更的訊息，請按一下 [確定]  。 如果您收到關於接受授權的訊息，請按一下 [我接受]  。

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。 您可以在 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)找到本教學課程的完整程式碼專案。

## <a id="Connect"></a>步驟 3：連線至 Azure Cosmos DB 帳戶
1. 首先，在 **Program.cs** 檔案中，將 C# 應用程式開頭的參考，替換為下列參考：

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. 現在，將下列常數和變數新增至公用類別 ``Program``。

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    請注意，如果您熟悉舊版 .NET SDK，則可能已習慣看到「集合」和「文件」等詞彙。 Azure Cosmos DB 支援多個 API 模型，因此 3.0+ 版的 .NET SDK 會使用「容器」和「項目」這些泛用詞彙。 容器可以是集合、圖形或資料表。 項目可以是文件、邊緣/頂點或資料列，並且是容器內的內容。 [深入了解資料庫、容器和項目。](databases-containers-items.md)

1. 從 [Azure 入口網站](https://portal.azure.com)擷取端點 URL 和主要金鑰。

    在 Azure 入口網站中，瀏覽至 Azure Cosmos DB 帳戶，然後按一下 [金鑰]  。

    從入口網站複製 URI，並將它貼到 ```Program.cs``` 檔案的 `<your endpoint URL>` 中。 從入口網站複製主要金鑰，並將它貼到 `<your primary key>` 中。

   ![從 Azure 入口網站取得 Azure Cosmos DB 金鑰的螢幕擷取畫面](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. 接下來，我們會建立 ```CosmosClient``` 的新執行個體，並為我們的計劃設定一些 Scaffolding。

    在 **Main** 方法底下，新增名為 **GetStartedDemoAsync** 的新非同步工作，以將新的 ```CosmosClient``` 具現化。 我們會使用 **GetStartedDemoAsync** 作為進入點，來呼叫方法以操作 Azure Cosmos DB 資源。

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

1. 新增下列程式碼，以從 **Main** 方法執行 **GetStartedDemoAsync** 非同步工作。 **Main** 方法會攔截例外狀況並將它們寫入主控台。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. 選取 **F5** 鍵執行您的應用程式。 主控台視窗輸出會顯示 `End of demo, press any key to exit.` 訊息，確認已連線至 Azure Cosmos DB。 您可以接著關閉主控台視窗。

恭喜！ 您已成功連線至 Azure Cosmos DB 帳戶。 

## <a name="step-4-create-a-database"></a>步驟 4：建立資料庫
您可以使用 ``CosmosClient`` 類別的 [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) 或 [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) 函式來建立資料庫。 資料庫是分割於多個容器之項目的邏輯容器。

1. 將 **CreateDatabase** 方法複製並貼到 **GetStartedDemoAsync** 方法底下。 **CreateDatabaseAsync** 會以識別碼 ``FamilyDatabase`` 建立新的資料庫 (如果該資料庫不存在)，此識別碼會指定於 ``databaseId`` 欄位中。 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. 將程式碼複製並貼到 CosmosClient 具現化時的所在處底下，以呼叫您剛才新增的 **CreateDatabaseAsync** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    此時，您的程式碼應該會有如下樣貌，且其中已填入您的端點和主要金鑰。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功建立 Azure Cosmos DB 資料庫。  

## <a id="CreateColl"></a>步驟 5：建立容器
> [!WARNING]
> 呼叫 **CreateContainerIfNotExistsAsync** 方法會建立具有定價含意的新容器。 如需詳細資訊，請造訪 [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。
>
>

容器可使用 **CosmosDatabase** 類別的 [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) 或 [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) 函式來建立。 容器中包含項目 (在 SQL API 中為 JSON 文件) 和 JavaScript 中相關聯的伺服器端應用程式邏輯，例如預存程序、使用者定義的函式和觸發程序。

1. 將 **CreateContainerAsync** 方法複製並貼到 **CreateDatabaseAsync** 方法底下。 **CreateContainerAsync** 會以識別碼 ``FamilyContainer`` 建立新的容器 (如果該容器不存在)，此識別碼會指定於由 ``LastName`` 屬性分割的 ``containerId`` 欄位中。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. 將程式碼複製並貼到 CosmosClient 具現化時的所在處底下，以呼叫您剛才新增的 **CreateContainer** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功建立 Azure Cosmos DB 容器。  

## <a id="CreateDoc"></a>步驟 6：將項目新增至容器
項目可以使用 **CosmosContainer** 類別的 [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) 函式來建立。 使用 SQL API 時，預期的項目會是文件，這是使用者定義的 (任意) JSON 內容。 您現在可以將項目插入 Azure Cosmos DB 容器中。

首先，我們來建立 **Family** 類別，以代表此範例中儲存在 Azure Cosmos DB 內的物件。 我們也會建立 **Family** 內使用的 **Parent**、**Child**、**Pet**、**Address** 子類別。 請注意，項目必須將 **Id** 屬性序列化為 JSON 中的**識別碼**。

1. 選取 [Ctrl+Shift+A]  以開啟 [新增項目]  對話方塊。 將新的類別 **Family.cs** 新增至您的專案。

    ![將新的 Family.cs 類別新增至專案的螢幕擷取畫面](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. 將 **Family**、**Parent**、**Child**、**Pet** 和 **Address** 類別複製並貼到 **Family.cs** 中。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. 瀏覽回到 **Program.cs**，然後將 **AddItemsToContainerAsync** 方法新增至 **CreateContainerAsync** 方法底下。
程式碼會先檢查，確認沒有使用相同識別碼的項目存在後，再建立該項目。 我們會插入兩個項目，一個給 Andersen 家族，另一個給 Wakefield 家族。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``AddItemsToContainerAsync`` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功建立兩個 Azure Cosmos DB 項目。  

## <a id="Query"></a>步驟 7：查詢 Azure Cosmos DB 資源
Azure Cosmos DB 支援針對儲存於每個集合的 JSON 文件進行豐富[查詢](sql-api-sql-query.md)。 下列範例程式碼示範如何對上一個步驟中插入的項目執行查詢。

1. 將 **QueryItemsAsync** 方法複製並貼到 **AddItemsToContainerAsync** 方法底下。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``QueryItemsAsync`` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功查詢 Azure Cosmos DB 容器。

## <a id="ReplaceItem"></a>步驟 8：取代 JSON 項目
現在，我們會更新 Azure Cosmos DB 中的項目。

1. 將 **ReplaceFamilyItemAsync** 方法複製並貼到 **QueryItemsAsync** 方法底下。 請注意，我們會變更 Family 的 ``IsRegistered`` 屬性，以及其中一個子系的 ``Grade``。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``ReplaceFamilyItemAsync`` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功取代 Azure Cosmos DB 項目。

## <a id="DeleteDocument"></a>步驟 9：刪除項目
現在，我們將刪除 Azure Cosmos DB 中的項目。

1. 將 **DeleteFamilyItemAsync** 方法複製並貼到 **ReplaceFamilyItemAsync** 方法底下。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``DeleteFamilyItemAsync`` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功刪除 Azure Cosmos DB 項目。

## <a id="DeleteDatabase"></a>步驟 10：刪除資料庫
現在我們會刪除資料庫。 刪除已建立的資料庫將會移除資料庫和所有子系資源 (容器、項目，以及任何預存程序、使用者定義的函式和觸發程序)。 我們也會處置 **CosmosClient** 執行個體。

1. 將 **DeleteDatabaseAndCleanupAsync** 方法複製並貼到 **DeleteFamilyItemAsync** 方法底下。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``DeleteDatabaseAndCleanupAsync`` 的呼叫。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

選取 **F5** 鍵執行您的應用程式。

恭喜！ 您已成功刪除 Azure Cosmos DB 資料庫。

## <a id="Run"></a>步驟 11：一起執行您的 C# 主控台應用程式！
在 Visual Studio 中選取 F5，即可在偵錯模式下建置和執行應用程式。

您應該會在主控台視窗中看到整個應用程式的輸出。 輸出將會顯示新增的查詢結果，而且應該符合以下的範例文字。

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

恭喜！ 您已經完成本教學課程，並擁有運作中的 C# 主控台應用程式！

## <a id="GetSolution"></a>取得完整的教學課程解決方案
如果您沒有時間完成本教學課程中的步驟，或只想要下載程式碼範例，您可以從 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 取得程式碼。 

若要建置 GetStarted 方案，您將需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。
* [Azure Cosmos DB 帳戶][cosmos-db-create-account]。
* 您可以在 GitHub 上找到 [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 方案。

若要在 Visual Studio 中還原 Azure Cosmos DB .NET SDK 的參考，請在 [方案總管] 的 **GetStarted** 方案上按一下滑鼠右鍵，然後按一下 [還原 NuGet 套件]  。 接下來，在 App.config 檔案中更新 EndPointUri 和 PrimaryKey 值，如[連線至 Azure Cosmos DB 帳戶](#Connect)中所述。

建置就這麼容易，繼續努力！

## <a name="next-steps"></a>後續步驟
* 需要更複雜的 ASP.NET MVC 教學課程嗎？ 請參閱 [ASP.NET MVC 教學課程：使用 Azure Cosmos DB 進行 Web 應用程式開發](sql-api-dotnet-application-preview.md)。
* 需要使用 Azure Cosmos DB 來執行規模和效能測試嗎？ 請參閱 [Azure Cosmos DB 的效能和級別測試](performance-testing.md)
* 了解如何[監視 Azure Cosmos DB 要求、使用量及儲存體](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中，針對範例資料集執行查詢。
* 若要深入了解 Azure Cosmos DB，請參閱[歡迎使用 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)。

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
