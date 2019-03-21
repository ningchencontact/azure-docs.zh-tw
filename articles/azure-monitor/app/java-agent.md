---
title: Azure Application Insights 中 Java Web 應用程式的效能監視 | Microsoft Docs
description: 使用 Application Insights 延伸 Java 網站的效能和使用量監視。
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004040"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>監視 Java Web 應用程式中的相依性、擷取到的例外狀況和方法執行時間


如果您已[使用 Application Insights 檢測您的 Java Web 應用程式][java]，您可以使用 Java 代理程式獲得更深入的見解，而不需變更任何程式碼：

* **相依性：** 您的應用程式對其他元件呼叫的相關資料，包括：
  * 擷取透過 HttpClient、OkHttp 和 RestTemplate (Spring) 進行的 **REST 呼叫**。
  * 擷取透過 Jedis 用戶端進行的 **Redis 呼叫**。
  * **[JDBC 呼叫](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - 自動擷取 MySQL、SQL Server 及 Oracle DB 命令。 MySQL 的呼叫時間如果比 10 秒長，代理程式會回報查詢計劃。
* **攔截到例外狀況：** 由程式碼處理的例外狀況相關資訊。
* **方法執行時間：** 執行特定的方法所花費的時間相關資訊。

若要使用 Java 代理程式，您要在伺服器上安裝它。 您必須使用 [Application Insights Java SDK][java] 檢測您的 Web 應用程式。 

## <a name="install-the-application-insights-agent-for-java"></a>安裝 Java 的 Application Insights 代理程式
1. 在執行 Java 伺服器的電腦上[下載代理程式](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)。 請確定下載的 Java 代理程式版本與 Application Insights Java SDK 核心和網頁套件相同。
2. 編輯應用程式伺服器啟動指令碼，並加入下列 JVM：
   
    `javaagent:`*代理程式 JAR 檔案的完整路徑*
   
    例如，在 Linux 機器上的 Tomcat 中：
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 重新啟動您的應用程式伺服器。

## <a name="configure-the-agent"></a>設定代理程式
建立名為 `AI-Agent.xml` 的檔案，並將它放在代理程式 JAR 檔案所在的同一資料夾中。

設定 XML 檔案的內容。 編輯下列範例以包含或省略您要的功能。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />
           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

您必須啟用報告例外狀況和個別方法的方法執行時間。

根據預設，`reportExecutionTime` 為 true，而 `reportCaughtExceptions` 為 false。

## <a name="additional-config-spring-boot"></a>其他的組態 (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

針對 Azure App Service 中下列工作：

* 選取 [設定] > [應用程式設定]
* 在 [應用程式設定] 之下，新增索引鍵值組︰

機碼：`JAVA_OPTS` 值： `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

最新版本的 Java 代理程式檢查 版本[此處](https://github.com/Microsoft/ApplicationInsights-Java/releases
)。 

代理程式必須封裝為您的專案中的資源，使得它最終會在 d: / home/site/wwwroot/目錄。 您可以確認您的代理程式是否在正確的應用程式服務目錄移至**開發工具** > **進階工具** > **偵錯主控台**並檢查網站目錄的內容。    

* 儲存設定，然後重新啟動您的應用程式。 （這些步驟只適用於在 Windows 上執行的應用程式服務。）

> [!NOTE]
> AI-Agent.xml 與代理程式 jar 檔案應在同一個資料夾中。 它們通常一起放在專案的 `/resources` 資料夾中。  

### <a name="spring-rest-template"></a>Spring Rest 範本

為了讓 Application Insights 成功檢測使用 Spring 的 Rest 範本所做的 HTTP 呼叫，則需使用 Apache HTTP 用戶端。 根據預設，Spring 的 Rest 範本不會設定為使用 Apache HTTP 用戶端。 在 Spring Rest 範本的建構函式中指定 [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html)，該範本會使用 Apache HTTP。

以下是如何使用 Spring Beans 來執行此作業的範例。 這是一個非常簡單的範例，其使用處理站類別的預設設定。

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>啟用 W3C 分散式追蹤

將下列程式碼新增至 AI-Agent.xml：

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> 回溯相容模式預設為啟用，enableW3CBackCompat 參數為選擇性，且應僅用於您想要將它關閉時。 

理想的狀況是，所有的服務已更新至支援 W3C 通訊協定的新版 SDK。 強烈建議儘快移至具備 W3C 支援的新版 SDK。

確定**[傳入](correlation.md#w3c-distributed-tracing)與傳出 (代理程式) 設定兩者**完全相同。

## <a name="view-the-data"></a>檢視資料
在 Application Insights 資源中，彙總的遠端相依性和方法執行時間會出現[在效能圖格下][metrics]。

若要搜尋相依性、例外狀況及方法報告的個別執行個體，請開啟[搜尋][diagnostic]。

[診斷相依性問題 - 深入了解](../../azure-monitor/app/asp-net-dependencies.md#diagnosis)。

## <a name="questions-problems"></a>有疑問嗎？ 有問題嗎？
* 沒有資料？ [設定防火牆例外狀況](../../azure-monitor/app/ip-addresses.md)
* [疑難排解 Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
