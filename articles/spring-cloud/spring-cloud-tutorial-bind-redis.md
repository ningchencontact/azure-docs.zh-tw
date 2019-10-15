---
title: 如何將 Azure Cache for Redis 繫結至您的 Azure Spring Cloud 應用程式 | Microsoft Docs
description: 了解如何將 Azure Cache for Redis 繫結至您的 Azure Spring Cloud 應用程式
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038237"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>教學課程：將 Azure 服務繫結至您的 Azure Spring Cloud 應用程式：Azure Cache for Redis

Azure Spring Cloud 可讓您將特定的 Azure 服務自動繫結至您的應用程式，而不需要手動設定您的 Spring Boot 應用程式。 本文會示範如何將您的應用程式繫結至 Azure Cache for Redis。

## <a name="prerequisites"></a>必要條件

* 已部署的 Azure Spring Cloud 執行個體
* Azure Cache for Redis 服務執行個體
* 適用於 Azure CLI 的 Azure Spring Cloud 擴充功能

若有必要，請使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括 Git、JDK、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

## <a name="bind-azure-cache-for-redis"></a>繫結 Azure Cache for Redis

1. 將下列相依性新增到您專案的 `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. 移除 `application.properties` 檔案中的 `spring.redis.*` 屬性 (若有的話)

1. 使用 `az spring-cloud app update` 來更新目前的部署，或是使用 `az spring-cloud app deployment create` 來建立新部署。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。 尋找 [應用程式儀表板]  ，然後選取要繫結至 Azure Cache for Redis 的應用程式。  這是您在上一個步驟中更新或部署的應用程式。 接下來，請選擇 `Service binding` 並選取 `Create service binding` 按鈕。 填寫表單，並請務必選取 `Azure Cache for Redis` 作為 [繫結類型]  、您的 Redis 伺服器，以及 [主要金鑰] 選項。 

1. 重新啟動應用程式，此繫結現在應該會正常運作。

1. 為了確定服務繫結的正確性，請選取繫結名稱並確認其詳細資料。 `property` 欄位看起來應該如下所示：
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將 Azure Spring Cloud 應用程式繫結至 Azure Redis 快取。  若要深入了解將服務繫結至您的應用程式，請繼續進行將應用程式繫結至 MySQL DB 的教學課程。

> [!div class="nextstepaction"]
> [了解如何將 Azure MySQL 服務繫結至您的 Azure Spring Cloud 服務](spring-cloud-tutorial-bind-mysql.md)。