---
title: Azure Application Insights 的應用程式對應 | Microsoft Docs
description: 使用應用程式對應監視複雜的應用程式拓撲
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 11f7bb69ed408adf87d62a4af1aa4bd87e70bd6d
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009190"
---
# <a name="application-map-triage-distributed-applications"></a>應用程式對應：將分散式應用程式分級

應用程式對應可協助您找出分散式應用程式所有元件的效能瓶頸或失敗熱點。 對應上的每個節點各代表應用程式元件或其相依性；並具有健康情況 KPI 和警示狀態。 您可以從任何元件逐一點選至更詳細的診斷，例如 Application Insights 事件。 如果您的應用程式使用 Azure 服務，您也可以逐一點選 Azure 診斷，例如 SQL 資料庫建議程式的建議。

## <a name="what-is-a-component"></a>什麼是元件？

元件是分散式/微服務應用程式中可獨立部署的組件。 開發人員和作業小組能在程式碼層級檢視或存取這些應用程式元件所產生的遙測資料。 

* 元件不同於 SQL、EventHub 等小組/組織可能無法存取的「可觀察」外部相依性 (程式碼或遙測資料)。
* 元件能在任何數量的伺服器/角色/容器執行個體上執行。
* 元件可以是不同的 Application Insights 檢測金鑰 (即使訂用帳戶不同)，也可以是回報給單一 Application Insights 檢測金鑰的不同角色。 預覽對應體驗會顯示元件 (不論元件的設定方式為何)。

## <a name="composite-application-map"></a>複合應用程式對應

您可以查看跨相關應用程式元件多個層級的完整應用程式拓撲。 元件可以是不同的 Application Insights 資源，或是單一資源中的不同角色。 應用程式對應會尋找元件，方法是遵循已安裝 Application Insights SDK 之伺服器之間所發出的 HTTP 相依性呼叫。 

這項體驗一開始會漸進地探索元件。 當您第一次載入應用程式對應時，被觸發一組查詢，以探索與此元件相關的元件。 在探索到應用程式中的元件時，左上角的按鈕會依探索到的元件數目進行更新。 

當您按一下 [更新對應元件] 時，系統便會使用目前為止所探索的所有元件來重新整理對應。 視應用程式的複雜度，可能需要數分鐘的時間載入。

如果所有元件都是單一 Application Insights 資源內的角色，則不需要進行此探索步驟。 這類應用程式一開始會載入所有元件。

![應用程式對應螢幕擷取畫面](media/app-map/app-map-001.png)

這項新體驗的重要目標之一，是要能夠以視覺化方式顯示含有數百個元件的複雜拓撲。

按一下任何元件，即可查看相關深入資訊，並前往該元件的效能和失敗分級體驗。

