---
title: 將 Azure Cosmos DB 系結至您的 Azure 春季雲端應用程式 |Microsoft Docs
description: 瞭解如何將 Azure Cosmos DB 系結至您的 Azure 春季雲端應用程式
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038726"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>教學課程：將 Azure Cosmos DB 系結至您的 Azure 春季雲端應用程式

Azure 春季雲端可讓您自動將選取的 Azure 服務系結至您的應用程式，而不是手動設定您的彈簧開機應用程式。 本文示範如何將您的應用程式系結至 Azure Cosmos DB。

必要條件：
* 已部署的 Azure 春季雲端實例。  遵循我們的[快速入門](spring-cloud-quickstart-launch-app-cli.md)以開始使用。
* 具有參與者最低許可權層級的 Azure Cosmos DB 帳戶。

## <a name="bind-azure-cosmos-db"></a>系結 Azure Cosmos DB

Azure Cosmos DB 有五個支援系結的不同 API 類型：

1. 建立 Azure Cosmos DB 資料庫。 [請參閱這篇文章](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)，以取得建立資料庫的協助。 記錄您的資料庫名稱。 我們的名稱 `testdb`。

1. 根據您的 API 類型，在春季雲端應用程式的 `pom.xml` 中新增下列其中一個相依性。
    
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

1. 使用 `az spring-cloud app update` 來更新目前的部署，或使用 `az spring-cloud app deployment create` 為此變更建立新的部署。  這些命令會使用新的相依性來更新或建立應用程式。

1. 移至 Azure 入口網站中的 Azure 春季雲端服務頁面。 這是您在上一個步驟中更新或部署的相同應用程式。 尋找 [**應用程式儀表板**]，然後選取要系結至 Cosmos DB 的應用程式。 接下來，選取 [`Service binding`]，然後選取 [`Create service binding`] 按鈕。 填寫表單，選取 [系結**類型**`Azure Cosmos DB`]、[API 類型]、您的資料庫名稱，以及 Azure Cosmos DB 帳戶。

    > [!NOTE]
    > 如果您使用 Cassandra，請使用索引鍵空間作為資料庫名稱。

1. 選取應用程式頁面上的 [**重新開機**] 按鈕，以重新開機應用程式。

1. 若要確保服務系結正確，請選取系結名稱並確認其詳細資料。 @No__t-0 欄位應如下所示：

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何將您的 Azure 春季雲端應用程式系結至 CosmosDB。  若要瞭解如何將您的應用程式系結至 Azure Redis 快取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> 將[Azure 春季雲端應用程式系結至 Azure Redis](spring-cloud-tutorial-bind-redis.md)快取。
