---
title: 在 Linux 上的 Azure App Service 中建置 Java Enterprise Web 應用程式 | Microsoft Docs
description: 了解如何在 Linux 上，讓 Java Enterprise 應用程式在 Azure App Service 上的 Wildfly 中運作。
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.openlocfilehash: 40bee31b7880a323a48e92912ee323c43c3a97da
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634746"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>教學課程：在 Azure 中建置 Java EE 和 Postgres Web 應用程式

本教學課程會說明如何在 Azure App Service 上建立 Java Enterprise Edition (EE) Web 應用程式，並將其連線至 Postgres 資料庫。 當您完成後，在 [Azure App Service for Linux](app-service-linux-intro.md) 上執行的 [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/) 中會有一個儲存資料的 [Wildfly](http://www.wildfly.org/about/) 應用程式。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 使用 Maven 將 Java EE 應用程式部署至 Azure
> * 在 Azure 中建立 Postgres 資料庫
> * 將 WildFly 伺服器設定為使用 Postgres
> * 更新和重新部署應用程式
> * 在 Wildfly 上執行單元測試

## <a name="prerequisites"></a>必要條件

1. [下載並安裝 Git](https://git-scm.com/)
1. [下載並安裝 Maven 3](https://maven.apache.org/install.html)
1. [下載並安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>複製並編輯應用程式範例

在此步驟中，您會複製應用程式範例，並設定 Maven 專案物件模型 (POM 或 pom.xml) 以供部署。

### <a name="clone-the-sample"></a>複製範例

在終端機視窗中，瀏覽到工作目錄並複製[範例存放庫](https://github.com/Azure-Samples/wildfly-petstore-quickstart)。

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>更新 Maven POM

使用 App Service 的所需名稱和資源群組來更新 Maven POM。 這些值會插入至 Azure 外掛程式，其位於 _pom.xml_ 檔案的更下層。 您不需要事先建立 App Service 方案或執行個體。 Maven 外掛程式會建立資源群組和 App Service (如果尚未存在)。

使用所需的資源名稱來取代預留位置：
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```

您可以向下捲動至 _pom.xml_ 的 `<plugins>` 區段來檢查 Azure 外掛程式。

## <a name="build-and-deploy-the-application"></a>建置並部署應用程式

現在，我們會使用 Maven 來建置應用程式，並將它部署至 App Service。

### <a name="build-the-war-file"></a>建置 .war 檔案

此專案中的 POM 已設定為會將應用程式封裝為網頁封存 (WAR) 檔案。 使用 Maven 建置應用程式：

```bash
mvn clean install -DskipTests
```

此應用程式中的測試案例設計為會在應用程式部署至 WildFly 時執行。 我們會略過這些測試以便在本機建置，請在應用程式部署至 App Service 之後執行這些測試。

### <a name="deploy-to-app-service"></a>部署到 App Service

現在，WAR 已準備就緒，接下來我們可以使用 Azure 外掛程式來部署至 App Service：

```bash
mvn azure-webapp:deploy
```

部署完成時，請繼續進行下一個步驟。

### <a name="create-a-record"></a>建立記錄

開啟瀏覽器並瀏覽至 `https://<your_app_name>.azurewebsites.net/` 。 恭喜您，您已將 Java EE 應用程式部署至 Azure App Service！

此時，應用程式會使用記憶體內部 H2 資料庫。 按一下導覽列中的 [管理員]，然後建立新的類別。 如果您重新啟動 App Service 執行個體，記憶體內部資料庫中的記錄將會遺失。 您會在下列步驟中修正此問題，所採用的方法是在 Azure 上佈建 Postgres 資料庫，並將 WildFly 設定為使用此資料庫。

![[管理員] 按鈕位置](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>佈建 Postgres 資料庫

若要佈建 Postgres 資料庫伺服器，請開啟終端機，然後使用您想要的伺服器名稱、使用者名稱、密碼和位置等值，來執行下列命令。 使用 App Service 所在的相同資源群組。 請記下密碼以供稍後使用！

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

瀏覽至入口網站，然後搜尋 Postgres 資料庫。 當刀鋒視窗啟動時，複製 [伺服器名稱] 和 [伺服器管理員登入名稱] 的值，以供稍後使用。

### <a name="allow-access-to-azure-services"></a>允許存取 Azure 服務

在 [Azure 資料庫] 刀鋒視窗的 [連線安全性] 面板中，將 [允許存取 Azure 服務] 按鈕切換到 [開啟] 位置。

![允許存取 Azure 服務](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>針對 Postgres 更新 Java 應用程式

現在，我們會對 Java 應用程式進行一些變更，從而讓它能夠使用 Postgres 資料庫。

### <a name="add-postgres-credentials-to-the-pom"></a>將 Postgres 認證新增至 POM

在 _pom.xml_ 中，使用 Postgres 伺服器名稱、管理員登入名稱和密碼來取代預留位置值。 當您重新部署應用程式時，這些值便會插入作為 App Service 執行個體中的環境變數。

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>更新 Java 交易 API

接下來，我們需要編輯 Java 交易 API (JPA) 組態，讓 Java 應用程式會與 Postgres (而非先前使用的記憶體內部 H2 資料庫) 通訊。 開啟編輯器並前往 _src/main/resources/META-INF/persistence.xml_。 將 `<jta-data-source>` 的值更換為 `java:jboss/datasources/postgresDS`。 JTA XML 現在應該會有這項設定：

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>設定 WildFly 應用程式伺服器

在部署重新設定過的應用程式之前，我們必須先使用 Postgres 模組和其相依性來更新 WildFly 應用程式伺服器。 若要設定伺服器，我們需要 `wildfly_config/` 目錄中的四個檔案：

- **postgresql-42.2.5.jar**：這個 JAR 檔案是 Postgres 的 JDBC 驅動程式。 如需詳細資訊，請參閱[官方網站](https://jdbc.postgresql.org/index.html)。
- **postgres-module.xml**：這個 XML 檔案會宣告 Postgres 模組 (org.postgres) 的名稱。 此外，它也會指定所要使用模組的必要資源和相依性。
- **jboss_cli_commands.cl**：這個檔案包含 JBoss CLI 所將會執行的組態命令。 這些命令會將 Postgres 模組新增至 WildFly 應用程式伺服器、提供認證、宣告 JNDI 名稱、設定逾時閾值等等。如果您不熟悉 JBoss CLI，請參閱[官方文件](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)。
- **startup_script.sh**：最後，每當 App Service 執行個體啟動時，就會執行這個殼層指令碼。 此指令碼只會執行一個函式：將 `jboss_cli_commands.cli` 中的命令以管道傳送至 JBoss CLI。

建議您一定要閱讀這些檔案的內容，尤其是 _jboss_cli_commands.cli_ 檔案。

### <a name="ftp-the-configuration-files"></a>以 FTP 傳送組態檔

我們必須將 `wildfly_config/` 的內容以 FTP 傳送至 App Service 執行個體。 若要取得 FTP 認證，請在 Azure 入口網站中的 [App Service] 刀鋒視窗上，按一下 [取得發行設定檔] 按鈕。 FTP 使用者名稱和密碼會位於所下載的 XML 文件中。 如需發行設定檔的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/app-service-deployment-credentials)。

使用您選擇的 FTP 工具，將 `wildfly_config/` 中的四個檔案傳輸至 `/home/site/deployments/tools/`。 (注意，請勿傳輸目錄，只要傳輸檔案本身即可)。


### <a name="finalize-app-service"></a>完成 App Service

在 [App Service] 刀鋒視窗中，瀏覽至 [應用程式設定] 面板。 在 [執行階段] 下，將 [啟動檔案] 欄位設定為 `/home/site/deployments/tools/startup_script.sh`。 這可確保系統會在 App Service 執行個體建立好之後，但在 WildFly 伺服器啟動之前，執行殼層指令碼。

最後，重新啟動 App Service。 此按鈕位於 [概觀] 面板中。

## <a name="redeploy-the-application"></a>重新部署應用程式

在終端機視窗中，重建並重新部署應用程式。

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

恭喜！ 應用程式現在會使用 Postgres 資料庫，且應用程式中所建立的任何記錄都會儲存到 Postgres，而非以前的 H3 記憶體內部資料庫。 若要確認這一點，您可以進行記錄，並重新啟動 App Service。 應用程式重新啟動時，記錄仍會位於該處。

## <a name="clean-up"></a>清除

如果您不需要這些資源來進行其他教學課程 (請參閱後續步驟)，您可以執行下列命令來將這些資源刪除︰

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>後續步驟

您已將 Java EE 應用程式部署至 App Service，接下來請參閱 [Java Enterprise 開發人員指南](https://aka.ms/wildfly-quickstart)，以深入了解如何設定服務、進行疑難排解和調整應用程式。