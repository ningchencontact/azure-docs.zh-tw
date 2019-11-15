---
title: 使用 Java 建立文件資料庫 - Azure Cosmos DB
description: 提供 Java 程式碼範例，您可用來連線及查詢 Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d5a32780f8598c0843958b99f02cd18aa33bea2e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582854"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

本快速入門說明如何使用 Java 應用程式，從您的 Azure Cosmos DB SQL API 帳戶建立和管理文件資料庫。 首先，您必須使用 Azure 入口網站建立 Azure Cosmos DB SQL API 帳戶、使用 SQL Java SDK 建立 Java 應用程式，然後使用 Java 應用程式將資源新增至您的 Cosmos DB 帳戶。 本快速入門中的指示可運用在任何足以執行 Java 應用程式的作業系統上。 完成本快速入門之後，您就會熟悉如何在 UI 中或以程式設計方式建立和修改 Cosmos DB 資料庫和容器 (不論您偏好哪種方式)。

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
    git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-app)。 

* `CosmosClient` 初始化。 `CosmosClient` 提供適用於 Azure Cosmos 資料庫服務的用戶端邏輯表示法。 此用戶端會用於設定和執行針對服務的要求。
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* 建立 CosmosDatabase。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* 建立 CosmosContainer。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* 使用 `createItem` 方法建立項目。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* 端點讀取是使用 `getItem` 和 `read` 方法來執行

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* 使用 `queryItems` 方法，透過 JSON 執行 SQL 查詢。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>執行應用程式

現在，返回 Azure 入口網站以取得連接字串資訊，然後使用端點資訊啟動應用程式。 這可讓您的應用程式與託管資料庫進行通訊。


1. 在 git 終端機視窗中，`cd` 至範例程式碼資料夾。

    ```bash
    cd azure-cosmos-java-getting-started
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
    
4. 此應用程式會建立名稱為 `AzureSampleFamilyDB` 的資料庫
5. 此應用程式會建立名稱為 `FamilyContainer` 的容器
6. 此應用程式會使用物件識別碼和分割索引鍵值 (在我們的範例中為 lastName) 來執行端點讀取。 
7. 此應用程式會查詢項目，以擷取姓氏屬於 ('Andersen', 'Wakefield', 'Johnson') 的所有家族

7. 應用程式並不會刪除已建立的資源。 切換回入口網站，從您的帳戶中[清除資源](#clean-up-resources)，  以免產生費用。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 [資料總管] 來建立 Azure Cosmos 帳戶、文件資料庫和容器，以及如何執行應用程式來以程式設計方式執行同樣的作業。 您現可將其他資料匯入至 Azure Cosmos 容器。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)
