---
title: Linux 上的 Java Enterprise 支援 - Azure App Service | Microsoft Docs
description: 搭配 Linux 上的 Azure App Service 使用 Wildfly 來部署 Java Enterprise 應用程式的開發人員指南。
keywords: azure app 服務, web 應用程式, linux, oss, java, wildfly, enterprise
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 408141650a11a81f0c6000c6e7927af8333e2afe
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548471"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>適用於 Linux 上 App Service 的 Java Enterprise 指南

Linux 上的 Azure App Service 可讓 Java 開發人員在完全受控的 Linux 型服務上，建置、部署和調整 Java Enterprise (JEE) 應用程式。  基礎的 Java Enterprise 執行階段環境是開放原始碼的 [Wildfly](https://wildfly.org/) \(英文\) 應用程式伺服器。

本指南提供 Java Enterprise 開發人員可在適用於 Linux 的 App Service 中使用的重要概念和指示。 如果您從未使用適用於 Linux 的 Azure App Service 部署 Java 應用程式，則應先完成 [Java 快速入門](quickstart-java.md)。 [Java 開發人員指南](app-service-linux-java.md)與 [App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java Enterprise 特定且適用於 Linux 的 App Service 相關問題。

## <a name="scale-with-app-service"></a>使用 App Service 調整 

在 Linux 上的 App Service 中執行的 WildFly 應用程式伺服器會以獨立模式執行，而不會在網域設定中執行。 當您相應放大 App Service 方案時，每個 WildFly 執行個體會設定為獨立伺服器。

 使用[調整規模規則](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json)以及藉由[增加執行個體計數](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以垂直或水平方式調整您的應用程式。 

## <a name="customize-application-server-configuration"></a>自訂應用程式伺服器設定

Web 應用程式執行個體為無狀態，因此必須在啟動時設定每個已啟動的新執行個體，以支援應用程式所需的 Wildfly 組態。
您可以撰寫啟動 Bash 指令碼來呼叫 WildFly CLI，以便：

- 設定資料來源
- 設定傳訊提供者
- 將其他模組和相依性新增到 Wildfly 伺服器設定。

 這個指令碼會在 Wildfly 啟動並執行時，但在應用程式啟動之前執行。 這個指令碼應該使用從 `/opt/jboss/wildfly/bin/jboss-cli.sh` 呼叫的 [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) \(英文\)，利用在應用程式伺服器啟動之後所需的任何設定或變更來設定該伺服器。 

請勿使用 CLI 的互動模式來設定 Wildfly。 您可以改用 `--file` 命令來為 JBoss CLI 提供命令的指令碼，例如：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

將啟動指令碼上傳至您 App Service 執行個體中的 `/home/site/deployments/tools`。 如需取得 FTP 認證的相關指示，請參閱[這份文件](/azure/app-service/deploy-configure-credentials#userscope)。 

將 Azure 入口網站中的 [啟動指令碼] 欄位設定為您啟動殼層指令碼的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。

在應用程式組態中提供[應用程式設定](/azure/app-service/web-sites-configure#application-settings)，以傳遞要在指令碼中使用的環境變數。 應用程式設定會保留設定應用程式脫離版本控制所需的連接字串和其他祕密。

## <a name="modules-and-dependencies"></a>模組和相依性

若要透過 JBoss CLI 將模組及其相依性安裝至 Wildfly Classpath，您將必須在它們自己的目錄中建立下列檔案。  部分模組和相依性可能需要其他設定 (例如 JNDI 命名或其他 API 特有的設定)，因此，這份清單是您將需要在大多數情況下設定相依性之項目的基本組合。

- [XML 模組描述元](https://jboss-modules.github.io/jboss-modules/manual/#descriptors) \(英文\)。 這個 XML 檔案會定義模組的名稱、屬性和相依性。 這個[範例 module.xml 檔案](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) \(英文\) 會定義 Postgres 模組、它的 JAR 檔案 JDBC 相依性，以及其他所需的模組相依性。
- 所有適用於您模組的必要 JAR 檔案相依性。
- 含有您 JBoss CLI 命令的指令碼，可用來設定新的模組。 這個檔案所包含的命令將透過 JBoss CLI 執行，以設定伺服器來使用相依性。 如需用來新增模組、資料來源和傳訊提供者的相關文件，請參閱[這份文件](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli) \(英文\)。
-  Bash 啟動指令碼，會在上一個步驟中呼叫 JBoss CLI 並執行此指令碼。 重新啟動您的 App Service 執行個體時，或在向外延展期間佈建新的執行個體時，將執行這個檔案。這個啟動指令碼是在將 JBoss 命令傳遞給 JBoss CLI 時，您可針對應用程式執行任何其他設定的地方。 至少，這個檔案可以是用來將您的 JBoss CLI 命令指令碼傳遞至 JBoss CLI 的單一命令： 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

當您擁有適用於模組的檔案和內容之後，請遵循下列步驟來將模組新增至 Wildfly 應用程式伺服器。 

1. 將檔案 FTP 至您 App Service 執行個體中的 `/home/site/deployments/tools`。 如需取得 FTP 認證的相關指示，請參閱這份文件。 
2. 在 Azure 入口網站的 [應用程式設定] 刀鋒視窗中，將 [啟動指令碼] 欄位設定為您啟動殼層指令碼的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。
3. 藉由按下入口網站上 [概觀] 區段中的 [重新啟動] 按鈕或使用 Azure CLI，來重新啟動您的 App Service 執行個體。

## <a name="data-sources"></a>資料來源

若要設定適用於資料來源連線的 Wildfly，請遵循上述＜安裝模組和相依性＞一節中概述的相同程序。 您可以針對所有 Azure 資料庫服務遵循相同的步驟。

1. 下載適用於您資料庫類別的 JDBC 驅動程式。 為了方便起見，此處是適用於 [Postgres](https://jdbc.postgresql.org/download.html) \(英文\) 和 [MySQL](https://dev.mysql.com/downloads/connector/j/) \(英文\) 的驅動程式。 將下載解壓縮以取得 .jar 檔案。
2. 遵循＜模組和相依性＞中概述的步驟，來建立及上傳 XML 模組描述元、JBoss CLI 指令碼、啟動指令碼和 JDBC .jar 相依性。


目前提供更多使用 [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) \(英文\)、[MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) \(英文\) 及 [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) \(英文\) 來設定 Wildfly 的資訊。 您可以使用這些自訂指示以及上述通用方法，來將資料來源定義新增至您的伺服器。

## <a name="messaging-providers"></a>傳訊提供者

若要使用服務匯流排啟用訊息導向的 Bean 作為傳訊機制：

1. 使用 [Apache QPId JMS 傳訊程式庫](https://qpid.apache.org/proton/index.html) \(英文\)。 針對應用程式，在您的 pom.xml (或其他組建檔案) 中包含此相依性。

2.  建立[服務匯流排資源](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)。 建立 Azure 服務匯流排命名空間及該命名空間內的佇列，以及具有傳送和接收功能的共用存取原則。

3. 藉由針對您的原則主要金鑰進行 URL 編碼或[使用服務匯流排 SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)，將共用存取原則金鑰傳遞至您的程式碼。

4. 透過適用於 JMS 提供者的模組 XML 描述元、.jar 相依性、JBoss CLI 命令及啟動指令碼，來遵循＜安裝模組和相依性＞一節中概述的步驟。 除了這四個檔案，您也必須建立一個 XML 檔案，來定義 JMS 佇列和主題的 JNDI 名稱。 如需參考設定檔，請參閱[這個存放庫](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)。


## <a name="configure-session-management-caching"></a>設定工作階段管理快取

根據預設，Linux 上的 App Service 將使用工作階段親和性 Cookie，來確保具現有工作階段的用戶端要求會路由傳送到您應用程式的相同執行個體。 此預設行為不需要任何設定，但有一些限制：

- 如果應用程式執行個體會重新啟動或相應減少，應用程式伺服器中的使用者工作階段狀態將會遺失。
- 如果應用程式具有很長的工作階段逾時設定或固定的使用者數目，可能需要一些時間，才能自動調整新的執行個體來接收負載，因為只會將新的工作階段路由傳送至最新啟動的執行個體。

您可以設定讓 Wildfly 使用外部工作階段存放區，例如 [Azure Redis 快取](/azure/azure-cache-for-redis/)。 您必須[停用現有的 ARR 執行個體親和性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) \(英文\) 設定來關閉工作階段 Cookie 型路由，讓設定的 Wildfly 工作階段存放區在不受干擾的情況下運作。

## <a name="enable-web-sockets"></a>啟用 Web 通訊端

預設會在 App Service 上啟用 Web 通訊端。 若要在應用程式中開始使用 Websocket，請參閱[這個快速入門](https://github.com/wildfly/quickstart/tree/master/websocket-hello) \(英文\)。

## <a name="logs-and-troubleshooting"></a>記錄與疑難排解

App Service 會提供工具，以協助您進行應用程式問題的疑難排解。

-   在左側導覽窗格中，按一下 [診斷記錄] 來開啟記錄。 按一下 [檔案系統] 來設定您的儲存體配額和保留期間，並儲存您的變更。 您可以在 `/home/LogFiles/` 下方找到這些記錄。
-   [使用 SSH 連線到應用程式執行個體](app-service-linux-ssh-support.md)，以檢視執行中應用程式的記錄。
-   在入口網站的 [診斷記錄] 面板中，或使用下列 Azure CLI 命令，來檢查診斷記錄：` az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group> `
