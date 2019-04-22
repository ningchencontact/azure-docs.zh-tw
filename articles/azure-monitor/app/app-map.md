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
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 89aa5006882680205816e7e5d1e7e55b9c4b2ab0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678528"
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

## <a name="set-cloud-role-name"></a>設定雲端角色名稱

使用應用程式對應**雲端角色名稱**屬性來識別在地圖上的元件。 Application Insights SDK 會自動新增至遙測元件發出的雲端角色名稱屬性。 比方說，SDK 會將網站名稱或服務的角色名稱加入雲端角色名稱屬性。 但是，有時候您可能會想覆寫預設值。 若要覆寫雲端角色名稱，並變更應用程式對應上顯示的內容取得：

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

Spring Boot starter，您輸入 spring.application.name 屬性的值，將會自動指派雲端角色名稱。

如需 Java 的進一步資訊相互關聯，以及如何設定雲端角色名稱非 SpringBoot 應用程式簽出這[一節](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name)上相互關聯。

### <a name="clientbrowser-side-javascript"></a>用戶端/瀏覽器端 JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>了解雲端應用程式對應的內容中的角色名稱

如何看待而言**雲端角色名稱**，很有幫助，若要查看有多個雲端角色名稱存在應用程式對應：

![應用程式對應螢幕擷取畫面](media/app-map/cloud-rolename.png)

在上方的綠色方塊中的名稱的每個應用程式對應是雲端角色名稱值，這個特定的分散式應用程式的不同層面。 讓此應用程式及其角色所組成： `Authentication`， `acmefrontend`， `Inventory Management`、 `Payment Processing Worker Role`。 

在使用此應用程式的情況下，為每個這些雲端角色名稱也代表不同的獨特的 Application Insights 資源與他們自己的檢測金鑰。 因為此應用程式的擁有者可以存取每個這些四個不同的 Application Insights 資源，應用程式對應是能夠將拼接在一起的基礎關聯性的對應。

針對[正式定義](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

或者，**雲端角色執行個體**很有幫助案例所在**雲端角色名稱**告訴您，問題就在您的 web 前端，但您可能會執行您的 web 前端之間多個負載平衡伺服器因此能夠向下切入 Kusto 查詢透過更深入一層，以及如何了解，是否此問題會影響所有 web 前端伺服器/執行個體，或其中一個是非常重要。

如果您的應用程式執行容器化的環境中，只了解個別的伺服器可能不足夠的資訊來找出特定的問題時，可能的案例，其中您可能會想要覆寫雲端角色執行個體的值。

如需如何使用遙測初始設定式來覆寫雲端角色名稱屬性的詳細資訊，請參閱[新增屬性：ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer)。

## <a name="troubleshooting"></a>疑難排解

如果您無法讓應用程式對應如預期般運作，請嘗試以下步驟：

### <a name="general"></a>一般

1. 請確定您使用的是正式支援的 SDK。 不支援的 SDK 或社群 SDK 可能不支援相互關聯。

    支援的 SDK 清單請參閱[此文章](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

2. 請將所有元件都升級為最新的 SDK 版本。

3. 如果您是將 C# 搭配 Azure Functions 使用，請升級至 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)。

4. 確認[雲端角色名稱](#set-cloud-role-name)已正確設定。

5. 如果缺少相依性，請確定相依性是否列在這份[自動收集的相依性](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) (英文) 清單中。 如果不在此清單中，仍可以使用[追蹤相依性呼叫](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency) (英文)，以手動方式進行追蹤。

### <a name="too-many-nodes-on-the-map"></a>在地圖上有太多的節點

應用程式對應建構每個獨特的雲端角色名稱出現在您的要求遙測的應用程式節點，而每個唯一組合的型別、 目標和相依性遙測中的雲端角色名稱的相依性節點。 如果您的遙測資料中有 10,000 名以上的節點，應用程式對應會無法擷取所有的節點和連結，讓您的對應將會不完整。 如果發生這種情況，檢視對應時，會出現一則警告訊息。

此外，應用程式對應只會支援多達 1000 個不同的已取消群組的節點，呈現一次。 應用程式對應可降低 visual 組成群組的相依性的複雜性，具有相同的類型和呼叫端，但如果您的遙測都有獨特的雲端角色名稱太多或太多的相依性類型，該群組將會不足，並對應將無法轉譯。

若要修正此問題，您將需要變更您的檢測設定正確的雲端角色名稱、 相依性類型及相依性目標欄位。

* 相依性目標應該代表相依性的邏輯名稱。 在許多情況下，它就相當於伺服器或相依性的資源名稱項目。 比方說，在 HTTP 相依性的情況下，它會設定主機名稱。 它不應包含唯一識別碼或變更兩個不同要求之間的參數。

* 相依性類型應該代表相依性的邏輯類型。 例如，HTTP、 SQL 或 Azure Blob 是一般的相依性類型。 它不應包含唯一識別碼。

* 雲端角色名稱的目的詳述[ 區段上方](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)。

## <a name="portal-feedback"></a>入口網站意見反應

若要提供意見反應，請使用意見反應選項。

![MapLink-1 影像](./media/app-map/14-updated.png)

## <a name="next-steps"></a>後續步驟

* [了解相互關聯](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)