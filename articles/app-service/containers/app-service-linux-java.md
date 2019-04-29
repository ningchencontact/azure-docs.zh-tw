---
title: Linux 上 App Service 的 Java 開發人員指南 - Azure | Microsoft Docs
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
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 5c9f70650f518c72a75d9a7826e7cbc30a95a00c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852697"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Linux 上 App Service 的 Java 開發人員指南

Linux 上的 Azure App Service 可讓 Java 開發人員在全受控 Linux 服務上，快速建置、部署和調整其 Tomcat 或 Java Standard Edition (SE) 封裝的 Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供 Java 開發人員在 App Service for Linux 中使用的重要概念和指示。 如果您從未使用過 Azure App Service for Linux，則應該先閱讀 [Java 快速入門](quickstart-java.md)。 [App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用[適用於 Azure App Service 的 Maven 外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)將.jar 和.war 檔案部署。 與熱門的 Ide 部署也支援[適用於 IntelliJ 的 Azure 工具組](/java/azure/intellij/azure-toolkit-for-intellij)或是[適用於 Eclipse 的 Azure 工具組](/java/azure/eclipse/azure-toolkit-for-eclipse)。

否則，您的部署方法將取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像上的 .jar 檔案，請使用 Kudu 網站的 `/api/zipdeploy/` 端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需如何存取和使用這些診斷工具的詳細資訊，請參閱 [Azure App Service 診斷概觀](/azure/app-service/overview-diagnostics)。

## <a name="application-performance-monitoring"></a>應用程式效能監視

請參閱 [Linux 上 Azure App Service 之 Java 應用程式的應用程式效能監視工具](how-to-java-apm-monitoring.md) (英文)，以瞭解如何在 Linux 上使用於應用程式服務上執行的 Java 應用程式，來設定 New Relic 與 AppDynamics。

### <a name="ssh-console-access"></a>SSH 主控台存取

使用 SSH 連線到執行您的應用程式的 Linux 環境。 如需透過網頁瀏覽器或本機終端機連線至 Linux 系統的完整指示，請參閱 [Linux 上的 Azure App Service 支援 SSH](/azure/app-service/containers/app-service-linux-ssh-support)。

### <a name="streaming-logs"></a>串流記錄

如需快速偵錯和疑難排解，您可以使用 Azure CLI 將記錄串流處理到主控台。 使用 `az webapp log config` 設定 CLI，以啟用記錄：

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

然後使用 `az webapp log tail` 將記錄串流處理到主控台：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

如需詳細資訊，請參閱[使用 Azure CLI 串流處理記錄](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](/azure/app-service/troubleshoot-diagnostic-logs#enablediag)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 Java 和 Tomcat 應用程式記錄檔可在`/home/LogFiles/Application/`目錄。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑難排解工具

內建的 Java 映像為基礎[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)作業系統。 使用`apk`套件管理員安裝的疑難排解工具或命令。

## <a name="customization-and-tuning"></a>自訂和調整

適用於 Linux 的 azure App Service 支援的微調方塊和透過 Azure 入口網站和 CLI 進行自訂。 請檢閱下列非 Java 特定 Web 應用程式設定的文章：

- [設定 App Service 設定](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [設定自訂網域](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [啟用 SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [新增 CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要設定配置的記憶體或其他 JVM 執行階段選項 Tomcat 和 Java SE 環境中，建立[應用程式設定](/azure/app-service/web-sites-configure#app-settings)名為`JAVA_OPTS`的選項。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

若要設定 Maven 外掛程式中的應用程式設定，設定/值標記的區段中新增 Azure 外掛程式。 下列範例會設定特定最小和最大 Java 堆積大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 及 S1 執行個體： `-Xms1024m -Xmx1024m`
- B2 和 S2 執行個體： `-Xms3072m -Xmx3072m`
- B3 和 S3 的執行個體： `-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

如果您要部署的 JAR 應用程式，其應為`app.jar`，讓內建的映像可以正確地識別您的應用程式。 （Maven 外掛程式執行此重新命名自動）。如果您不想重新命名以 JAR `app.jar`，您可以上傳的命令執行 JAR 的殼層指令碼。 貼在此指令碼的完整路徑[啟動檔案](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file)入口網站的組態中的文字方塊。

### <a name="turn-on-web-sockets"></a>開啟 Web 通訊端

在應用程式的 [應用程式設定] 中，開啟 Azure 入口網站中的 Web 通訊端支援。 您必須重新啟動應用程式，設定才會生效。

搭配使用 Azure CLI 與下列命令，以開啟 Web 通訊端支援：

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

然後重新啟動您的應用程式：

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>設定預設字元編碼

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且值為 `-Dfile.encoding=UTF-8` 的新應用程式設定。

或者，您可以使用 App Service Maven 外掛程式來設定應用程式設定。 在外掛程式設定中新增設定名稱和值標籤：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>調整啟動逾時

如果您的 Java 應用程式特別大時，您應該增加啟動時間限制。 若要這樣做，請建立 應用程式設定，`WEBSITES_CONTAINER_START_TIME_LIMIT`並將它設定為應用程式服務應該逾時前等候的秒數。最大值是`1800`秒。

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users"></a>驗證使用者

設定使用 Azure 入口網站中的應用程式驗證**驗證和授權**選項。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。  如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](/azure/app-service/configure-authentication-provider-aad)，以及其他身分識別提供者的相關文章。

如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)一文中的指示。

Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。 請務必要在 `application.properties` 檔案中增加標頭大小上限。 建議值為 `16384`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

請遵循[繫結現有自訂 SSL 憑證](/azure/app-service/app-service-web-tutorial-custom-ssl)中的指示，上傳現有的 SSL 憑證，並將其繫結至您應用程式的網域名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用金鑰保存庫參考

[Azure 金鑰保存庫](../../key-vault/key-vault-overview.md)提供集中式的祕密管理存取原則和稽核歷程記錄。 您可以將機密資料 （例如密碼或連接字串） 儲存在金鑰保存庫，並在您的應用程式，透過環境變數存取這些機密資料。

首先，遵循[您的應用程式存取權授與 Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)並[讓您的祕密的金鑰保存庫參考中為應用程式設定](../app-service-key-vault-references.md#reference-syntax)。 您可以驗證參考解析設為祕密的列印環境變數時從遠端存取應用程式服務的終端機。

若要插入這些 Spring 或 Tomcat 組態檔中的祕密，使用環境變數的資料隱碼語法 (`${MY_ENV_VAR}`)。 Spring 組態檔，請參閱此文件[外部化組態](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)。

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

若要設定讓 Tomcat 使用「Java 資料庫連線」(JDBC) 或「Java 保存 API」(JPA)，請先自訂 Tomcat 在啟動時所讀入的 `CATALINA_OPTS` 環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

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

#### <a name="shared-server-level-resources"></a>共用的伺服器層級的資源

1. 如果您尚未設定，則會使用 SSH 將 `/usr/local/tomcat/conf` 的內容複製至 App Service Linux 執行個體上的 `/home/tomcat/conf`。

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>最後，將驅動程式 Jar 放置在 Tomcat classpath，然後重新啟動您的 App Service

1. 請確定 Tomcat classloader 可以使用 JDBC 驅動程式檔案，方法是將它們放在 `/home/tomcat/lib` 目錄中。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：  
   1. 安裝 Azure App Service webpp 延伸模組：

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. 執行下列 CLI 命令，以建立從本機系統到 App Service 的 SSH 通道：

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. 使用您的 SFTP 用戶端來連線至本機通道連接埠，然後將檔案上傳至 `/home/tomcat/lib` 資料夾。

      或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_HOME` 重設為 `/home/tomcat/conf`，並使用已更新的設定。

### <a name="spring-boot"></a>Spring Boot

若要連接至 Spring Boot 應用程式中的資料來源，我們建議建立的連接字串，並插入到您`application.properties`檔案。

1. 在 [App Service] 刀鋒視窗的 [應用程式設定] 區段中，設定字串的名稱、 將您的 JDBC 連接字串貼到 [值] 欄位中，以及設定為 「 自訂 」 型別。 您可以選擇設定此連接字串，做為位置設定。

    ![在入口網站中建立的連接字串。][1]

    此連接字串是可存取我們的應用程式作為環境變數，名為`CUSTOMCONNSTR_<your-string-name>`。 例如，前面所建立的連接字串將會命名為`CUSTOMCONNSTR_exampledb`。

2. 在您`application.properties`檔案中，參考環境變數名稱與此連接字串。 我們的範例中，我們會使用下列程式碼。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

請參閱[Spring Boot 文件上的資料存取](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
)並[外部化組態](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)如需有關本主題。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="runtime-availability-and-statement-of-support"></a>執行階段可用性和支援聲明

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 唯一支援的主要版本是 Java 8。

## <a name="java-runtime-statement-of-support"></a>Java 執行階段支援聲明

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

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
