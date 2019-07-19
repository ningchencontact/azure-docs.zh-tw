---
title: 探索 Azure 應用程式 Insights 中的 JAVA 追蹤記錄 (2.5.0-BETA) |Microsoft Docs
description: 在 Application Insights 中搜尋 Log4J 或 Logback 追蹤 (2.5.0-BETA)
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 028563658256f7bd8e016b5c7bbf703a5030a3de
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298356"
---
# <a name="explore-java-trace-logs-in-application-insights-250-beta"></a>探索 Application Insights 中的 JAVA 追蹤記錄 (2.5.0-BETA)
如果您使用 Logback 或 Log4J (v1.2 或 v2.0) 進行追蹤，您可以將追蹤記錄自動傳送到 Application Insights，您可以在其中探索及搜尋記錄。

## <a name="note-if-you-are-using-the-application-insights-java-agent"></a>注意: 如果您使用 Application Insights JAVA 代理程式

您可以藉由啟用檔案中`AI-Agent.xml`的功能, 將 Application Insights JAVA 代理程式設定為自動捕捉您的記錄檔:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

否則 。

## <a name="install-the-java-sdk"></a>安裝 Java SDK

依照指示安裝[APPLICATION INSIGHTS SDK For JAVA][java](如果您尚未這麼做)。

## <a name="add-logging-libraries-to-your-project"></a>將記錄程式庫加入至專案
*選擇適合您的專案的方式。*

#### <a name="if-youre-using-maven"></a>如果您使用 Maven...
如果您的專案已設定為使用 Maven 來建置，請將下列其中一個程式碼片段合併至 pom.xml 檔案。

然後重新整理專案相依性，以下載程式庫。

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>如果您使用 Gradle...
如果您的專案已設定為使用 Gradle 來建置，請將下列其中一行加入至 build.gradle 檔案中的 `dependencies` 群組：

然後重新整理專案相依性，以下載程式庫。

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>否則...
依照指導方針手動安裝 Application Insights Java SDK，下載適當附加器的 jar (到達 Maven 中央頁面之後，在下載區段中按一下 'jar' 連結)，然後將下載的附加器 jar 新增至專案。

| 記錄器 | 下載 | 程式庫 |
| --- | --- | --- |
| Logback |[Logback 附加器 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 附加器 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Log4J v1.2 附加器 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>將附加器加入至記錄架構
若要開始進行追蹤，請將相關的程式碼片段合併到 Log4J 或 Logback 組態檔案： 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights 附加器可由任何設定的記錄器參考，而不一定是根記錄器 (如以上程式碼範例所示)。

## <a name="explore-your-traces-in-the-application-insights-portal"></a>在 Application Insights 入口網站中探索您的追蹤
現在您已將專案設定為將追蹤傳送到 Application Insights, 您可以在 Application Insights 入口網站的 [[搜尋][diagnostic]] 分頁中, 查看並搜尋這些追蹤。

透過記錄器提交的例外狀況會在入口網站上顯示為例外狀況遙測。

![在 Application Insights 入口網站中，開啟 [搜尋]](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>後續步驟
[診斷搜尋][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


