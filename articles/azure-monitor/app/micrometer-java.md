---
title: 如何搭配 Azure Application Insights SDK 使用 Micrometer | Microsoft Docs
description: '搭配 Application Insights Spring Boot 和非 Spring Boot 應用程式使用 Micrometer 的逐步解說指南。 '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 1074495f5ac9112b6ce4f67ad2d81ee57b28e720
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012700"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>如何搭配 Azure Application Insights Java SDK 使用 Micrometer
Micrometer 應用程式監視會測量以 JVM 為基礎之應用程式程式碼的計量，並可讓您將資料匯出到您慣用的監視系統。 這篇文章會教導您如何搭配 Application Insights Spring Boot 和非 Spring Boot 應用程式使用 Micrometer。

## <a name="using-spring-boot-15x"></a>使用 Spring Boot 1.5x
將以下相依性新增至您的 pom.xml 或 build.gradle 檔案： 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA 或更新版本
* Micrometer Azure Registry 1.1.0 或更新版本
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 或更新版本 (這會反向移植 Spring Framework 中的自動設定程式碼)。
* [ApplicationInsights 資源](../../azure-monitor/app/create-new-resource.md )

步驟

1. 更新您的 Spring Boot 應用程式的 pom.xml 檔案，並在其中新增下列相依性：

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. 使用以下屬性搭配 Application Insights 檢測金鑰更新 application.properties 或 yml 檔案：

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. 建置應用程式並執行
2. 上述動作應該能讓您的應用程式啟動，並使用自動收集至 Azure 監視器的預先彙總計量。 如需如何微調 Application Insights Spring Boot 簡易版的詳細資訊，請參閱 [GitHub 上的讀我檔案](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md)。

## <a name="using-spring-2x"></a>使用 Spring 2.x

將以下相依性新增至您的 pom.xml 或 build.gradle 檔案：

* Application Insights Spring-boot-starter 2.1.2 或更新版本
* Azure-短期開機-計量-初學者2.1.5 或更高版本  
* [Application Insights 資源](../../azure-monitor/app/create-new-resource.md )

步驟：

1. 更新您 Spring Boot 應用程式的 pom.xml 檔案，並在其中新增以下相依性：

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.1.6</version>
    </dependency>
    ```
1. 使用以下屬性搭配 Application Insights 檢測金鑰更新 application.properties 或 yml 檔案：

     `management.metrics.export.azuremonitor.instrumentation-key=<your-instrumentation-key-here>`
3. 建置應用程式並執行
4. 上述動作應該能讓您的應用程式使用自動收集至 Azure 監視器的預先彙總計量。 如需如何微調 Application Insights Spring Boot 簡易版的詳細資訊，請參閱 [GitHub 上的讀我檔案](https://github.com/Microsoft/azure-spring-boot/releases/latest)。

預設度量：

*    針對 Tomcat、JVM、Logback 度量、Log4J 度量、執行時間度量、處理器度量，以及 FileDescriptorMetrics 自動設定的度量。
*    例如，如果類別路徑中有 netflix hystrix，我們就能獲得那些度量。 
*    下列計量可以藉由新增個別的 bean 來提供使用。 
        - CacheMetrics (CaffeineCache、EhCache2、GuavaCache、HazelcaseCache、Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 度量 
        - Kafka 度量 

 

如何關閉自動度量收集： 
 
- JVM 度量： 
    - management.metrics.binders.jvm.enabled=false 
- Logback 度量： 
    - management.metrics.binders.jvm.enabled=false
- 執行時間度量： 
    - management.metrics.binders.jvm.enabled=false 
- 處理器度量：
    -  management.metrics.binders.jvm.enabled=false 
- FileDescriptorMetrics：
    - management.metrics.binders.jvm.enabled=false 
- 如果類別路徑上的程式庫有 Hystrix 度量： 
    - management.metrics.binders.hystrix.enabled=false 
- 如果類別路徑上的程式庫有 AspectJ 度量： 
    - spring.aop.enabled=false 

> [!NOTE]
> 在 Spring Boot 應用程式的 application.properties 或 application.yml 檔案中指定上述屬性

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>搭配非 Spring Boot web 應用程式使用 Micrometer

將以下相依性新增至您的 pom.xml 或 build.gradle 檔案：
 
* [Application Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) 或更新版本
* [Application Insights Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) 或更新版本
* [註冊網站篩選](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Micrometer Azure Registry 1.1.0 或更新版本
* [Application Insights 資源](../../azure-monitor/app/create-new-resource.md )

步驟：

1. 在您的 pom.xml 或 build.gradle 檔案中新增以下相依性：

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. 將 Application Insights.xml 放在 [資源] 資料夾中

    範例 Servlet 類別 (會發出計時器度量)：

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

      範例組態類別：

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

若要深入了解計量，請參閱 [Micrometer 文件](https://micrometer.io/docs/)。

您可以在[官方 Micrometer GitHub 存放庫](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)找到有關如何建立不同類型度量的其他範例程式碼。

## <a name="how-to-bind-additional-metrics-collection"></a>如何繫結其他度量集合

### <a name="springbootspring"></a>SpringBoot/Spring

建立個別計量類別目錄的 bean。 例如，假設我們需要 Guava 快取計量：

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
有數個計量是預設為不啟用的，但可以透過上述方式結合。 如需完整清單，請參閱[官方 Micrometer GitHub 存放庫](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )。

### <a name="non-spring-apps"></a>非 Spring 應用程式
將下列繫結程式碼新增至組態檔：
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Micrometer，請參閱官方 [Micrometer 文件](https://micrometer.io/docs)。
* 若要了解 Azrue 上的 Spring，請參閱官方 [Spring on Azure 文件](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)。
