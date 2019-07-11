---
title: 在 Linux 上建立 Java Web 應用程式 - Azure App Service
description: 在本快速入門中，您將在短短幾分鐘內在 Linux 上的 Azure App Service 中部署第一個 Java Hello World。
keywords: Azure, App Service, Web 應用程式, Linux, Java, Maven, 快速入門
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 09a3ad182ff5ee19a81b03557b3277343912a774
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461431"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>快速入門：在 Linux 上的 App Service 中建立 Java 應用程式

[Linux 上的 App Service](app-service-linux-intro.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本快速入門說明如何使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 搭配[適用於 Azure App Service 的 Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)，部署 Java Web 封存 (WAR) 檔案。
> [!NOTE]
>
> 使用 IntelliJ 和 Eclipse 等熱門 IDE 也可以執行相同的動作。 請參閱 [Azure Toolkit for IntelliJ 快速入門](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入門](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)中的類似文件。
>
![在 Azure 中執行的範例應用程式](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>建立 Java 應用程式

在 Cloud Shell 提示字元中執行下列 Maven 命令，以建立名為 `helloworld` 的新應用程式：

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>設定 Maven 外掛程式

若要從 Maven 進行部署，請在 Cloud Shell 中使用程式碼編輯器，開啟 `helloworld` 目錄中的 `pom.xml` 專案檔案。 

```bash
code pom.xml
```

然後在 `pom.xml` 檔案的 `<build>` 元素內新增下列外掛程式定義。

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
    </plugin>
</plugins>
```

部署到 Azure App Service 的程序從 Azure CLI 使用帳戶認證。 繼續執行前，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

您可以設定部署，在命令提示字元中執行 maven 命令 `mvn azure-webapp:config`，並按 **ENTER** 鍵使用預設組態，直到您看到**確認 (是/否)** 提示，然後按 **'y'** 完成設定。

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.6.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> 在本文中，我們只會使用封裝在 WAR 檔案中的 JAVA 應用程式。 此外掛程式也支援 JAR Web 應用程式，請瀏覽[將 Java SE JAR 檔案部署至 Linux 上的 App Service](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，並加以試用。

再次瀏覽至 `pom.xml` 以查看外掛程式組態是否已更新，如有需要，您可以直接在 pom 檔案中修改 App Service 的其他組態，以下列出一些常見的組態：

 屬性 | 必要 | 說明 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定組態結構描述的版本。 支援的值包括：`v1`、`v2`。 | 1.5.2
`<resourceGroup>` | true | Web 應用程式的 Azure 資源群組。 | 0.1.0+
`<appName>` | true | 您的 Web 應用程式名稱。 | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | 指定將裝載 Web 應用程式的區域；預設值為 **westus**。 所有有效的區域皆列於[支援的區域](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region)一節。 | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | 您 Web 應用程式的定價層。 預設值為 **P1V2**。| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | 執行階段環境組態，您可以在[此處](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)查看詳細資料。 | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | 部署組態，您可以在[此處](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting)查看詳細資料。 | 0.1.0+

## <a name="deploy-the-app"></a>部署應用程式

使用下列命令將您的 Java 應用程式部署至 Azure：

```bash
mvn package azure-webapp:deploy
```

完成部署後，在網頁瀏覽器中使用下列 URL，瀏覽至已部署的應用程式，例如 `http://<webapp>.azurewebsites.net`。 

![在 Azure 中執行的範例應用程式](media/quickstart-java/java-hello-world-in-browser.png)

**恭喜！** 您已將第一個 Java 應用程式部署至 Linux 上的 App Service。

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在資源群組中建立了 Azure 資源。 如果您在未來不需要這些資源，請在 Cloud Shell 中執行下列命令，刪除資源群組：

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

此命令可能會花一分鐘執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：搭配 PostgreSQL 的 Java Enterprise 應用程式](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [設定 Java 應用程式](configure-custom-container.md)

> [!div class="nextstepaction"]
> [使用 Jenkins 的 CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [適用於 Java 開發人員的其他 Azure 資源](/java/azure/)
