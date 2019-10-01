---
title: 設定 Linux JAVA 應用程式-Azure App Service |Microsoft Docs
description: 了解如何設定在 Linux 上 Azure App Service 中執行的 Java 應用程式。
keywords: azure app service，web 應用程式，linux，oss，java，java ee，jee，javaee
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 8e47365f74668ba2b93bad2b65a9dc9e83080832
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098131"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>設定適用于 Azure App Service 的 Linux JAVA 應用程式

Linux 上的 Azure App Service 可讓 Java 開發人員在全受控 Linux 服務上，快速建置、部署和調整其 Tomcat 或 Java Standard Edition (SE) 封裝的 Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供 JAVA 開發人員在 App Service 中使用內建 Linux 容器的重要概念和指示。 如果您從未使用過 Azure App Service，請先遵循[java 快速入門](quickstart-java.md)和[java with 于 postgresql 教學](tutorial-java-enterprise-postgresql-app.md)課程。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用[Maven 外掛程式進行 Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) ，以部署 .jar 和 war 檔案。 [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij)或[Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)也支援使用熱門 ide 進行部署。

否則，您的部署方法將取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像上的 .jar 檔案，請使用 Kudu 網站的 `/api/zipdeploy/` 端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱[Azure App Service 診斷總覽](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 主控台存取

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

如需詳細資訊，請參閱[Cloud Shell 中的串流記錄](../troubleshoot-diagnostic-logs.md#in-cloud-shell)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 您的 JAVA 和 Tomcat 應用程式記錄可在 */home/LogFiles/Application/* 目錄中找到。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑難排解工具

內建的 JAVA 映射是以[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)作業系統為基礎。 `apk`使用封裝管理員來安裝任何疑難排解工具或命令。

### <a name="flight-recorder"></a>飛行記錄器

App Service 上的所有 Linux JAVA 映射都已安裝了祖魯飛行記錄器，因此您可以輕鬆地連接到 JVM 並啟動 profiler 記錄，或產生堆積傾印。

#### <a name="timed-recording"></a>計時記錄

若要開始使用，請透過 SSH 連線到您`jcmd`的 App Service，然後執行命令，以查看執行的所有 JAVA 進程的清單。 除了 jcmd 本身之外，您還應該看到以處理序識別碼（pid）執行的 JAVA 應用程式。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

執行下列命令，以啟動 JVM 的30秒記錄。 這會分析 JVM，並在主目錄中建立名為*jfr_example JFR*的 JFR 檔案。 （將116取代為 JAVA 應用程式的 pid）。

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在30秒的間隔期間，您可以藉由`jcmd 116 JFR.check`執行來驗證錄製是否正在進行中。 這會顯示指定 JAVA 進程的所有錄製。

#### <a name="continuous-recording"></a>連續記錄

您可以使用祖魯飛行記錄器，持續對您的 JAVA 應用程式進行程式碼剖析，而對執行時間效能（[來源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)）的影響最小。 若要這麼做，請執行下列 Azure CLI 命令，以使用必要的設定來建立名為 JAVA_OPTS 的應用程式設定。 當您的應用程式啟動時，會將 JAVA_OPTS `java`應用程式設定的內容傳遞至命令。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

記錄開始之後，您可以隨時使用`JFR.dump`命令傾印目前的記錄資料。

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

如需詳細資訊，請參閱[Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析錄製

使用[FTPS](../deploy-ftp.md)將您的 JFR 檔案下載到本機電腦。 若要分析 JFR 檔案，請下載並安裝[祖魯任務控制](https://www.azul.com/products/zulu-mission-control/)。 如需有關祖魯任務控制的指示，請參閱[Azul 檔](https://docs.azul.com/zmc/)和[安裝指示](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自訂和調整

適用于 Linux 的 Azure App Service 支援透過 Azure 入口網站和 CLI 進行的微調和自訂。 請參閱下列文章，以瞭解非 JAVA 特定的 web 應用程式設定：

- [設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [設定自訂網域](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [啟用 SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [新增 CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要在 Tomcat 和 JAVA SE 環境中設定已配置的記憶體或其他 JVM 執行時間選項，請使用`JAVA_OPTS`選項建立名為的[應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定] 下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

若要從 Maven 外掛程式設定應用程式設定，請在 Azure 外掛程式區段中新增設定/值標記。 下列範例會設定特定的最小和最大 JAVA 堆積大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 和 S1 實例：`-Xms1024m -Xmx1024m`
- B2 和 S2 實例：`-Xms3072m -Xmx3072m`
- B3 和 S3 實例：`-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

如果您要部署 JAR 應用程式，它應該命名為*app.config* ，讓內建映射能夠正確地識別您的應用程式。 （Maven 外掛程式會自動進行此重新命名）。如果您不想要將 JAR 重新命名為*app.config*，可以使用命令上傳 shell 腳本來執行 JAR。 然後在入口網站的 [設定] 區段的 [[啟動](app-service-linux-faq.md#built-in-images)檔案] 文字方塊中，貼上此腳本的完整路徑。 啟動腳本不會從其放置所在的目錄執行。 因此，請一律使用絕對路徑來參考啟動腳本中的檔案（例如： `java -jar /home/myapp/myapp.jar`）。

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

### <a name="adjust-startup-timeout"></a>調整啟動超時

如果您的 JAVA 應用程式特別大，您應該增加啟動時間限制。 若要這麼做，請建立應用程式`WEBSITES_CONTAINER_START_TIME_LIMIT`設定，並將它設定為 App Service 應該在超時前等待的秒數。最大值為`1800`秒。

### <a name="pre-compile-jsp-files"></a>預先編譯 JSP 檔案

若要改善 Tomcat 應用程式的效能，您可以在部署至 App Service 之前，先編譯 JSP 檔案。 您可以使用 Apache Sling 提供的[Maven 外掛程式](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)，或使用此[Ant 組建](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)檔案。

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users-easy-auth"></a>驗證使用者（簡單驗證）

使用 **驗證和授權** 選項，在 Azure 入口網站中設定應用程式驗證。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)一文中的指示。

#### <a name="tomcat-and-wildfly"></a>Tomcat 和 Wildfly

您的 Tomcat 或 Wildfly 應用程式可以藉由將主體物件轉換成對應物件，直接從 servlet 存取使用者的宣告。 Map 物件會將每個宣告類型對應到該類型的宣告集合。 在下列程式碼中`request` ，是的`HttpServletRequest`實例。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在您可以檢查`Map`物件是否有任何特定的宣告。 例如，下列程式碼片段會逐一查看所有宣告類型，並列印每個集合的內容。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

若要將使用者登出，請`/.auth/ext/logout`使用路徑。 若要執行其他動作，請參閱[App Service 驗證和授權使用](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)的檔。 Tomcat [HttpServletRequest 介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)和其方法也有官方檔。 下列 servlet 方法也會根據您的 App Service 設定來序列化：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要停用此功能，請使用`WEBSITE_AUTH_SKIP_PRINCIPAL` `1`值建立名為的應用程式設定。 若要停用 App Service 新增的所有 servlet 篩選器，請`WEBSITE_SKIP_FILTERS`使用`1`值建立名為的設定。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。 請務必增加您的*應用程式. properties*檔案中的標頭大小上限。 建議值為 `16384`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

請遵循[繫結現有自訂 SSL 憑證](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)中的指示，上傳現有的 SSL 憑證，並將其繫結至您應用程式的網域名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用 KeyVault 參考

[Azure KeyVault](../../key-vault/key-vault-overview.md)提供具有存取原則和 audit 歷程記錄的集中式秘密管理。 您可以在 KeyVault 中儲存秘密（例如密碼或連接字串），並透過環境變數存取您應用程式中的這些秘密。

首先，遵循將應用程式[存取權授與 Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)的指示，並[在應用程式設定中 KeyVault 您的密碼參考](../app-service-key-vault-references.md#reference-syntax)。 您可以在遠端存取 App Service 終端機時，藉由列印環境變數，來驗證參考是否會解析為秘密。

若要將這些秘密插入您的春季或 Tomcat 設定檔中，請使用環境`${MY_ENV_VAR}`變數插入語法（）。 如需春季設定檔，請參閱這[外部化](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)設定的相關檔。

## <a name="configure-apm-platforms"></a>設定 APM 平臺

本節說明如何將 Linux 上的 Azure App Service 上部署的 JAVA 應用程式與 NewRelic 和 AppDynamics 應用程式效能監視（APM）平臺連接在一起。

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從 NewRelic 下載 JAVA 代理程式，它會有類似*newrelic-java-x*的檔案名。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. 透過[SSH 連線到您的 App Service 實例](app-service-linux-ssh-support.md)，並建立新的目錄 */home/site/wwwroot/apm*。
5. 將已解壓縮的 NewRelic JAVA 代理程式檔案上傳至 */home/site/wwwroot/apm*底下的目錄。 您的代理程式檔案應位於 */home/site/wwwroot/apm/newrelic*中。
6. 在 */home/site/wwwroot/apm/newrelic/newrelic.yml*修改 YAML 檔案，並將預留位置授權值取代為您自己的授權金鑰。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。
    - 如果您使用的是**WildFly**，請參閱[這裡](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)的新 new relic 檔，以取得安裝 JAVA 代理程式和 JBoss 設定的指引。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. 從 AppDynamics 網站下載 JAVA 代理程式，檔案名會類似*AppServerAgent-x*的名稱。
3. 透過[SSH 連線到您的 App Service 實例](app-service-linux-ssh-support.md)，並建立新的目錄 */home/site/wwwroot/apm*。
4. 將 JAVA 代理程式檔案上傳至 */home/site/wwwroot/apm*底下的目錄。 您的代理程式檔案應位於 */home/site/wwwroot/apm/appdynamics*中。
5. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用的是**WildFly**，請參閱[這裡](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)的 AppDynamics 檔，以取得安裝 JAVA 代理程式和 JBoss 設定的指引。

>  如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `-javaagent:/...` 選項附加至目前值的結尾。

## <a name="configure-jar-applications"></a>設定 JAR 應用程式

### <a name="starting-jar-apps"></a>啟動 JAR 應用程式

根據預設，App Service 會預期您的 JAR 應用程式會命名為*app.config*。 如果它有此名稱，則會自動執行。 針對 Maven 使用者，您可以`<finalName>app</finalName>` `<build>`在*pom*的區段中包含來設定 JAR 名稱。 您可以藉由設定`archiveFileName`屬性，[在 Gradle 中執行相同的](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)動作。

如果您想要為 JAR 使用不同的名稱，您也必須提供可執行 JAR 檔案的[啟動命令](app-service-linux-faq.md#built-in-images)。 例如： `java -jar my-jar-app.jar` 。 您可以在入口網站中的 [設定] > [一般設定] 底下，或使用名為`STARTUP_COMMAND`的應用程式設定，設定啟動命令的值。

### <a name="server-port"></a>伺服器埠

App Service Linux 會將傳入要求路由傳送至埠80，讓您的應用程式也應該在埠80上接聽。 您可以在應用程式的設定中執行此動作（例如，春天的*應用程式. 屬性*檔），或在您的啟動命令`java -jar spring-app.jar --server.port=80`中進行（例如，）。 請參閱下列適用于一般 JAVA 架構的檔：

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play 架構](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| [SQL Server] | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

若要將 tomcat 設定為使用 JAVA 資料庫連線（JDBC）或 JAVA 持續性 API （JPA），請`CATALINA_OPTS`先自訂 tomcat 在啟動時所讀入的環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或者 **，在 Azure 入口網站的 [**  > 設定**應用程式設定**] 頁面中設定環境變數。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用層級的資料來源

1. 在您專案的*中繼 INF/* 目錄中，建立一個*內容 .xml*檔案。 建立*中繼 INF/* 目錄（如果不存在）。

2. 在*內容 .xml*中，新增`Context`元素以將資料來源連結至 JNDI 位址。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

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

3. 更新應用程式的*web.config* ，以在您的應用程式中使用資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>共用伺服器層級資源

1. 如果您還沒有設定，請使用 SSH 將 */usr/local/tomcat/conf*的內容複寫到您 App Service Linux 實例上的 */home/tomcat/conf* 。

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. 在您的*伺服器*中， `<Server>`于元素內加入內容專案。

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

3. 更新應用程式的*web.config* ，以在您的應用程式中使用資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成設定

最後，將驅動程式 Jar 放在 Tomcat 路徑中，然後重新開機您的 App Service。

1. 藉由將 JDBC 驅動程式檔案放在 */home/tomcat/lib*目錄中，確保其可供 Tomcat classloader 使用。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：

    1. 在[Cloud Shell](https://shell.azure.com)中，安裝 webapp 擴充功能：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 執行下列 CLI 命令，從您的本機系統建立 SSH 通道，以 App Service：

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 使用您的 SFTP 用戶端連線到本機通道埠，並將檔案上傳至 */home/tomcat/lib*資料夾。

    或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_BASE` 重設為 `/home/tomcat`，並使用已更新的設定。

### <a name="spring-boot"></a>Spring Boot

若要連接到春季開機應用程式中的資料來源，建議您建立連接字串，並將它們插入您的*應用程式中。 properties*檔案。

1. 在 [App Service] 頁面的 [設定] 區段中，設定字串的名稱，在 [值] 欄位中貼上您的 JDBC 連接字串，並將類型設定為 [自訂]。 您可以選擇性地將此連接字串設定為位置設定。

    我們的應用程式可存取此連接字串，作為名為`CUSTOMCONNSTR_<your-string-name>`的環境變數。 例如，我們在上面建立的連接字串將會命名`CUSTOMCONNSTR_exampledb`為。

2. 在您的*應用程式. properties*檔案中，使用環境變數名稱來參考此連接字串。 在我們的範例中，我們會使用下列程式。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

如需有關此主題的詳細資訊，請參閱有關資料存取和[外部化](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)設定的[彈簧開機檔](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)。

## <a name="configure-java-ee-wildfly"></a>設定 JAVA EE （WildFly）

> [!NOTE]
> App Service Linux 上的 JAVA Enterprise Edition 目前為預覽狀態。 **不**建議將此堆疊用於生產面向的工作。

Linux 上的 Azure App Service 可讓 JAVA 開發人員在完全受控的 Linux 服務上，建立、部署及調整 JAVA Enterprise （JAVA EE）應用程式。  基礎 JAVA 企業執行時間環境是開放原始碼[WildFly](https://wildfly.org/)應用程式伺服器。

本節包含下列小節：

- [使用 App Service 進行調整](#scale-with-app-service)
- [自訂應用程式伺服器設定](#customize-application-server-configuration)
- [安裝模組和相依性](#install-modules-and-dependencies)
- [設定資料來源](#configure-data-sources)
- [啟用訊息提供者](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>使用 App Service 調整

在 Linux 上的 App Service 中執行的 WildFly 應用程式伺服器會以獨立模式執行，而不會在網域設定中執行。 當您相應放大 App Service 方案時，每個 WildFly 執行個體會設定為獨立伺服器。

使用[調整規模規則](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)以及藉由[增加執行個體計數](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以垂直或水平方式調整您的應用程式。

### <a name="customize-application-server-configuration"></a>自訂應用程式伺服器設定

Web 應用程式實例是無狀態的，因此每個啟動的新實例都必須在啟動時設定，以支援應用程式所需的 WildFly 設定。
您可以撰寫啟動 Bash 指令碼來呼叫 WildFly CLI，以便：

- 設定資料來源
- 設定傳訊提供者
- 將其他模組和相依性新增至 WildFly 伺服器設定。

當 WildFly 已啟動並執行，但在應用程式啟動之前，會執行此腳本。 腳本應該使用從 */opt/jboss/wildfly/bin/jboss-cli.sh*呼叫的[JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)來設定應用程式伺服器，以及伺服器啟動後所需的任何設定或變更。

請勿使用 CLI 的互動模式來設定 WildFly。 您可以改用 `--file` 命令來為 JBoss CLI 提供命令的指令碼，例如：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

使用 FTP，將啟動腳本上傳至 */home*目錄下 App Service 實例中的位置，例如 */home/site/deployments/tools*。 如需詳細資訊，請參閱[使用 FTP/S 將您的應用程式部署到 Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

將 Azure 入口網站中的 [**啟動腳本**] 欄位設定為啟動 shell 腳本的位置，例如 */home/site/deployments/tools/your-startup-script.sh*。

在應用程式設定中提供[應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)，以傳遞要在腳本中使用的環境變數。 應用程式設定會保留設定應用程式脫離版本控制所需的連接字串和其他祕密。

### <a name="install-modules-and-dependencies"></a>安裝模組和相依性

若要透過 JBoss CLI 將模組及其相依性安裝到 WildFly 的路徑，您必須在自己的目錄中建立下列檔案。 部分模組和相依性可能需要其他設定 (例如 JNDI 命名或其他 API 特有的設定)，因此，這份清單是您將需要在大多數情況下設定相依性之項目的基本組合。

- [XML 模組描述元](https://jboss-modules.github.io/jboss-modules/manual/#descriptors) \(英文\)。 這個 XML 檔案會定義模組的名稱、屬性和相依性。 這個[範例 module.xml 檔案](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) \(英文\) 會定義 Postgres 模組、它的 JAR 檔案 JDBC 相依性，以及其他所需的模組相依性。
- 所有適用於您模組的必要 JAR 檔案相依性。
- 含有您 JBoss CLI 命令的指令碼，可用來設定新的模組。 這個檔案所包含的命令將透過 JBoss CLI 執行，以設定伺服器來使用相依性。 如需用來新增模組、資料來源和傳訊提供者的相關文件，請參閱[這份文件](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli) \(英文\)。
- Bash 啟動指令碼，會在上一個步驟中呼叫 JBoss CLI 並執行此指令碼。 重新啟動您的 App Service 執行個體時，或在向外延展期間佈建新的執行個體時，將執行這個檔案。這個啟動指令碼是在將 JBoss 命令傳遞給 JBoss CLI 時，您可針對應用程式執行任何其他設定的地方。 至少，這個檔案可以是用來將您的 JBoss CLI 命令指令碼傳遞至 JBoss CLI 的單一命令：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

當您擁有模組的檔案和內容之後，請遵循下列步驟，將模組新增至 WildFly 應用程式伺服器。

1. 使用 FTP，將您的檔案上傳至 */home*目錄下的 App Service 實例中的位置，例如 */home/site/deployments/tools*。 如需詳細資訊，請參閱[使用 FTP/S 將您的應用程式部署到 Azure App Service](../deploy-ftp.md)。
2. **在 Azure 入口網站的 [**  > 設定] **[一般設定**] 頁面中，將 [**啟動腳本**] 欄位設為啟動 shell 腳本的位置，例如 */home/site/deployments/tools/startup.sh*。
3. 按入口網站 [**總覽**] 區段中的 [**重新開機**] 按鈕，或使用 Azure CLI，重新開機您的 App Service 實例。

### <a name="configure-data-sources"></a>設定資料來源

若要設定 WildFly/JBoss 來存取資料來源，請使用上述「安裝模組和相依性」一節中所述的一般程式。 下一節針對于 postgresql、MySQL 和 SQL Server 資料來源，提供此程式的特定詳細資訊。

本節假設您已有應用程式、App Service 實例和 Azure 資料庫服務實例。 下列指示會參考您的 App Service 名稱、其資源群組，以及您的資料庫連接資訊。 您可以在 Azure 入口網站上找到此資訊。

如果您想要從一開始就使用範例應用程式來完成整個過程，請[參閱教學課程：在 Azure](tutorial-java-enterprise-postgresql-app.md)中建立 JAVA EE 和 Postgres web 應用程式。

下列步驟說明連接現有 App Service 和資料庫的需求。

1. 下載適用于[于 postgresql](https://jdbc.postgresql.org/download.html)、 [MySQL](https://dev.mysql.com/downloads/connector/j/)或[SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)的 JDBC 驅動程式。 解壓縮下載的封存以取得驅動程式 .jar 檔案。

2. 使用類似*module*的名稱建立檔案，然後新增下列標記。 將`<module name>`預留位置（包括角括弧`org.postgres` ）取代為適用于于 postgresql、 `com.mysql`適用于 MySQL 的`com.microsoft` ，或用於 SQL Server。 將`<JDBC .jar file path>`取代為上一個步驟中的 .jar 檔案名稱，包括將檔案放在 App Service 實例中的位置完整路徑。 這可以是 */home*目錄下的任何位置。

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. 建立名稱類似*datasource-commands*的檔案，並新增下列程式碼。 將`<JDBC .jar file path>`取代為您在上一個步驟中使用的值。 將`<module file path>`取代為上一個步驟中的檔案名和 App Service 路徑，例如 */home/module.xml*。

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    這個檔案是由下一個步驟中所述的啟動腳本執行。 它會將 JDBC 驅動程式安裝為 WildFly 模組、建立對應的 WildFly 資料來源，並重載伺服器以確保變更生效。

4. 建立名稱類似*startup.sh*的檔案，並新增下列程式碼。 將`<JBoss CLI script>`取代為您在上一個步驟中建立的檔案名。 請務必包含您將在 App Service 實例中放置檔案之位置的完整路徑，例如 */home/datasource-commands.cli*。

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. 使用 FTP 將 JDBC .jar 檔案、模組 XML 檔案、JBoss CLI 腳本和啟動腳本上傳至您的 App Service 實例。 將這些檔案放在您在先前步驟中指定的位置，例如 */home*。 如需 FTP 的詳細資訊，請參閱[使用 ftp/S 將您的應用程式部署到 Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

6. 使用 [Azure CLI] 將設定新增至保存資料庫連接資訊的 App Service。 將`<resource group>` 和`<webapp name>`取代為您的 App Service 所使用的值。 將`<database server name>` 、`<database name>`、和`<admin password>`取代為您的資料庫連接資訊。 `<admin name>` 您可以從 Azure 入口網站取得您的 App Service 和資料庫資訊。

    **于 postgresql**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    每個資料庫伺服器的 DATABASE_CONNECTION_URL 值都不同，而且不同于 Azure 入口網站上的值。 WildFly 需要使用此處所示的 URL 格式（和上述程式碼片段）：

    * **于 postgresql：** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL：** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server：** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. 在 Azure 入口網站中，流覽至您的 App Service 並**尋找**   > **設定 一般設定** 頁面。 將 [**啟動腳本**] 欄位設定為啟動腳本的名稱和位置，例如 */home/startup.sh*。

下次 App Service 重新開機時，它將會執行啟動腳本，並執行必要的設定步驟。 若要測試此設定是否正確發生，您可以使用 SSH 存取您的 App Service，然後從 Bash 提示字元自行執行啟動腳本。 您也可以檢查 App Service 記錄。 如需這些選項的詳細資訊，請參閱[記錄和偵錯工具](#logging-and-debugging-apps)。

接下來，您將需要更新應用程式的 WildFly 設定，並重新部署它。 請使用下列步驟：

1. 開啟應用程式的*src/main/resources/META-INF/持續性 .xml*檔案，並尋找`<jta-data-source>`元素。 取代其內容，如下所示：

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. 在 Bash 提示字元中使用下列命令，重建並重新部署您的應用程式：

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. 在 Azure 入口網站的 [**總覽**] 區段中按下 [**重新開機**] 按鈕，或使用 Azure CLI，重新開機您的 App Service 實例。

您的 App Service 實例現在已設定為可存取您的資料庫。

如需使用 WildFly 設定資料庫連線的詳細資訊，請參閱[于 postgresql](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)、 [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)或[SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)。

### <a name="enable-messaging-providers"></a>啟用訊息提供者

若要使用服務匯流排啟用訊息導向的 Bean 作為傳訊機制：

1. 使用 [Apache QPId JMS 傳訊程式庫](https://qpid.apache.org/proton/index.html) \(英文\)。 針對應用程式，在您的 pom.xml (或其他組建檔案) 中包含此相依性。

2. 建立[服務匯流排資源](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)。 建立 Azure 服務匯流排命名空間及該命名空間內的佇列，以及具有傳送和接收功能的共用存取原則。

3. 藉由針對您的原則主要金鑰進行 URL 編碼或[使用服務匯流排 SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)，將共用存取原則金鑰傳遞至您的程式碼。

4. 透過適用於 JMS 提供者的模組 XML 描述元、.jar 相依性、JBoss CLI 命令及啟動指令碼，來遵循＜安裝模組和相依性＞一節中概述的步驟。 除了這四個檔案，您也必須建立一個 XML 檔案，來定義 JMS 佇列和主題的 JNDI 名稱。 如需參考設定檔，請參閱[這個存放庫](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)。

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>使用 Redis 作為 Tomcat 的會話快取

您可以設定 Tomcat 使用外部會話存放區，例如[Azure Cache For Redis](/azure/azure-cache-for-redis/)。 這可讓您在使用者轉移至應用程式的另一個實例時，保留使用者會話狀態（例如購物車資料），例如在發生自動調整、重新開機或容錯移轉的情況下。

若要搭配使用 Tomcat 與 Redis，您必須將應用程式設定為使用[PersistentManager](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html)的執行。 下列步驟會使用[Pivotal 會話管理員來說明此程式： redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store)作為範例。

1. 開啟 Bash 終端機，並`export <variable>=<value>`使用來設定下列每個環境變數。

    | 變數                 | 值                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | 包含您 App Service 實例之資源群組的名稱。       |
    | WEBAPP_NAME              | App Service 實例的名稱。                                     |
    | WEBAPP_PLAN_NAME         | App Service 方案的名稱                                          |
    | 地區                   | 裝載應用程式的區功能變數名稱稱。                           |
    | REDIS_CACHE_NAME         | Azure Cache for Redis 實例的名稱。                           |
    | REDIS_PORT               | 您的 Redis 快取接聽的 SSL 埠。                             |
    | REDIS_PASSWORD           | 實例的主要存取金鑰。                                  |
    | REDIS_SESSION_KEY_PREFIX | 您指定的值，用來識別來自您應用程式的工作階段金鑰。 |

    您可以在服務實例的 [**屬性**] 或 [**存取金鑰**] 區段中，尋找 Azure 入口網站上的名稱、埠和存取金鑰資訊。

2. 使用下列內容建立或更新您應用程式的*src/main/webapp/META-INF/上下文 .xml*檔案：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    此檔案會指定並設定您應用程式的會話管理員執行。 它會使用您在上一個步驟中設定的環境變數，將您的帳戶資訊保留在原始程式檔中。

3. 使用 FTP 將會話管理員的 JAR 檔案上傳到您的 App Service 實例，並將它放在 */home/tomcat/lib*目錄中。 如需詳細資訊，請參閱[使用 FTP/S 將您的應用程式部署到 Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

4. 停用 App Service 實例的[會話親和性 cookie](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 。 若要這麼做，您可以流覽至您 Azure 入口網站的應用程式，然後將 **[設定 > 一般設定] > [ARR 親和性**] 設為 [**關閉**]。 或者，您可以使用下列命令：

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    根據預設，App Service 會使用會話親和性 cookie，以確保具有現有會話的用戶端要求會路由傳送至應用程式的相同實例。 此預設行為不需要任何設定，但當您的應用程式實例重新開機時，或當流量路由傳送到另一個實例時，就無法保留使用者會話狀態。 當您[停用現有的 ARR 實例相似性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)設定以關閉以會話 cookie 為基礎的路由時，您可以讓已設定的會話存放區在不受干擾的情況下運作。

5. 流覽至 App Service 實例的 [**屬性**] 區段，然後尋找**其他的輸出 IP 位址**。 這些代表您的應用程式所有可能的輸出 IP 位址。 複製這些，以便在下一個步驟中使用。

6. 針對每個 IP 位址，在您的 Azure Cache for Redis 實例中建立防火牆規則。 您可以從 Redis 實例的 [**防火牆**] 區段中的 [Azure 入口網站] 執行此動作。 為每個規則提供唯一的名稱，並將 [**起始 ip 位址**] 和 [**結束 ip 位址**] 值設為相同的 IP 位址。

7. 流覽至 Redis 實例的 [ **Advanced settings** ] 區段，並將 [**僅允許透過 SSL 存取**] 設定為 [**否**]。 這可讓您的 App Service 實例透過 Azure 基礎結構與 Redis 快取進行通訊。

8. 更新您`azure-webapp-maven-plugin`應用程式的*pom .xml*檔案中的設定，以參考您的 Redis 帳戶資訊。 此檔案會使用您先前設定的環境變數，將您的帳戶資訊保留在原始程式檔中。

    如有必要，請將 `1.7.0` 變更為最新版的 [Maven 外掛程式 (適用於 Azure App Service)](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)。

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. 重建並重新部署您的應用程式。

    ```bash
    mvn package
    mvn azure-webapp:deploy
    ```

您的應用程式現在會使用您的 Redis 快取來進行會話管理。

如需您可以用來測試這些指示的範例，請參閱 GitHub 上的[調整狀態-java-web 應用程式-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure)存放庫。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="statement-of-support"></a>支援聲明

### <a name="runtime-availability"></a>執行時間可用性

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 支援的版本為 JAVA 8 和11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

OpenJDK 的 Azul Zulu Enterprise 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。 您可以從[JAVA Jdk 安裝](https://aka.ms/azure-jdks)安裝 JDK。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Java 開發人員的 Azure](/java/azure/) 中心，以找出 Azure 快速入門、教學課程和 Java 參考文件。

[App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。
