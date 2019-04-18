---
title: Azure 服務的記錄和計量收集到 Log Analytics 工作區 |Microsoft Docs
description: 將記錄檔和度量寫入 Azure 監視器中的 Log Analytics 工作區的 Azure 資源上設定診斷。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006274"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>收集到 Log Analytics 工作區，在 Azure 監視器中的 Azure 服務的記錄和計量

有四種不同的方式可收集 Azure 服務的記錄和度量︰

1. Azure 診斷直達 Log Analytics 工作區，在 Azure 監視器中 (*診斷*下表中)
2. Azure 監視器中的 Log Analytics 工作區的 Azure 儲存體的 azure 診斷 (*儲存體*下表中)
3. Azure 服務的連接器 (下表中的「連接器」)
4. 使用指令碼來收集並再將資料公佈至 Log Analytics 工作區，在 Azure 監視器 （空格下表中和未列出的服務）


| 服務                 | 資源類型                           | 記錄        | 度量     | 解決方法 |
| --- | --- | --- | --- | --- |
| 應用程式閘道    | Microsoft.Network/applicationGateways   | 診斷 | 診斷 | [Azure 應用程式閘道分析](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application insights    |                                         | 連接器   | 連接器   | [Application Insights Connector (Application Insights 連接器)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (預覽) |
| 自動化帳戶     | Microsoft.Automation/AutomationAccounts | 診斷 |             | [詳細資訊](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch 帳戶          | Microsoft.Batch/batchAccounts           | 診斷 | 診斷 | |
| 傳統雲端服務  |                                         | 儲存體     |             | [詳細資訊](azure-storage-iis-table.md) |
| 認知服務      | Microsoft.CognitiveServices/accounts    |             | 診斷 | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | 診斷 |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | 診斷 |             | |
| 事件中樞命名空間     | Microsoft.EventHub/namespaces           | 診斷 | 診斷 | |
| IoT 中樞                | Microsoft.Devices/IotHubs               |             | 診斷 | |
| Key Vault               | Microsoft.KeyVault/vaults               | 診斷 |             | [金鑰保存庫分析](../insights/azure-key-vault.md) |
| 負載平衡器          | Microsoft.Network/loadBalancers         | 診斷 |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | 診斷 | 診斷 | |
| 網路安全性群組 | Microsoft.Network/networksecuritygroups | 診斷 |             | [Azure 網路安全性群組分析](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| 復原保存庫         | Microsoft.RecoveryServices/vaults       |             |             | [Azure 復原服務分析 (預覽)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| 搜尋服務         | Microsoft.Search/searchServices         | 診斷 | 診斷 | |
| 服務匯流排命名空間   | Microsoft.ServiceBus/namespaces         | 診斷 | 診斷 | [服務匯流排分析 (預覽)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | 儲存體     |             | [Service Fabric Analytics (Service Fabric 分析) (預覽)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | 診斷 | [Azure SQL Analytics (預覽)](../insights/azure-sql.md) |
| 儲存體                 |                                         |             | 指令碼      | [Azure 儲存體分析 (預覽)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| 虛擬機器        | Microsoft.Compute/virtualMachines       | 分機   | 分機 <br> 診斷  | |
| 虛擬機器擴展集 | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | 診斷 | |
| Web 伺服器陣列        | Microsoft.Web/serverfarms               |             | 診斷 | |
| 網站               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | 診斷 | [Azure Web Apps 分析 (預覽)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> 若要監視 Azure 虛擬機器 (Linux 和 Windows)，我們建議您安裝 [Log Analytics VM 擴充](../../azure-monitor/learn/quick-collect-azurevm.md)。 代理程式將虛擬機器內收集到的深入資訊提供給您。 您也可以使用虛擬機器擴展集的擴充。
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure 診斷直達 Log Analytics
許多 Azure 資源診斷記錄和計量直接寫入至 Azure 監視器中的 Log Analytics 工作區，而且這是收集資料以供分析的慣用的方法。 當使用 Azure 診斷，資料會寫入立即到工作區，並不需要先將資料寫入儲存體。

支援的 azure 資源[Azure 監視器](../../azure-monitor/overview.md)可以記錄和度量直接傳送至 Log Analytics 工作區。

> [!NOTE]
> 目前不支援將多維度計量傳送至 Log Analytics 工作區透過診斷設定。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> *例如*：可以依據每個佇列層級來瀏覽及繪製「事件中樞」上的「傳入訊息」計量。 不過，當您透過診斷設定匯出時，計量會呈現為事件中樞內所有佇列的所有內送郵件。
>
>

* 如需可用度量的詳細資訊，請參閱[支援 Azure 監視器的度量](../../azure-monitor/platform/metrics-supported.md)。
* 如需可用記錄的詳細資訊，請參閱[支援的服務以及診斷記錄的結構描述](../../azure-monitor/platform/diagnostic-logs-schema.md)。

### <a name="enable-diagnostics-with-powershell"></a>啟用 PowerShell 的診斷功能

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下列 PowerShell 範例示範如何使用[組 AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting)上的網路安全性群組啟用診斷。 同樣的方法適用於所有支援的資源 - 只要將 `$resourceId` 設定為您想要啟用診斷之資源的資源識別碼即可。

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>使用 Resource Manager 範本啟用診斷

若要在資源建立時啟用診斷，並將診斷傳送至 Log Analytics 工作區，您可以使用類似如下的範本。 這個範例是針對自動化帳戶，但也適用於所有支援的資源類型。

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure 診斷至儲存體，再至 Log Analytics

收集從某些資源內的記錄檔，就可以將記錄傳送至 Azure 儲存體，然後設定 Log Analytics 工作區，從儲存體讀取記錄檔。

Azure 監視器可以使用這種方法來收集從下列資源和記錄檔的 Azure 儲存體的診斷資訊：

| 資源 | 記錄 |
| --- | --- |
| Service Fabric |ETWEvent <br> 運作事件 <br> Reliable Actor 事件 <br> Reliable Service 事件 |
| 虛擬機器 |Linux Syslog <br> Windows 事件 <br> IIS 記錄 <br> Windows ETWEvent |
| Web 角色 <br> 背景工作角色 |Linux Syslog <br> Windows 事件 <br> IIS 記錄 <br> Windows ETWEvent |

> [!NOTE]
> 當您將診斷傳送至儲存體帳戶和 Log Analytics 工作區時從儲存體帳戶讀取資料時，則要收費儲存體和交易的一般 Azure 資料的費用。
>
>

請參閱[事件的 IIS 和資料表儲存體使用 blob 儲存體](azure-storage-iis-table.md)若要深入了解 Azure 監視器可以收集這些記錄檔的方式。

## <a name="connectors-for-azure-services"></a>Azure 服務的連接器

沒有 Application insights，允許傳送到 Log Analytics 工作區的 Application Insights 所收集的資料連接器。

深入了解 [Application Insights 連接器](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)。

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>使用指令碼來收集，並將資料公佈至 Log Analytics 工作區

如不提供直接的方式，來將記錄和計量傳送至 Log Analytics 工作區的 Azure 服務中，您可以使用 Azure 自動化指令碼來收集記錄和度量。 指令碼就可以傳送資料至工作區使用[資料收集器 API](../../azure-monitor/platform/data-collector-api.md)

具有 Azure 範本庫[使用 Azure 自動化的範例](https://azure.microsoft.com/resources/templates/?term=OMS)從服務收集資料，並將它傳送至 Azure 監視器。

## <a name="next-steps"></a>後續步驟

* [對 IIS 使用 Blob 儲存體，對事件使用表格儲存體](azure-storage-iis-table.md)，以讀取 Azure 服務 (將診斷寫入表格儲存體) 的記錄，或讀取寫入 Blob 儲存體的 IIS 記錄。
* [啟用解決方案](../../azure-monitor/insights/solutions.md) 以提供資料的深入見解。
* [使用搜尋查詢](../../azure-monitor/log-query/log-query-overview.md) 以分析資料。
