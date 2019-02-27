---
title: 整合多個 Azure 監視器 Application Insights 資源 | Microsoft Docs
description: 本文提供有關如何使用 Azure 監視器記錄中的函數來查詢多個 Application Insights 資源，並將該資料視覺化的詳細資訊。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429511"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>整合多個 Azure 監視器 Application Insights 資源 
本文說明如何集中一處查詢和檢視所有 Application Insights 應用程式記錄檔資料，即使它們位於不同的 Azure 訂用帳戶中，以取代淘汰的 Application Insights Connector。 您在單一查詢中可納入的資源 (Application Insights 資源) 數目上限為 100 個。  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>查詢多個 Application Insights 資源的建議方法 
在查詢中列出多個 Application Insights 資源可能很麻煩且難以維護。 相反地，您可以利用函數將查詢邏輯與應用程序範圍分開。  

此範例示範如何監視多個 Application Insights 資源，並將失敗的要求計數依應用程式名稱視覺化。 在開始之前，請在連線到 Application Insights 資源的工作區中執行下列查詢，以取得已連線應用程式的清單： 

```
ApplicationInsights
| summarize by ApplicationName
```

使用聯集運算子搭配應用程式清單來建立函式，然後使用 *applicationsScoping* 別名將查詢在您的工作區中儲存成函式。  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>您可以隨時修改所列出的應用程式，方法是在入口網站中瀏覽至您工作區中的 [查詢總管] 並選取函式來進行編輯後再儲存，或是使用 `SavedSearch` PowerShell Cmdlet。 `withsource= SourceApp` 命令在結果中新增一個資料行，可指定傳送記錄的應用程式。 
>
>該查詢使用 Application Insights 資料結構，雖然查詢是在工作區中執行的，因為 applicationsScoping 函數會傳回 Application Insights 資料結構。 
>
>在此範例中，parse 運算子是選擇性的，它會從 SourceApp 屬性中擷取應用程式名稱。 

您現在可以在跨資源查詢中使用 applicationsScoping 函數：  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

函式別名會從所有定義的應用程式傳送要求的聯集。 接著，查詢會篩選失敗的要求，並依應用程式將趨勢視覺化。

![跨查詢結果範例](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>跨 Application Insights 資源和工作區資料查詢 
當您停止連接器並需要在 Application Insights 資料保留期 (90 天) 修剪的時間範圍內執行查詢時，您需要在工作區和 Application Insights 資源上執行[跨資源查詢](../../azure-monitor/log-query/cross-workspace-query.md)一段時間。 這是在您的應用程式資料按照上述新的 Application Insights 資料保留累積之前。 由於 Application Insights 與工作區中的結構描述不同，因此查詢會需要一些操作。 請參閱本節稍後反白顯示結構描述差異的表格。 

>[!NOTE]
>新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 支援記錄警示中的[跨資源查詢](../log-query/cross-workspace-query.md)。 根據預設，Azure 監視器會使用[舊版 Log Analytics 警示 API](../platform/api-alerts.md) 從 Azure 入口網站建立新的記錄警示規則，除非您從[舊版記錄警示 API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) 切換。 切換之後，新的 API 將成為 Azure 入口網站中新警示規則的預設值，並允許您建立跨資源查詢記錄警示規則。 您可以使用[適用於 scheduledQueryRules API 的 ARM 範本](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)建立[跨資源查詢](../log-query/cross-workspace-query.md)記錄警示規則而無需進行切換 – 但此警示規則可透過 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 進行管理，而不是 Azure 入口網站。

例如，如果連接器在 2018-11-01 停止工作，則當您在工作區中跨 Application Insights 資源及應用程式資料查詢記錄時，您的查詢會如下列範例所示建構：

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights 和 Log Analytics 工作區結構描述差異
下表顯示 Log Analytics 和 Application Insights 之間的結構描述差異。  

| Log Analytics 工作區屬性| Application Insights 資源屬性|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | Message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| [瀏覽器] | client_browser |
| City | client_city |
| ClientIP | client_IP |
| 電腦 | cloud_RoleInstance | 
| 國家 (地區) | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | Message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| 作業系統 | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | 成功 | 
| ResponseCode | ResultCode | 
| 角色 | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>後續步驟

使用[記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)來檢視 Application Insights 應用程式的詳細資訊。
