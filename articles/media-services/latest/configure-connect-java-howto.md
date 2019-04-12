---
title: 連線到 Azure 媒體服務 v3 API-Java
description: 了解如何連接到媒體服務 v3 API 與 Java。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 9177a1ae1f2939979d1f824c98b6018a83c2779f
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502259"
---
# <a name="connect-to-media-services-v3-api---java"></a>連線到媒體服務 v3 API-Java

這篇文章說明如何連接到 Azure 媒體服務 v3 Java SDK 使用服務主體的登入方法。

在本文中，Visual Studio Code 用來開發應用程式。

## <a name="prerequisites"></a>必要條件

- 請遵循[使用 Visual Studio Code 的撰寫 Java](https://code.visualstudio.com/docs/java/java-tutorial)安裝：

   - JDK
   - Apache Maven
   - Java 延伸套件
- 請務必將`JAVA_HOME`和`PATH`環境變數。
- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住的資源群組名稱和媒體服務帳戶名稱。
- 請依照下列中的步驟[存取 Api](access-api-cli-how-to.md)主題。 在稍後步驟中記錄的訂用帳戶識別碼、 應用程式識別碼 （用戶端識別碼）、 驗證金鑰 （密碼） 和您所需要的租用戶識別碼。

也請檢閱：

- [Visual Studio Code 中的 Java](https://code.visualstudio.com/docs/languages/java)
- [在 VS Code 中的 Java 專案管理](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>建立 Maven 專案

開啟命令列工具和`cd`至您想要用來建立專案的目錄。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

當您執行命令， `pom.xml`， `App.java`，並建立其他檔案。 

## <a name="add-dependencies"></a>新增相依性

1. 在 Visual Studio Code 中，開啟您的專案所在的資料夾。 
1. 尋找和開啟`pom.xml`。 
1. 加入所需的相依性。 其中一個是[com.microsoft.azure.mediaservices.v2018_07_01:azure mgmt 媒體](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>連接到 Java 用戶端

1. 開啟`App.java`檔案下`src\main\java\com\azure\ams`，並確定您的套件是否包含在最上方：

    ```java
    package com.azure.ams;
    ```
2. 在 package 陳述式下，新增下列 import 陳述式：
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
2. 若要建立需要提出要求的 Active Directory 認證，將下列程式碼加入應用程式類別的 main 方法並設定所得的值[存取 Api](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```

## <a name="see-also"></a>請參閱

- [媒體服務概念](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java 參考](https://aka.ms/ams-v3-java-ref)
- [https://search.maven.org/](https://search.maven.org/)

## <a name="next-steps"></a>後續步驟

您現在可以包含`import com.microsoft.azure.management.mediaservices.v2018_07_01.Asset;`及開始操作實體。<br/>
例如取得您帳戶中的所有資產： `Observable<Asset> asyncAssets = 
                    manager.assets().listAsync(groupId, accountId).last();`
