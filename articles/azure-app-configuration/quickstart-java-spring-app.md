---
title: 了解如何使用 Azure 應用程式設定的快速入門 | Microsoft Docs
description: 搭配使用 Azure 應用程式設定與 Java Spring 應用程式的快速入門。
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e27635d153e58f96dad7db6870ed1dc3f640236a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326477"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式組態建立 Java Spring 應用程式

在本快速入門中，您會將 Azure 應用程式組態納入 Java Spring 應用程式中，以集中儲存和管理應用程式設定 (與您的程式碼分開)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- 受支援的 [Java 開發套件 (JDK)](https://docs.microsoft.com/java/azure/jdk) 第 8 版。
- [Apache Maven](https://maven.apache.org/download.cgi) 3.0 版或更新版本。

## <a name="create-an-app-configuration-store"></a>建立應用程式設定存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 選取 [組態總管]   >  [+ 建立]  來新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | /application/config.message | 您好 |

    目前會先讓 [標籤]  和 [內容類型]  保持空白。

## <a name="create-a-spring-boot-app"></a>建立 Spring Boot 應用程式

您會使用 [Spring Initializr](https://start.spring.io/) 來建立新的 Spring Boot 專案。

1. 瀏覽至 <https://start.spring.io/> 。

2. 指定下列選項：

   * 使用 **Java** 產生 **Maven** 專案。
   * 指定 **Spring Boot** 版本，應等於或大於 2.0。
   * 指定應用程式的**群組**和**成品**名稱。
   * 新增 **Web** 相依性。

3. 在指定先前的選項之後，選取 [產生專案]  。 出現提示時，將專案下載至本機電腦上的路徑。

## <a name="connect-to-an-app-configuration-store"></a>連線至應用程式設定存放區

1. 當您在本機系統上擷取檔案之後，就可以開始編輯簡單的 Spring Boot 應用程式。 在應用程式的根目錄中尋找 pom.xml  檔案。

2. 在文字編輯器中開啟 pom.xml  檔案，並將 Spring Cloud Azure 設定 Starter 新增至 `<dependencies>` 的清單中：

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
    </dependency>
    ```

3. 在應用程式的 package 目錄中建立名為 MessageProperties.java  的新 Java 檔案。 加入下列幾行：

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. 在應用程式的 package 目錄中建立名為 HelloController.java  的新 Java 檔案。 加入下列幾行：

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. 開啟主要的應用程式 Java 檔案，並新增 `@EnableConfigurationProperties` 來啟用這項功能。

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. 在應用程式的資源目錄下建立名為 `bootstrap.properties` 的新檔案，然後將下列幾行新增至該檔案。 以適當的應用程式設定存放區屬性取代範例值。

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 使用 Maven 建置 Spring Boot 應用程式並加以執行；例如：

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. 在您的應用程式執行之後，使用 *curl* 來測試您的應用程式；例如：

      ```shell
      curl -X GET http://localhost:8080/
      ```
    您會看到您在應用程式設定存放區中輸入的訊息。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式設定存放區，並將其與 Java Spring 應用程式搭配使用。 如需詳細資訊，請參閱 [Azure 上的 Spring](https://docs.microsoft.com/java/azure/spring-framework/)。

若要深入了解如何使用應用程式設定，請繼續進行下一個示範驗證的教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
