---
title: 設定 Linux Java 應用程式-Azure App Service |Microsoft Docs
description: 了解如何設定在 Linux 上 Azure App Service 中執行的 Java 應用程式。
keywords: Azure App Service, Web 應用程式, Linux, OSS, Java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850455"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>設定適用於 Azure App Service 的 Linux Java 應用程式

Linux 上的 Azure App Service 可讓 Java 開發人員在全受控 Linux 服務上，快速建置、部署和調整其 Tomcat 或 Java Standard Edition (SE) 封裝的 Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供重要概念和 Java 開發人員使用內建的 Linux 容器，App Service 中的指示。 如果您從未使用過 Azure App Service，請遵循[Java 快速入門](quickstart-java.md)並[PostgreSQL 教學課程的 Java](tutorial-java-enterprise-postgresql-app.md)第一次。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱 < [Azure App Service 診斷概觀](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 主控台存取

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

如需詳細資訊，請參閱[使用 Azure CLI 串流處理記錄](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

## <a name="customization-and-tuning"></a>自訂和調整

適用於 Linux 的 azure App Service 支援的微調方塊和透過 Azure 入口網站和 CLI 進行自訂。 請檢閱以下文章中的非特定 Java web 應用程式組態：

- [設定 App Service 設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [設定自訂網域](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [啟用 SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [新增 CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要在 Tomcat 和 Java SE 環境中設定已配置的記憶體或其他 JVM 執行階段選項，請設定 JAVA_OPTS，如下面[應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)所示。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `$JAVA_OPTS -Xms512m -Xmx1204m`)。

若要從 Azure App Service Linux Maven 外掛程式設定應用程式設定，請在 Azure 外掛程式區段中新增設定/值標籤。 下列範例會設定特定最小和最大 Java 堆積大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 和 S1 執行個體：-Xms1024m -Xmx1024m
- B2 和 S2 執行個體：-Xms3072m -Xmx3072m
- B3 和 S3 執行個體：-Xms6144m -Xmx6144m


調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

### <a name="turn-on-web-sockets"></a>開啟 Web 通訊端

在應用程式的 [應用程式設定] 中，開啟 Azure 入口網站中的 Web 通訊端支援。 您必須重新啟動應用程式，設定才會生效。

搭配使用 Azure CLI 與下列命令，以開啟 Web 通訊端支援：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

然後重新啟動您的應用程式：

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>設定預設字元編碼

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且值為 `$JAVA_OPTS -Dfile.encoding=UTF-8` 的新應用程式設定。

或者，您可以使用 App Service Maven 外掛程式來設定應用程式設定。 在外掛程式設定中新增設定名稱和值標籤：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](/azure/security/security-paas-applications-using-app-services)。 

### <a name="authenticate-users"></a>驗證使用者

設定使用 Azure 入口網站中的應用程式驗證**驗證和授權**選項。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以及其他身分識別提供者的相關文章。

如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)一文中的指示。

 Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

請遵循[繫結現有自訂 SSL 憑證](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)中的指示，上傳現有的 SSL 憑證，並將其繫結至您應用程式的網域名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

## <a name="configure-apm-platforms"></a>設定 APM 平台

本節說明如何連接使用 NewRelic 和 AppDynamics 的應用程式效能監視 (APM) 平台部署在 Linux 上的 Azure App Service 上的 Java 應用程式。

[設定 New Relic](#configure-new-relic)
[設定 AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從 NewRelic 下載 Java 代理程式，其檔案名稱類似 `newrelic-java-x.x.x.zip`。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. [透過 ssh 連線到您的 App Service 執行個體](app-service-linux-ssh-support.md)，然後建立新目錄`/home/site/wwwroot/apm`。
5. 將解壓縮的 NewRelic Java 代理程式檔案上傳至 `/home/site/wwwroot/apm` 之下的目錄。 您的代理程式檔案應位於 `/home/site/wwwroot/apm/newrelic`。
6. 在 `/home/site/wwwroot/apm/newrelic/newrelic.yml` 修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。
    - 如果您使用**WildFly**，請參閱 New Relic 的文件[這裡](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)安裝 Java 代理程式和 JBoss 設定的相關指引。
    - 如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `javaagent` 選項附加至目前值的結尾。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
1. 從 AppDynamics 網站下載 Java 代理程式，其檔案名稱會類似 `AppServerAgent-x.x.x.xxxxx.zip`
1. [透過 ssh 連線到您的 App Service 執行個體](app-service-linux-ssh-support.md)，然後建立新目錄`/home/site/wwwroot/apm`。
1. 將 Java 代理程式檔案上傳至 `/home/site/wwwroot/apm` 之下的目錄。 您的代理程式檔案應位於 `/home/site/wwwroot/apm/appdynamics`。
1. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用**WildFly**，請參閱 AppDynamics 文件[這裡](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)安裝 Java 代理程式和 JBoss 設定的相關指引。

## <a name="configure-tomcat"></a>設定 Tomcat

### <a name="connect-to-data-sources"></a>連接到資料來源

>[!NOTE]
> 如果您的應用程式使用 Spring Framework 或 Spring Boot，則可以設定 Spring Data JPA 的資料庫連線資訊作為環境變數 [在應用程式屬性檔案中]。 然後使用[應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)，以在 Azure 入口網站或 CLI 中定義您應用程式的這些值。

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

若要設定 Tomcat，以使用 Java Database Connectivity (JDBC) 或 Java 持續性 API (JPA)，先自訂`CATALINA_OPTS`會讀取在 Tomcat 在啟動時的環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或是在 Azure 入口網站的 [應用程式設定] 刀鋒視窗中設定環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用程式層級的資料來源

1. 在您專案的 `META-INF/` 目錄中建立 `context.xml` 檔案。 建立 `META-INF/`目錄 (如果此目錄不存在)。

2. 在 `context.xml` 中，新增 `Context` 元素以將資料來源連結至 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. 更新您應用程式的 `web.xml`，以使用您應用程式中的資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>伺服器層級的共用的資源

1. 如果您尚未設定，則會使用 SSH 將 `/usr/local/tomcat/conf` 的內容複製至 App Service Linux 執行個體上的 `/home/tomcat/conf`。
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. 在您 `server.xml` 的 `<Server>` 元素內新增 Context 元素。

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. 更新您應用程式的 `web.xml`，以使用您應用程式中的資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成設定

最後，將驅動程式 Jar 放置在 Tomcat classpath，然後重新啟動您的 App Service。

1. 請確定 Tomcat classloader 可以使用 JDBC 驅動程式檔案，方法是將它們放在 `/home/tomcat/lib` 目錄中。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：
    1. 在  [Cloud Shell](https://shell.azure.com)，安裝 webapp 擴充功能：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 執行下列 CLI 命令，從您的本機系統建立 SSH 通道，至 App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用您的 SFTP 用戶端來連線至本機通道連接埠，然後將檔案上傳至 `/home/tomcat/lib` 資料夾。

    或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_HOME` 重設為 `/home/tomcat/conf`，並使用已更新的設定。

## <a name="configure-wildfly-server"></a>設定 WildFly 伺服器

[使用 App Service 縮放比例](#scale-with-app-service)
[自訂應用程式伺服器設定](#customize-application-server-configuration)
[模組和相依性](#modules-and-dependencies)
[資料來源](#data-sources)
[讓傳訊的提供者](#enable-messaging-providers)
[設定工作階段管理的快取](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>使用 App Service 調整

在 Linux 上的 App Service 中執行的 WildFly 應用程式伺服器會以獨立模式執行，而不會在網域設定中執行。 當您相應放大 App Service 方案時，每個 WildFly 執行個體會設定為獨立伺服器。

 使用[調整規模規則](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)以及藉由[增加執行個體計數](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以垂直或水平方式調整您的應用程式。

### <a name="customize-application-server-configuration"></a>自訂應用程式伺服器設定

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

將啟動指令碼上傳至您 App Service 執行個體中的 `/home/site/deployments/tools`。 如需取得 FTP 認證的相關指示，請參閱[這份文件](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope)。

將 Azure 入口網站中的 [啟動指令碼] 欄位設定為您啟動殼層指令碼的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。

提供[應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)應用程式組態，將指令碼中使用的環境變數中。 應用程式設定會保留設定應用程式脫離版本控制所需的連接字串和其他祕密。

### <a name="modules-and-dependencies"></a>模組和相依性

若要透過 JBoss CLI 將模組及其相依性安裝至 Wildfly Classpath，您將必須在它們自己的目錄中建立下列檔案。 部分模組和相依性可能需要其他設定 (例如 JNDI 命名或其他 API 特有的設定)，因此，這份清單是您將需要在大多數情況下設定相依性之項目的基本組合。

- [XML 模組描述元](https://jboss-modules.github.io/jboss-modules/manual/#descriptors) \(英文\)。 這個 XML 檔案會定義模組的名稱、屬性和相依性。 這個[範例 module.xml 檔案](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) \(英文\) 會定義 Postgres 模組、它的 JAR 檔案 JDBC 相依性，以及其他所需的模組相依性。
- 所有適用於您模組的必要 JAR 檔案相依性。
- 含有您 JBoss CLI 命令的指令碼，可用來設定新的模組。 這個檔案所包含的命令將透過 JBoss CLI 執行，以設定伺服器來使用相依性。 如需用來新增模組、資料來源和傳訊提供者的相關文件，請參閱[這份文件](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli) \(英文\)。
- Bash 啟動指令碼，會在上一個步驟中呼叫 JBoss CLI 並執行此指令碼。 重新啟動您的 App Service 執行個體時，或在向外延展期間佈建新的執行個體時，將執行這個檔案。這個啟動指令碼是在將 JBoss 命令傳遞給 JBoss CLI 時，您可針對應用程式執行任何其他設定的地方。 至少，這個檔案可以是用來將您的 JBoss CLI 命令指令碼傳遞至 JBoss CLI 的單一命令：

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

當您擁有適用於模組的檔案和內容之後，請遵循下列步驟來將模組新增至 Wildfly 應用程式伺服器。

1. 將檔案 FTP 至您 App Service 執行個體中的 `/home/site/deployments/tools`。 如需取得 FTP 認證的相關指示，請參閱這份文件。
2. 在 Azure 入口網站的 [應用程式設定] 刀鋒視窗中，將 [啟動指令碼] 欄位設定為您啟動殼層指令碼的位置，例如 `/home/site/deployments/tools/your-startup-script.sh`。
3. 藉由按下入口網站上 [概觀] 區段中的 [重新啟動] 按鈕或使用 Azure CLI，來重新啟動您的 App Service 執行個體。

### <a name="data-sources"></a>資料來源

若要設定適用於資料來源連線的 Wildfly，請遵循上述＜安裝模組和相依性＞一節中概述的相同程序。 您可以針對所有 Azure 資料庫服務遵循相同的步驟。

1. 下載適用於您資料庫類別的 JDBC 驅動程式。 為了方便起見，此處是適用於 [Postgres](https://jdbc.postgresql.org/download.html) \(英文\) 和 [MySQL](https://dev.mysql.com/downloads/connector/j/) \(英文\) 的驅動程式。 將下載解壓縮以取得 .jar 檔案。
2. 遵循＜模組和相依性＞中概述的步驟，來建立及上傳 XML 模組描述元、JBoss CLI 指令碼、啟動指令碼和 JDBC .jar 相依性。

目前提供更多使用 [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) \(英文\)、[MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) \(英文\) 及 [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) \(英文\) 來設定 Wildfly 的資訊。 您可以使用這些自訂指示以及上述通用方法，來將資料來源定義新增至您的伺服器。

### <a name="enable-messaging-providers"></a>啟用訊息的提供者

若要使用服務匯流排啟用訊息導向的 Bean 作為傳訊機制：

1. 使用 [Apache QPId JMS 傳訊程式庫](https://qpid.apache.org/proton/index.html) \(英文\)。 針對應用程式，在您的 pom.xml (或其他組建檔案) 中包含此相依性。

2. 建立[服務匯流排資源](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)。 建立 Azure 服務匯流排命名空間及該命名空間內的佇列，以及具有傳送和接收功能的共用存取原則。

3. 藉由針對您的原則主要金鑰進行 URL 編碼或[使用服務匯流排 SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)，將共用存取原則金鑰傳遞至您的程式碼。

4. 透過適用於 JMS 提供者的模組 XML 描述元、.jar 相依性、JBoss CLI 命令及啟動指令碼，來遵循＜安裝模組和相依性＞一節中概述的步驟。 除了這四個檔案，您也必須建立一個 XML 檔案，來定義 JMS 佇列和主題的 JNDI 名稱。 如需參考設定檔，請參閱[這個存放庫](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)。

### <a name="configure-session-management-caching"></a>設定工作階段管理快取

根據預設，Linux 上的 App Service 將使用工作階段親和性 Cookie，來確保具現有工作階段的用戶端要求會路由傳送到您應用程式的相同執行個體。 此預設行為不需要任何設定，但有一些限制：

- 如果應用程式執行個體會重新啟動或相應減少，應用程式伺服器中的使用者工作階段狀態將會遺失。
- 如果應用程式具有很長的工作階段逾時設定或固定的使用者數目，可能需要一些時間，才能自動調整新的執行個體來接收負載，因為只會將新的工作階段路由傳送至最新啟動的執行個體。

您可以設定讓 Wildfly 使用外部工作階段存放區，例如 [Azure Redis 快取](/azure/azure-cache-for-redis/)。 您必須[停用現有的 ARR 執行個體親和性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) \(英文\) 設定來關閉工作階段 Cookie 型路由，讓設定的 Wildfly 工作階段存放區在不受干擾的情況下運作。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="statement-of-support"></a>支援聲明

### <a name="runtime-availability"></a>執行階段可用性

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 支援的版本為 Java 8 和 11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

Azure 支援的 Java Development Kit (JDK) 是透過 [Azul Systems](https://www.azul.com/) 提供的 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)。

透過 Azure App Service for Linux 中的新執行階段選項，將提供主要版本更新。 客戶會更新為設定其 App Service 部署的這些新版 Java，以及負責測試並確定主要更新符合其需求。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

### <a name="local-development"></a>本機開發

開發人員可以從 [Azul 的下載網站](https://www.azul.com/downloads/azure-only/zulu/)下載實際執行版本的 Azul Zulu Enterprise JDK，以進行本機開發。

### <a name="development-support"></a>開發支援

使用[合格的 Azure 支援方案](https://azure.microsoft.com/support/plans/)為 Azure 或 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 進行開發時，會提供[支援 Azure 的 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) 的產品支援。

### <a name="runtime-support"></a>執行階段支援

開發人員如果具有[合格的支援方案](https://azure.microsoft.com/support/plans/)，便可透過 Azure 支援服務，針對 Azul Zulu JDK [開立問題](/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Java 開發人員的 Azure](/java/azure/) 中心，以找出 Azure 快速入門、教學課程和 Java 參考文件。

[App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。