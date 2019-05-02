---
title: 藉由更新您的 logic apps 和 runbook 來準備 Azure 監視器傳統警示移轉
description: 了解如何修改您的 webhook，logic apps 和自願性移轉準備工作的 runbook。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 347c89991cbb4d28b46eafff0a783148793ad2f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727495"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>適用於傳統的警示規則的移轉準備您的 logic apps 和 runbook

作為[先前所宣布](monitoring-classic-retirement.md)，在 Azure 監視器傳統警示會在 2019 年 7 月即將淘汰。 客戶使用傳統的警示規則的人員，而且想要自行觸發移轉至 Azure 入口網站中使用移轉工具。

如果您選擇主動將傳統的警示規則移轉至新的警示規則，請注意，有兩個系統之間的一些差異。 這篇文章會說明這些差異，以及如何準備變更。

## <a name="api-changes"></a>API 變更

建立及管理傳統的警示規則的 Api (`microsoft.insights/alertrules`) 不同的 Api，建立和管理新的計量警示 (`microsoft.insights/metricalerts`)。 如果您以程式設計方式建立，並立即管理傳統的警示規則，請更新您的部署指令碼，來使用新的 Api。

下表是針對傳統和新警示之程式設計介面的參考：

|         |傳統警示  |新的計量警示 |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [參考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure Resource Manager 範本 | [傳統警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[適用於新的計量警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知承載變更

通知承載格式是之間稍有不同[傳統警示規則](alerts-webhooks.md)並[新的計量警示](alerts-metric-near-real-time.md#payload-schema)。 如果您有任何 webhook、 邏輯應用程式或由傳統的警示規則所觸發的 runbook 動作，您必須更新這些通知端點，以接受新的計量警示承載格式。

您可以使用下表，webhook 承載中的欄位對應的傳統的格式為新的格式：

|  |傳統警示  |新的計量警示 |
|---------|---------|---------|
|啟動或已解決的警示？    | **status**       | **data.status** |
|警示的內容資訊     | **context**        | **data.context**        |
|在警示已啟用，或已解決的時間戳記     | **context.timestamp**       | **data.context.timestamp**        |
| 警示規則識別碼 | **context.id** | **data.context.id** |
| 警示規則名稱 | **context.name** | **data.context.name** |
| 警示規則的描述 | **context.description** | **data.context.description** |
| 警示規則條件 | **context.condition** | **data.context.condition** |
| 度量名稱 | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| 時間彙總 （如何度量會彙總評估視窗）| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| 評估期 | **context.condition.windowSize** | **data.context.condition.windowSize** |
| 運算子 （如何彙總的度量值比較根據臨界值） | **context.condition.operator** | **data.context.condition.operator** |
| 閾值 | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| 度量值 | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| 订阅 ID | **context.subscriptionId** | **data.context.subscriptionId** |
| 受影響資源的資源群組 | **context.resourceGroup** | **data.context.resourceGroup** |
| 受影響資源的名稱 | **context.resourceName** | **data.context.resourceName** |
| 受影響的資源類型 | **context.resourceType** | **data.context.resourceType** |
| 受影響資源的資源識別碼 | **context.resourceId** | **data.context.resourceId** |
| 入口網站資源摘要頁面的直接連結 | **context.portalLink** | **data.context.portalLink** |
| 要傳遞至 webhook 或邏輯應用程式的自訂承載欄位 | **properties** | **data.properties** |

如您所見，很類似，承載。 下一節提供：

- 修改邏輯應用程式，以使用新格式的詳細資料。
- 剖析新警示的通知承載的 runbook 範例。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改邏輯應用程式收到計量的警示通知

如果您使用 logic apps 與傳統的警示，您必須修改您的邏輯應用程式程式碼剖析的新計量警示承載。 請遵循下列步驟：

1. 建立新的邏輯應用程式。

1. 使用 「 Azure 監視器-計量警示處理常式 」 的範本。 此範本有**HTTP 要求**具有適當的結構描述定義觸發程序。

    ![邏輯應用程式範本](media/alerts-migration/logic-app-template.png "計量的警示範本")

1. 新增動作，來裝載您的處理邏輯。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用自動化 runbook 會接收計量的警示通知

下列範例會提供在您的 runbook 中使用的 PowerShell 程式碼。 此程式碼可以剖析傳統計量警示規則和新的計量警示規則的裝載。

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

停止虛擬機器時，會觸發警示的 runbook 的完整範例，請參閱 < [Azure 自動化文件](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)。

## <a name="partner-integration-via-webhooks"></a>夥伴整合，透過 webhook

大部分[合作夥伴與傳統警示整合](https://docs.microsoft.com/azure/azure-monitor/platform/partners)已經支援透過其整合的新版計量警示。 已經在新的計量警示使用的已知的整合如下：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果您在此使用未列出的夥伴整合，確認與整合提供者可與新的計量警示的整合。

## <a name="next-steps"></a>後續步驟

- [如何使用移轉工具](alerts-using-migration-tool.md)
- [了解移轉工具的運作方式](alerts-understand-migration.md)
