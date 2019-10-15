---
title: 準備 Spring 應用程式以部署到 Azure Spring Cloud | Microsoft Docs
description: 在本快速入門中，您會準備 Java Spring 應用程式以進行部署。
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 98d9f3f656cff84cec8d223ed535255157155bd2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038327"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>教學課程：準備 Java Spring 應用程式以部署到 Azure Spring Cloud

本快速入門會示範如何準備現有的 Java Spring Cloud 應用程式以部署到 Azure Spring Cloud。  若能夠正確設定，Azure Spring Cloud 將能提供豐富的服務以監視、調整及更新您的 Spring Cloud 應用程式。 

## <a name="java-runtime-version"></a>Java 執行階段版本

只有 Spring/Java 應用程式可以在 Azure Spring Cloud 中執行。

同時支援 Java 8 和 Java 11。 主機環境會包含適用於 Azure 的最新 Azul Zulu OpenJDK。 請參閱[這篇文章](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)以取得適用於 Azure 的 Azul Zulu OpenJDK 的詳細資訊。 

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

Azure Spring Cloud 只支援 Spring Boot 應用程式。 同時支援 Spring Boot 2.0 和 2.1。 下表會列出支援的 Spring Boot 和 Spring Cloud 組合。

Spring Boot 版本 | Spring Cloud 版本
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

根據您的版本，確認您的 `pom.xml` 檔案具有 Spring Boot 和 Spring Cloud 相依性。

### <a name="version-20"></a>2\.0 版：

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>2\.1 版：

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 用戶端相依性

Azure Spring Cloud 會為您裝載及管理 Spring Cloud 元件，例如 Spring Cloud Service Registry 和 Spring Cloud Config Server。 在您的相依性中包含 Azure Spring Cloud 的用戶端程式庫，以允許和您 Azure Spring Cloud 服務執行個體之間的通訊。

下表列出適用於您 Spring Boot/Spring Cloud 應用程式的正確版本。

Spring Boot 版本 | Spring Cloud 版本 | Azure Spring Cloud 版本
---|---|---
2.0.x | Finchley.RELEASE | 2.0.0-SNAPSHOT
2.1.x | Greenwich.RELEASE | 2.1.0-SNAPSHOT

將此程式碼片段包含在您的 `pom.xml` 中，同時在 'dependency' 中包含正確的 Azure Spring Cloud 版本：

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="other-required-dependencies"></a>其他必要的相依性

若要啟用 Azure Spring Cloud 的內建功能，您的應用程式必須包含下列相依性。 這將能確保您的應用程式能針對每個元件正確自我設定。  

### <a name="service-registry"></a>服務登錄

若要使用受控 Azure Service Registry 服務，包括 `POM.xml` 中的 `spring-cloud-starter-netflix-eureka-client` (如下所示)。

Service Registry 伺服器的端點將會自動搭配您的應用程式插入為環境變數。 應用程式將能夠向 Service Registry 伺服器註冊自己，並探索其他相依的微服務。

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>分散式設定

若要啟用分散式設定，請在您 `pom.xml` 的相依性區段中包含 `spring-cloud-config-client`。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 請不要在啟動程序設定中指定 `spring.cloud.config.enabled=false`，因為那會使應用程式無法搭配 Config Server 運作。

### <a name="metrics"></a>度量

在您 pom.xml 的相依性區段中包含 `spring-boot-starter-actuator`。 系統會定期從 JMX 端點提取計量，並可以使用 Azure 入口網站來將它視覺化。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>分散式追蹤

在您 pom.xml 的相依性區段中包含 `spring-cloud-starter-sleuth` 和 `spring-cloud-starter-zipkin`，如下所示。 此外，您也需要啟用 Azure App Insights 執行個體，以搭配您的 Azure Spring Cloud 服務執行個體運作。 在[這裡](spring-cloud-tutorial-distributed-tracing.md)深入閱讀搭配 Azure Spring Cloud 啟用 App Insights 的方法

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何設定 Java Spring 應用程式以部署至 Azure Spring Cloud。  若要了解如何啟用 Config Server，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [了解如何設定您的 Config Server](spring-cloud-tutorial-config-server.md)。

