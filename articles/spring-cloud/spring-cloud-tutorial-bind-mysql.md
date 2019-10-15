---
title: 如何將適用於 MySQL 的 Azure 資料庫繫結至您的 Azure Spring Cloud 應用程式 | Microsoft Docs
description: 此文章將會示範如何將 Azure MySQL 繫結至您的 Azure Spring Cloud 應用程式
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038347"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>教學課程：將 Azure 服務繫結至您的 Azure Spring Cloud 應用程式：適用於 MySQL 的 Azure 資料庫

Azure Spring Cloud 可讓您將特定的 Azure 服務自動繫結至您的應用程式，而不需要手動設定您的 Spring Boot 應用程式。 此教學課程將會示範如何將應用程式繫結至 Azure MySQL。

## <a name="prerequisites"></a>必要條件

* 已部署的 Azure Spring Cloud 執行個體
* 適用於 MySQL 的 Azure 資料庫帳戶
* Azure CLI

若有必要，請使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。  它已預先安裝常用的 Azure 工具，包括 Git、JDK、Maven 和 Azure CLI 的最新版本。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

## <a name="bind-azure-database-for-mysql"></a>繫結適用於 MySQL 的 Azure 資料庫

1. 請記下您 Azure MySQL 帳戶的管理使用者名稱和密碼。 從 MySQL 用戶端連線至伺服器並建立名為 `testdb` 的資料庫。 建立新的非管理帳戶。

1. 將下列相依性新增到您專案的 `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. 移除 `application.properties` 檔案中的 `spring.datasource.*` 屬性 (若有的話)。

1. 使用 `az spring-cloud app update` 來更新目前的部署，或是使用 `az spring-cloud app deployment create` 來為此變更建立新部署。  這些命令會搭配新的相依性來更新或建立應用程式。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。 尋找 [應用程式儀表板]  ，然後選取要繫結至 Azure MySQL 的應用程式。  這是您在上一個步驟中更新或部署的應用程式。 接下來，請選取 `Service binding` 並選取 `Create service binding` 按鈕。 填寫表單，並且務必選取 `Azure MySQL` (您先前所使用的資料庫名稱) 作為 [繫結類型]  ，以及您在第一個步驟中所記下的使用者名稱和密碼。

1. 重新啟動應用程式，此繫結現在應該會正常運作。

1. 為了確定服務繫結的正確性，請選取繫結名稱並確認其詳細資料。 `property` 欄位看起來應該如下所示：
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何將 Azure Spring Cloud 應用程式繫結至 MySQL DB。  若要深入了解如何管理 Azure Spring Cloud 服務，請繼續閱讀以了解服務探索和註冊。

> [!div class="nextstepaction"]
> [了解如何使用 Spring Cloud Service Registry 來啟用服務探索和註冊](spring-cloud-service-registration.md)。

