---
title: 使用 Java 建立文件資料庫 - Azure Cosmos DB
description: 提供 Java 程式碼範例，您可用來連線及查詢 Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.custom: seo-java-august2019
ms.openlocfilehash: 110c4a343909a81e092b147b79ed69d6bd5c2d9c
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981760"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

本快速入門說明如何使用 Java 應用程式來建立和管理 Azure Cosmos DB [SQL API](sql-api-introduction.md) 帳戶的資源。 首先，您必須使用 Azure 入口網站建立 Azure Cosmos DB SQL API 帳戶、使用 [SQL Java SDK](sql-api-sdk-async-java.md) 建立 Java 應用程式，並使用 Java 應用程式將資源新增至您的 Cosmos DB 帳戶。 本快速入門中的指示可運用在任何足以執行 Java 應用程式的作業系統上。 完成本快速入門之後，您就會熟悉如何在 UI 中或以程式設計方式建立和修改 Cosmos 資料庫和容器 (不論您偏好哪種方式)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

此外： 

* [JAVA 開發套件 (JDK) 8 版](https://aka.ms/azure-jdks) \(英文\)
    * 務必設定 JAVA_HOME 環境變數，以指向 JDK 安裝所在的資料夾。
* [下載](https://maven.apache.org/download.cgi)和[安裝 ](https://maven.apache.org/install.html) [Maven](https://maven.apache.org/) 二進位封存檔
    * 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。
* [Git](https://www.git-scm.com/)
    * 在 Ubuntu 上，您可以執行 `sudo apt-get install git` 來安裝 Git。

## <a name="create-a-database-account"></a>建立資料庫帳戶

您必須先使用 Azure Cosmos DB 建立 SQL API 帳戶，才可以建立文件資料庫。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>新增容器

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>新增範例資料

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查詢資料

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 我們將從 GitHub 複製 SQL API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-app)。 

* `AsyncDocumentClient` 初始化。 [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) 提供適用於 Azure Cosmos 資料庫服務的用戶端邏輯表示法。 此用戶端會用於設定和執行針對服務的要求。

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* [資料庫](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database)建立。

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection) 建立。

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* 使用 [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document) 方法建立文件。

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* 使用 [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable) 方法，透過 JSON 執行 SQL 查詢。

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>執行應用程式

現在，返回 Azure 入口網站以取得連接字串資訊，然後使用端點資訊啟動應用程式。 這可讓您的應用程式與託管資料庫進行通訊。


1. 在 git 終端機視窗中，`cd` 至範例程式碼資料夾。

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. 在 git 終端機視窗中，使用下列命令來安裝必要的 Java 套件。

    ```bash
    mvn package
    ```

3. 在 git 終端機視窗中，使用下列命令啟動 Java 應用程式 (請將 YOUR_COSMOS_DB_HOSTNAME 取代為入口網站中加上引號的 URI 值，並將 YOUR_COSMOS_DB_MASTER_KEY 取代為入口網站中加上引號的主要金鑰)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    終端機視窗會顯示已建立 FamilyDB 資料庫的通知。 
    
4. 請按任一按鍵來建立資料庫，然後按下另一個按鍵來建立集合。 

    切換回瀏覽器中的資料總管，您會看到資料總管中現在已包含 FamilyDB 資料庫和 FamilyCollection 集合。

5. 切換至 [主控台] 視窗，然後按下任一按鍵來建立第一個文件，然後按下另一個按鍵以建立第二個文件。 然後切換回資料總管以檢視這些文件。 

6. 按下任一按鍵以執行查詢，並在 [主控台] 視窗中查看輸出。 

7. 應用程式並不會刪除已建立的資源。 切換回入口網站，從您的帳戶中[清除資源](#clean-up-resources)，  以免產生費用。

    ![主控台輸出](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 [資料總管] 來建立 Azure Cosmos 帳戶、文件資料庫和容器，以及如何執行應用程式來以程式設計方式執行同樣的作業。 您現可將其他資料匯入至 Azure Cosmos 容器。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)
