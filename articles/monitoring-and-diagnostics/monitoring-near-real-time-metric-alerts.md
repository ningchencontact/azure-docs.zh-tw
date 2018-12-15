---
title: Azure 監視器中計量警示所支援的資源
description: Azure 監視器中計量警示所支援計量與記錄的相關參考
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: d5802ed71c187979fb91bcd4ea532891d343f14a
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435696"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure 監視器中計量警示所支援的資源

Azure 監視器現已可支援[新的計量警示類型](../azure-monitor/platform/alerts-overview.md)，此類型具有遠優於[傳統計量警示](../azure-monitor/platform/alerts-classic.overview.md)的優點。 計量適用於 [Azure 服務的大型清單](../azure-monitor/platform/metrics-supported.md)。 新版警示則可支援該資源類型 (不斷增長) 的子集。 本文將列出該子集。


您也可以在擷取來作為計量的常用 Log Analytics 記錄上使用新版計量警示。 如需詳細資訊，請檢視[記錄的計量警示](../azure-monitor/platform/alerts-metric-logs.md)。

## <a name="portal-powershell-cli-rest-support"></a>入口網站、PowerShell、CLI、REST 支援
目前，您只能在 Azure 入口網站、[REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/) 或 [Resource Manager 範本](../azure-monitor/platform/alerts-metric-create-templates.md)中建立新版計量警示。 即將支援使用 PowerShell 和 Azure CLI 2.0 版與更新版本來設定新版警示。

## <a name="metrics-and-dimensions-supported"></a>支援的計量和維度
新版計量警示支援針對使用維度的計量發出警示。 您可以使用維度來將計量篩選到正確層級。 從 [Azure 監視器 - 計量瀏覽器](../azure-monitor/platform/metrics-charts.md)，即可探索並以視覺化方式檢視所有支援的計量及適用的維度。

以下是新版警示所支援的 Azure 監視器計量來源完整清單：

|資源類型  |支援的維度  | 可用的計量|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | 是        | [API 管理](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     是   | [自動化帳戶](../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| [Batch 帳戶](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/A     |[Azure Cache for Redis](../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/A     | [認知服務](../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/A     | [虛擬機器](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[虛擬機器擴展集](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | 是| [容器群組](../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | 是 | [受控叢集](../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| 是| [資料處理站 V1](../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   是     |[資料處理站 (V2)](../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/A      |[適用於 MySQL 的 DB](../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/A     | [適用於 PostgreSQL 的 DB](../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  是      |[事件中樞](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| 否 | [保存庫](../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     N/A    |[Logic Apps](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/A     | [應用程式閘道](../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | N/A |  [快速路由線路](../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | N/A| [DNS 區域](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (僅適用於標準 SKU)| 是| [負載平衡器](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/A       |[公用 IP 位址](../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | N/A | [容量](../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | 是 | [流量管理員設定檔](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   N/A      |[搜尋服務](../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  是       |[服務匯流排](../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    是     | [儲存體帳戶](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     是    | [Blob 服務](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)、[檔案服務](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices)、[佇列服務](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)及[資料表服務](../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       | [串流分析](../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | 是 | [App Service 方案](../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | 是 | [App Service](../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) 與[函式](../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | 是 | [App Service 位置](../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| 是|[Log Analytics 工作區](../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>承載結構描述

當使用已適當設定的[動作群組](../azure-monitor/platform/action-groups.md)時，POST 作業會針對所有新版計量警示，包含下列 JSON 承載和結構描述：

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

* 深入了解新的[警示體驗](../azure-monitor/platform/alerts-overview.md)。
* 了解 [Azure 中的記錄警示](../azure-monitor/platform/alerts-unified-log.md)。
* 深入了解 [Azure 中的警示](../azure-monitor/platform/alerts-overview.md)。
