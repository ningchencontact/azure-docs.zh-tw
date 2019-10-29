---
title: Azure Spring Cloud 的疑難排解指南 | Microsoft Docs
description: Azure Spring Cloud 的疑難排解指南
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ee51841046962a6896b4c16e651f85ff761a69fc
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592471"
---
# <a name="troubleshooting-guide-for-common-problems"></a>常見問題的疑難排解指南

此文章將針對使用 Azure Spring Cloud 的開發人員，詳細說明一些常見問題與疑難排解步驟。 我們也建議您閱讀我們的[常見問題文章](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、效能和應用程式問題

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>我的應用程式無法啟動 (例如，無法連線端點，或在幾次重試之後傳回 502)

將記錄匯出至 _Azure Log Analytics_。 Spring 應用程式記錄的資料表名為 `AppPlatformLogsforSpring`。 若要深入了解，請瀏覽[使用診斷設定來分析記錄和計量](diagnostic-services.md)

在記錄中找到下列錯誤，表示兩個可能問題的其中一個：

`org.springframework.context.ApplicationContextException: Unable to start web server`

* 其中一個 Bean 遺漏或其一個相依性遺漏。
* 其中一個 Bean 屬性遺漏或無效。 在此情況下，您可能會看到 `java.lang.IllegalArgumentException`。

服務繫結可能也會導致應用程式啟動失敗。 使用繫結服務相關關鍵字來查詢記錄。  例如，假設您的應用程式繫結至設定為本機系統時間的 MySQL 執行個體。 如果應用程式啟動失敗，您可能會在記錄中發現下列錯誤：

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

若要修正此錯誤，請移至 MySql 執行個體的 `server parameters`，並將 `time_zone` 從 `SYSTEM` 變更為 `+0:00`。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的應用程式損毀或擲回未預期的錯誤

當偵錯應用程式損毀時，請從檢查應用程式的執行狀態和探索狀態開始。 在 Azure 入口網站中移至 [應用程式管理]  ，以確保所有應用程式都是 [執行中]  且 [已啟動]  。

* 如果狀態為 [執行中]  ，但探索狀態不是 [已啟動]  ，請移至[我的應用程式無法註冊](#my-application-cannot-be-registered)。

* 如果探索狀態為 [已啟動]  ，您可以移至 [計量]  來檢查應用程式的健康情況。 檢查下列計量：


  - `TomcatErrorCount` (_tomcat.global.error_)：所有 Spring 應用程式例外狀況都會在此計算。 如果此數目很大，請移至 _Azure Log Analytics_ 來檢查您的應用程式記錄。

  - `AppMemoryMax` (_jvm.memory.max_)：應用程式可使用記憶體的最大數量。 此值可能並未定義，或在定義後隨時間變更。 已使用和認可的記憶體數量一律會小於或等於最大值 (如果已定義此值)。 不過，如果嘗試增加使用的記憶體，以致使用的記憶體大於認可的記憶體 (即使使用的記憶體仍會小於等於最大值)，則記憶體配置可能因為 `OutOfMemoryError` 而失敗。 在這種情況下，請嘗試透過 `-Xmx` 參數提高堆積大小上限。

  - `AppMemoryUsed` (_jvm.memory.used_)：應用程式目前使用的記憶體數量 (以位元組為單位)。 若為正常負載 Java 應用程式，此計量序列會形成「鋸齒」模式，其中的記憶體使用量會以小幅增量的方式穩定增加和減少，然後突然大幅下降，此模式會重複出現。 這是因為 Java 虛擬機器內部的記憶體回收，其中的回收動作代表「鋸齒」的下降部分。
    此計量對於找出記憶體問題極為重要，例如：* 一開始的記憶體爆炸 * 特定邏輯路徑的記憶體配置激增 * 逐漸的記憶體流失

  如需詳細資料，請瀏覽[計量](spring-cloud-concept-metrics.md)。

瀏覽[此使用者入門文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) \(部分機器翻譯\)，以開始使用 _Azure Log Analytics_。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的應用程式遇到高 CPU 使用量或高記憶體使用量

如果您的應用程式遇到高 CPU/記憶體使用量，則以下兩件事其中一個為真：
* 所有應用程式執行個體都遇到高 CPU/記憶體使用量，或
* 有些應用程式執行個體遇到高 CPU/記憶體使用量。

若要確認這是哪種情況，

1. 移至 [計量]  ，選取 `Service CPU Usage Percentage` 或 `Service Memory Used`，
2. 新增 `App=` 篩選條件以指定您要監視的應用程式，以及
3. 依照 `Instance` 分割計量。

如果所有執行個體都遇到高 CPU/記憶體，您必須相應放大應用程式或相應增加 CPU/記憶體。 如需詳細資料，請瀏覽[調整應用程式](spring-cloud-tutorial-scale-manual.md)

如果有些執行個體遇到高 CPU/記憶體，請檢查執行個體狀態及其探索狀態。

如需詳細資料，請瀏覽[計量](spring-cloud-concept-metrics.md)。

如果所有執行個體都已啟動且正在執行中，請移至 _Azure Log Analytics_ 來查詢您的應用程式記錄並檢閱您的程式碼邏輯，查看是否有任何項目可能影響級別分割。 如需詳細資料，請瀏覽[使用診斷設定來分析記錄和計量](diagnostic-services.md)。

瀏覽[此使用者入門文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) \(部分機器翻譯\)，以開始使用 _Azure Log Analytics_。 使用 [Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)來查詢記錄。

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>將 Spring 應用程式上架到 Azure Spring Cloud 前的檢查清單

* 此應用程式可以使用指定的 Java 執行階段版本在本機執行。
* 環境設定 (CPU/RAM/執行個體) 符合應用程式提供者設定的最低需求。
* 組態項目具有預期的值。 如需詳細資訊，請參閱[設定伺服器](spring-cloud-tutorial-config-server.md)。
* 環境變數具有預期的值。
* JVM 參數具有預期的值。
* 我們建議您在應用程式封裝中停用/移除內嵌的「設定伺服器」  和「Spring 服務登錄」  服務。
* 如果要透過_服務繫結_來繫結任何 Azure 資源，請確定目標資源已啟動且正在執行中。

## <a name="configuration-and-management"></a>設定和管理

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>我在建立 Azure Spring Cloud 服務執行個體時遇到問題

當您嘗試透過入口網站佈建 _Azure Spring Cloud_ 服務執行個體時，Azure Spring Cloud 會為您執行驗證。

不過，如果您嘗試透過 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 或 [Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/)佈建 _Azure Spring Cloud_ 服務執行個體，請確認：

* 訂用帳戶是作用中狀態。
* _Azure Spring Cloud_ [支援](spring-cloud-faq.md)此位置。
* 已經建立執行個體的資源群組。
* 資源名稱符合命名規則。 (只能包含小寫字母、數字和連字號。 第一個字元必須是字母。 最後一個字元必須是字母或數字。 此值長度必須介於 2 至 32 個字元之間。)

如果您嘗試透過 Resource Manager 範本佈建 _Azure Spring Cloud_ 服務執行個體，請瀏覽 https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates 來檢查範本語法。

_Azure Spring Cloud_ 服務執行個體的名稱將用於在 `azureapps.io` 下要求子網域名稱，因此如果此名稱與現有名稱衝突，佈建將會失敗。 您可以在活動記錄中找到更多詳細資料。

### <a name="i-cannot-deploy-a-jar-package"></a>我無法部署 JAR 套件

您無法透過入口網站或 Resource Manager 範本上傳 JAR/來源套件。

當您使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 部署應用程式套件時，它會定期輪詢部署進度，最後顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

請確定您的應用程式是以正確的[可執行檔 jar 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html) \(英文\) 封裝。 如果不是，您會看到如下的錯誤：

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>我無法部署來源套件

您無法透過入口網站或 Resource Manager 範本上傳 JAR/來源套件。

當您透過 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 部署應用程式套件時，它會定期輪詢部署進度，最後顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取建置和部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

不過，請注意，一個 _Azure Spring Cloud_ 服務執行個體一次只能針對一個來源封裝觸發一個組建作業。 如需詳細資料，請參閱[部署應用程式](spring-cloud-quickstart-launch-app-portal.md)和[預備環境指南](spring-cloud-howto-staging-environment.md)。

### <a name="my-application-cannot-be-registered"></a>我的應用程式無法註冊

在大部分情況下，發生此狀況的原因是未適當地在 POM 檔案中設定必要相依性/服務探索。 一旦設定，內建服務登錄伺服器端點會插入為您應用程式的環境變數。 應用程式接著會向服務登錄伺服器註冊自己，並探索其他相依的微服務。

先等待至少 2 分鐘，讓新註冊的執行個體開始接收流量。

如果您要將現有 Spring Cloud 型解決方案移轉至 Azure，請確定您的特定「服務登錄」  和「設定伺服器」  執行個體都已移除 (或已停用)，以避免與 _Azure Spring Cloud_ 所提供的受控執行個體發生衝突。

您也可以在 _Azure Log Analytics_ 中檢查「服務登錄」  用戶端記錄。 如需詳細資料，請瀏覽[使用診斷設定來分析記錄和計量](diagnostic-services.md)

瀏覽[此使用者入門文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) \(部分機器翻譯\)，以開始使用 _Azure Log Analytics_。 使用 [Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)來查詢記錄。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要檢查應用程式的環境變數

環境變數會提供 Azure Spring Cloud 架構的資訊，以確保 Azure 了解設定您應用程式組成服務的位置和方式。  確保環境變數正確是針對潛在問題進行疑難排解的第一步。  您可以使用 Spring Boot Actuator 端點來檢查您的環境變數。  

> [!WARNING]
> 此程序會使用您的測試端點來公開您的環境變數。  如果您的測試端點可公開存取，或您已將網域名稱指派給您的應用程式，則請勿繼續執行該作業。

1. 瀏覽至 URL：`https://<your application test endpoint>/actuator/health`  
    - 類似於 `{"status":"UP"}` 的回應會指出端點已啟用。
    - 如果回應是負面結果，請在您的 `POM.xml` 中加入下列相依性：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 啟用 Spring Boot Actuator 端點後，請移至 Azure 入口網站，然後尋找您應用程式的設定頁面。  新增名為 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE' and the value `*` 的環境變數。 

1. 重新啟動您的應用程式。

1. 流覽至 `https://<the test endpoint of your app>/actuator/env` 並檢查回應。  它看起來應該如下所示：

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

尋找名為 `systemEnvironment` 的子節點。  此節點包含您應用程式的環境變數。

> [!IMPORTANT]
> 請記得先撤銷公開環境變數的設定，再讓應用程式可公開存取。  移至 Azure 入口網站，尋找應用程式的設定頁面，然後刪除此環境變數：`MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`。

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>我找不到我的應用程式的計量或記錄

移至 [應用程式管理]  ，確定應用程式為「執行中」  且「已啟動」  。

如果您可以看到來自 _JVM_ 的計量，但看不到來自 _Tomcat_ 的計量，請檢查 `spring-boot-actuator` 相依性是否已在您的應用程式套件中啟用並成功啟動。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果您的應用程式記錄可以封存到儲存體帳戶，但不會傳送至 _Azure Log Analytics_，請檢查[您的工作區是否設定正確](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果您使用 _Azure Log Analytics_ 的免費層，請注意，[免費層不提供 SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。