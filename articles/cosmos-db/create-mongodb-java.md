---
title: 快速入門：使用適用於 MongoDB 的 API 和 Java SDK 建置 Web 應用程式 - Azure Cosmos DB
description: 呈現 Java 程式碼範例，您可使用 Azure Cosmos DB 適用於 MongoDB 的 API 進行連線及查詢。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: bfded407790d01d65feec0b6e069c23739064e97
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756928"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>快速入門：在 Azure Cosmos DB 中使用 Java 和 MongoDB API 建立主控台應用程式

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

在本快速入門中，您會使用適用於 Mongo DB 和 Java SDK 的 Azure Cosmos DB API 來建立主控台 Web 應用程式。 Azure Cosmos DB 可讓您快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Cosmos DB 核心的全域散發和水平調整功能。

本快速入門示範如何使用 [Azure Cosmos DB 適用於 MongoDB 的 API](mongodb-introduction.md) 來建立 Cosmos 帳戶。 您會接著使用 [MongoDB Java 驅動程式](https://docs.mongodb.com/ecosystem/drivers/java/)建置和部署主控台應用程式。 

## <a name="prerequisites"></a>必要條件

您必須具備下列必要條件，才能執行此範例：
* [安裝 JDK for Azure 和 Azure Stack JDK 8 版](https://aka.ms/azure-jdks)
* Maven (如果您沒有 Maven 則執行 `apt-get install maven`)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>新增集合

將新資料庫命名為 **db**，以即將新集合命名為 **coll**。

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 然後在您慣用的編輯器中開啟程式碼。 

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。 

下列程式碼片段全部取自 Program.java 檔案。

* 已初始化 DocumentClient。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 已建立新的資料庫和集合。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* 已使用 `MongoCollection.insertOne` 插入一些文件

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* 已使用 `MongoCollection.find` 執行一些查詢

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 從 [帳戶] 選取 [快速入門]  ，選取 [Java]  ，然後將連接字串複製到剪貼簿。

2. 開啟 `Program.java` 檔案，以連接字串取代 MongoClientURI 建構函式的引數。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 
    
## <a name="run-the-console-app"></a>執行主控台應用程式

1. 在終端機中執行 `mvn package` 以安裝必要的 npm 模組

2. 在終端機中執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 來啟動您的 Java 應用程式。

您現在可以使用 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) 來查詢、修改及處理這項新資料。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Cosmos 帳戶、建立集合，以及執行主控台應用程式。 您現在可以將其他資料匯入到 Cosmos 資料庫。

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](mongodb-migrate.md)
