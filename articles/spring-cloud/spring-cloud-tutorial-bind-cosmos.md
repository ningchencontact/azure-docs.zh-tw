---
title: 將 Azure Cosmos DB 繫結至 Azure Spring Cloud 應用程式 | Microsoft Docs
description: 了解如何將 Azure Cosmos DB 繫結至 Azure Spring Cloud 應用程式
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607123"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>教學課程：將 Azure Cosmos DB 繫結至 Azure Spring Cloud 應用程式

Azure Spring Cloud 可讓您將特定的 Azure 服務自動繫結至您的應用程式，而不需要手動設定您的 Spring Boot 應用程式。 本文會示範如何將您的應用程式繫結至 Azure Cosmos DB。

必要條件：
* 已部署的 Azure Spring Cloud 執行個體。  請遵循我們的[快速入門](spring-cloud-quickstart-launch-app-cli.md)以開始使用。
* 具有參與者最低權限水準的 Azure Cosmos DB 帳戶。

## <a name="bind-azure-cosmos-db"></a>繫結 Azure Cosmos DB

Azure Cosmos DB 有五個不同的 API 類型可支援繫結：

1. 建立 Azure Cosmos DB 資料庫。 如需建立資料庫的協助，請[參閱這篇文章](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)。 記錄資料庫的名稱。 我們的資料庫名稱是 `testdb`。

1. 根據您的 API 類型，在您的 Spring Cloud 應用程式 `pom.xml` 中新增下列其中一個相依性。
    
    #### <a name="api-type-core-sql"></a>API 類型：Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API 類型：MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API 類型：Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API 類型：Gremlin (圖形)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API 類型：Azure 資料表

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. 使用 `az spring-cloud app update` 來更新目前的部署，或是使用 `az spring-cloud app deployment create` 來為此變更建立新部署。  這些命令會搭配新的相依性來更新或建立應用程式。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。 這是您在上一個步驟中更新或部署的應用程式。 尋找 [應用程式儀表板]  ，然後選取要繫結至 Cosmos DB 的應用程式。 接下來，請選取 `Service binding` 並選取 `Create service binding` 按鈕。 填寫表單，選取 [繫結類型]  (`Azure Cosmos DB`)、API 類型、您的資料庫名稱，以及 Azure Cosmos DB 帳戶。

    > [!NOTE]
    > 如果您使用 Cassandra，請為資料庫名稱使用 Keyspace。

1. 選取應用程式頁面上的 [重新啟動]  按鈕，以重新啟動應用程式。

1. 為確保服務的繫結是否正確，請選取繫結名稱並確認其詳細資料。 `property` 欄位應如下所示：

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將 Azure Spring Cloud 應用程式繫結至 Cosmos DB。  若要了解如何將應用程式繫結至 Azure Redis 快取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [將 Azure Spring Cloud 應用程式繫結至 Azure Redis 快取](spring-cloud-tutorial-bind-redis.md)。
