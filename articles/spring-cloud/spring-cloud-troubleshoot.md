---
title: Azure 春季雲端的疑難排解指南 |Microsoft Docs
description: Azure 春季雲端的疑難排解指南
author: jpconnock
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 9603f4a687b55f45be2875ccaa7b801c0c5589c9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607618"
---
# <a name="troubleshooting-guide-for-common-problems"></a>常見問題的疑難排解指南

本文詳細說明在 Azure 春季雲端中工作的開發人員所遇到的一些常見問題和疑難排解步驟。 我們也建議您閱讀我們的[常見問題文章](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、效能和應用程式問題

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>我的應用程式無法啟動（例如，無法連接端點，或在幾次重試後傳回502）

將記錄匯出至_Azure Log Analytics_。 春季應用程式記錄的資料表名稱 `AppPlatformLogsforSpring`。 若要深入瞭解，請造訪[使用診斷設定來分析記錄和計量](diagnostic-services.md)

在記錄中尋找下列錯誤，表示兩個可能的問題之一：

`org.springframework.context.ApplicationContextException: Unable to start web server`

* 缺少其中一項 bean 或其相依性的其中一個。
* 其中一個 bean 屬性遺失或無效。 在此情況下，您可能會看到 `java.lang.IllegalArgumentException`。

服務系結可能也會導致應用程式啟動失敗。 使用與系結服務相關的關鍵字來查詢記錄。  例如，假設您的應用程式系結至將 MySQL 實例設定為本機系統時間。 如果應用程式無法啟動，您可能會在記錄檔中發現下列錯誤：

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

若要修正此錯誤，請移至 MySql 實例的 `server parameters`，並將 `time_zone` 從 `SYSTEM` 變更為 `+0:00`。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的應用程式當機或擲回未預期的錯誤

當偵測應用程式當機，請從檢查應用程式的執行狀態和探索狀態開始。 移至 Azure 入口網站中的 [_應用程式管理_]，以確保所有應用_程式都在_執行_中。_

* 如果狀態是 [執行中] _，但探索_狀態不是 [已_啟動_]，請移至 [[我的應用程式無法註冊](#my-application-cannot-be-registered)]。

* 如果探索狀態為 [已_啟動_]，請移至 [_計量_] 來檢查應用程式的健康情況。 檢查下列計量：


  - `TomcatErrorCount` （_tomcat. 全域錯誤_）：所有春季應用程式例外狀況都會在此計算。 如果此數目很大，請移至_Azure Log Analytics_來檢查您的應用程式記錄。

  - `AppMemoryMax` （_jvm_）：可供應用程式使用的記憶體數量上限。 若已定義，則可能未定義或變更一段時間。 已使用和認可的記憶體數量一律會小於或等於最大值（如果已定義）。 不過，如果記憶體配置嘗試增加已使用的記憶體（例如 >，即使使用 < = max 仍為 true 時），則可能會因 `OutOfMemoryError` 而失敗。 在這種情況下，請嘗試透過 `-Xmx` 參數增加堆積大小上限。

  - `AppMemoryUsed` （_jvm_）：應用程式目前使用的記憶體數量（以位元組為單位）。 對於一般的 load JAVA 應用程式，此計量數列會形成「sawtooth」模式，其中記憶體使用量會穩定地增加並減少，而且突然下降，而此模式會重複出現。 這是因為 JAVA 虛擬機器中的垃圾收集，其中集合動作代表 ' sawteeth ' 上的 drop。
    此計量對於識別記憶體問題很重要，例如： * 在一開始的記憶體激增（針對特定邏輯路徑的激增記憶體配置 * 漸進式記憶體流失）

  如需詳細資訊，請流覽[計量](spring-cloud-concept-metrics.md)。

請造訪此使用者入門[文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)，以開始使用_Azure Log Analytics_。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的應用程式會遇到高 CPU 使用率或高記憶體使用量

如果您的應用程式遇到高 CPU/記憶體使用量，則下列兩個專案的其中一項為真：
* 所有應用程式實例都會遇到高 CPU/記憶體使用量，或
* 某些應用程式實例會遇到高 CPU/記憶體使用量。

若要確認這種情況，

1. 移至 [_計量_]，選取 [`Service CPU Usage Percentage`] 或 [`Service Memory Used`]。
2. 新增 `App=` 篩選器來指定您想要監視的應用程式，以及
3. 依 `Instance`分割計量。

如果所有實例都遇到高 CPU/記憶體，您必須相應放大應用程式，或相應增加 CPU/記憶體。 如需詳細資訊，請造訪[調整應用程式](spring-cloud-tutorial-scale-manual.md)

如果某些實例遇到高 CPU/記憶體，請檢查實例狀態及其探索狀態。

如需詳細資訊，請流覽[計量](spring-cloud-concept-metrics.md)。

如果所有實例都已啟動並執行，請移至_Azure Log Analytics_來查詢您的應用程式記錄檔，並檢查您的程式碼邏輯，以查看其中是否有任何專案可能會影響調整資料分割。 如需詳細資訊，請造訪[使用診斷設定來分析記錄和計量](diagnostic-services.md)。

請造訪此使用者入門[文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)，以開始使用_Azure Log Analytics_。 使用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)來查詢記錄。

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>將您的春季應用程式上架到 Azure 春季雲端之前的檢查清單

* 應用程式可以使用指定的 JAVA 執行階段版本，在本機執行。
* 環境設定（CPU/RAM/實例）符合應用程式提供者所設定的最低需求。
* 設定專案具有預期的值。 如需詳細資訊，請參閱[Config Server](spring-cloud-tutorial-config-server.md)。
* 環境變數具有預期的值。
* JVM 參數具有預期的值。
* 我們建議您從應用程式封裝中停用/移除內嵌設定_伺服器_和_春季服務_登錄服務。
* 如果要透過_服務_系結系結任何 Azure 資源，請確定目標資源已啟動且正在執行。

## <a name="configuration-and-management"></a>設定和管理

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>我在建立 Azure 春季雲端服務實例時遇到問題

當您嘗試透過入口網站布建_Azure 春季雲端_服務實例時，Azure 春季雲端會為您執行驗證。

不過，如果您嘗試透過[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)或[Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/)來布建_Azure 春季雲端_服務實例，請確認：

* 訂用帳戶為作用中。
* _Azure 春季雲端_[支援](spring-cloud-faq.md)此位置。
* 已建立實例的資源群組。
* 資源名稱符合命名規則。 （它只能包含小寫字母、數位和連字號。 第一個字元必須是字母。 最後一個字元必須是字母或數位。 值的長度必須介於2到32個字元之間）。

如果您嘗試透過 Resource Manager 範本布建_Azure 春季雲端_服務實例，請流覽 https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates 以檢查範本語法。

_Azure 春季雲端_服務實例的名稱將用來要求 `azureapps.io`之下的子功能變數名稱稱，因此，如果名稱與現有的名稱衝突，則布建將會失敗。 您可以在活動記錄中找到更多詳細資料。

### <a name="i-cannot-deploy-a-jar-package"></a>我無法部署 JAR 套件

您無法透過入口網站或 Resource Manager 範本上傳 JAR/來源套件。

當您使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署應用程式封裝時，它會定期輪詢部署進度，而在結束時，它會顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取部署記錄檔：

`az spring-cloud app show-deploy-log -n <app-name>`

請確定您的應用程式是以正確的[可執行檔 jar 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)封裝。 如果不是，您會看到類似下面的錯誤：

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>我無法部署來源套件

您無法透過入口網站或 Resource Manager 範本上傳 JAR/來源套件。

當您部署已考慮[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)的應用程式套件時，它會定期輪詢部署進度，而在結束時，它會顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取組建和部署記錄檔：

`az spring-cloud app show-deploy-log -n <app-name>`

不過，請注意，一個_Azure 春季雲端_服務實例一次只能觸發一個來源套件的一個組建工作。 如需詳細資訊，請參閱[部署應用程式](spring-cloud-quickstart-launch-app-portal.md)和[預備環境指南](spring-cloud-howto-staging-environment.md)。

### <a name="my-application-cannot-be-registered"></a>無法註冊我的應用程式

在大部分情況下，當您的 POM 檔案中未正確設定必要的相依性/服務探索時，就會發生這種情況。 設定之後，內建的服務登錄伺服器端點將會插入為您應用程式的環境變數。 然後，應用程式會向服務登錄伺服器註冊自己，並探索其他相依的微服務。

等待至少2分鐘，新註冊的實例才會開始接收流量。

如果您要將現有的春季雲端型解決方案遷移至 Azure，請確定您的臨機操作_服務_登錄和設定_伺服器_實例已移除（或已停用），以避免與_Azure 春季雲端所提供的受控實例發生衝突_.

您也可以在_Azure Log Analytics_中檢查_服務_登錄用戶端記錄。 如需詳細資訊，請造訪[使用診斷設定來分析記錄和計量](diagnostic-services.md)

請造訪此使用者入門[文章](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)，以開始使用_Azure Log Analytics_。 使用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)來查詢記錄。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要檢查應用程式的環境變數

環境變數會通知 Azure 春季雲端架構，確保 Azure 瞭解如何和如何設定組成您應用程式的服務。  確保您的環境變數是正確的，這是針對潛在問題進行疑難排解的第一個步驟。  您可以使用春季開機傳動端點來檢查您的環境變數。  

> [!WARNING]
> 此程式會使用您的測試端點來公開您的環境變數。  如果您的測試端點可公開存取，或您已將功能變數名稱指派給您的應用程式，請勿繼續。

1. 流覽至此 URL： `https://<your application test endpoint>/actuator/health`。  
    - 類似于 `{"status":"UP"}` 的回應表示已啟用端點。
    - 如果回應是負數，請在您的 `POM.xml`中加入下列相依性：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 啟用 [春季開機傳動程式] 端點後，移至 [Azure 入口網站]，然後尋找您應用程式的 [設定] 頁面。  新增名為 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 和值 `*` 的環境變數。 

1. 重新開機您的應用程式。

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

尋找名為 `systemEnvironment`的子節點。  此節點包含您應用程式的環境變數。

> [!IMPORTANT]
> 請記得先反轉環境變數的暴露，再讓公用應用程式可供存取。  移至 [Azure 入口網站]，尋找應用程式的 [設定] 頁面，並刪除此環境變數： `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`。

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>我找不到應用程式的計量或記錄

移至 [_應用程式管理_]，以確定應用程式正在執行且已_啟動_。

如果您可以看到來自_JVM_的計量，但沒有來自_Tomcat_的計量，請檢查您的應用程式封裝中是否已啟用`spring-boot-actuator` 相依性並成功啟動。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果您的應用程式記錄可以封存到儲存體帳戶，但未傳送至_Azure Log Analytics_，請檢查您是否已[正確設定您的工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果您使用_Azure Log Analytics_的免費層，請注意[免費層不提供 SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。