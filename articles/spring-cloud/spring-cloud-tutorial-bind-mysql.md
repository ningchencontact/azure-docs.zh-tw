---
title: 如何將適用於 MySQL 的 Azure 資料庫繫結至您的 Azure Spring Cloud 應用程式 | Microsoft Docs
description: 此文章將會示範如何將 Azure MySQL 繫結至您的 Azure Spring Cloud 應用程式
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607595"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>教學課程：將 Azure 服務繫結至您的 Azure Spring Cloud 應用程式：適用於 MySQL 的 Azure 資料庫

Azure Spring Cloud 可讓您將特定的 Azure 服務自動繫結至您的應用程式，而不需要手動設定您的 Spring Boot 應用程式。 此教學課程將會示範如何將應用程式繫結至 Azure MySQL。

## <a name="prerequisites"></a>必要條件

* 已部署的 Azure Spring Cloud 執行個體
* 適用於 MySQL 的 Azure 資料庫帳戶
* Azure CLI

如果您沒有已部署好的 Azure Spring Cloud 執行個體，請遵循此[快速入門](spring-cloud-quickstart-launch-app-portal.md)中的步驟，來部署您的第一個 Spring Cloud 應用程式。

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

