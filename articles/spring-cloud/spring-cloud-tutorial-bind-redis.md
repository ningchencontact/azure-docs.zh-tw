---
title: 如何將 Azure Cache for Redis 繫結至您的 Azure Spring Cloud 應用程式 | Microsoft Docs
description: 了解如何將 Azure Cache for Redis 繫結至您的 Azure Spring Cloud 應用程式
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607563"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>教學課程：將 Azure 服務繫結至您的 Azure Spring Cloud 應用程式：Azure Cache for Redis

Azure Spring Cloud 可讓您將特定的 Azure 服務自動繫結至您的應用程式，而不需要手動設定您的 Spring Boot 應用程式。 本文會示範如何將您的應用程式繫結至 Azure Cache for Redis。

## <a name="prerequisites"></a>必要條件

* 已部署的 Azure Spring Cloud 執行個體
* Azure Cache for Redis 服務執行個體
* 適用於 Azure CLI 的 Azure Spring Cloud 擴充功能

如果您沒有已部署好的 Azure Spring Cloud 執行個體，請遵循此[快速入門](spring-cloud-quickstart-launch-app-portal.md)中的步驟，來部署您的第一個 Spring Cloud 應用程式。

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