![飛出視窗](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>調查失敗

選取 [調查失敗] 以啟動 [失敗] 窗格。

![調查失敗按鈕的螢幕擷取畫面](media/app-map/investigate-failures.png)

![失敗體驗的螢幕擷取畫面](media/app-map/failures.png)

### <a name="investigate-performance"></a>調查效能

若要疑難排解效能問題，請選取 [調查效能]。

![調查效能按鈕的螢幕擷取畫面](media/app-map/investigate-performance.png)

![效能體驗的螢幕擷取畫面](media/app-map/performance.png)

### <a name="go-to-details"></a>前往詳細資料

選取 [移至詳細資料] 以探索端對端交易體驗，其可提供對呼叫堆疊層級進行的檢視。

![移至詳細資料按鈕的螢幕擷取畫面](media/app-map/go-to-details.png)

![端對端交易詳細資料的螢幕擷取畫面](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>在 Analytics 中檢視

若要進一步查詢及調查應用程式資料，請按一下 [在 Analytics 中檢視]。

![在 Analytics 中檢視按鈕的螢幕擷取畫面](media/app-map/view-in-analytics.png)

![Analytics 體驗的螢幕擷取畫面](media/app-map/analytics.png)

### <a name="alerts"></a>警示

若要檢視作用中警示和導致警示觸發的基礎規則，請選取 [警示]。

![警示按鈕的螢幕擷取畫面](media/app-map/alerts.png)

![Analytics 體驗的螢幕擷取畫面](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>設定 cloud_RoleName

應用程式對應會使用 `cloud_RoleName` 屬性以識別地圖上的元件。 Application Insights SDK 會自動將 `cloud_RoleName` 屬性新增由元件發出的遙測。 例如，SDK 會將網站名稱或服務角色名稱新增至 `cloud_RoleName` 屬性。 但是，有時候您可能會想覆寫預設值。 覆寫 cloud_RoleName 和變更在應用程式對應上顯示的內容：

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**載入您的初始設定式**

在 ApplicationInsights.config 中：

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

替代方法則是在程式碼 (如 Global.aspx.cs) 中具現化初始設定式：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js 的替代方法

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

如果您是使用 Spring Boot 搭配 Application Insights Spring Boot 入門版，只需設定您在 application.properties 檔案中應用程式的自訂名稱。

`spring.application.name=<name-of-app>`

Spring Boot 入門版會自動將 cloudRoleName 指派給您給 spring.application.name 屬性輸入的值。

如需有關 JAVA 相互關聯的更多資訊，並進一步了解如何設定非 SpringBoot 應用程式的 cloudRoleName，請參閱[此節](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name)內相互關聯的相關資訊。

### <a name="clientbrowser-side-javascript"></a>用戶端/瀏覽器端 JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloudrolename-within-the-context-of-the-application-map"></a>了解 Cloud.RoleName 內容中的應用程式對應

遠如何思考 Cloud.RoleName 很有幫助查看應用程式對應具有多個 Cloud.RoleNames 存在：

![應用程式對應螢幕擷取畫面](media/app-map/cloud-rolename.png)

在上方的綠色方塊中的名稱的每個應用程式對應是 Cloud.RoleName/role 值，這個特定的分散式應用程式的不同層面。 讓此應用程式及其角色所組成： `Authentication`， `acmefrontend`， `Inventory Management`、 `Payment Processing Worker Role`。 

在此應用程式每個這些情況下`Cloud.RoleNames`也代表不同的獨特的 Application Insights 資源，與他們自己的檢測金鑰。 因為此應用程式的擁有者可以存取每個這些四個不同的 Application Insights 資源，應用程式對應是能夠將拼接在一起的基礎關聯性的對應。

針對[正式定義](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

或者，Cloud.RoleInstance 可以是其中 Cloud.RoleName 會告訴您問題就在您的 web 前端，但您可能會執行您 web 前端跨越多個負載平衡的伺服器，能夠向下鑽研更深入一層的情況下很有幫助透過 Kusto 查詢和了解問題影響所有 web 前端伺服器/執行個體，或僅只一個可以是非常重要。

您可能想要針對 Cloud.RoleInstance 覆寫值的案例是如果您的應用程式執行容器化的環境中，只了解個別的伺服器可能不足夠的資訊來找出特定的問題。

如需如何使用遙測初始設定式覆寫 cloud_RoleName 屬性的詳細資訊，請參閱[新增屬性：ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer)。

## <a name="troubleshooting"></a>疑難排解

如果您無法讓應用程式對應如預期般運作，請嘗試以下步驟：

1. 請確定您使用的是正式支援的 SDK。 不支援的 SDK 或社群 SDK 可能不支援相互關聯。

    支援的 SDK 清單請參閱[此文章](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

2. 請將所有元件都升級為最新的 SDK 版本。

3. 如果您是將 C# 搭配 Azure Functions 使用，請升級至 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)。

4. 請確認 [cloud_RoleName](#set-cloud_rolename) 設定正確無誤。

5. 如果缺少相依性，請確定相依性是否列在這份[自動收集的相依性](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) (英文) 清單中。 如果不在此清單中，仍可以使用[追蹤相依性呼叫](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency) (英文)，以手動方式進行追蹤。

## <a name="portal-feedback"></a>入口網站意見反應

若要提供意見反應，請使用意見反應選項。

![MapLink-1 影像](./media/app-map/14-updated.png)

## <a name="next-steps"></a>後續步驟

* [了解相互關聯](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)