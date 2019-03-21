---
title: 為 Log Analytics 收集 Azure 服務的記錄和計量 | Microsoft Docs
description: 在 Azure 資源上設定診斷以便將記錄和度量寫入 Log Analytics。
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
ms.openlocfilehash: ae4e4487b3d9df4b2cf756174f0a56e721af91c4
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406895"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>收集 Azure 服務的記錄和計量以便使用於 Log Analytics

有四種不同的方式可收集 Azure 服務的記錄和度量︰

1. Azure 診斷直達 Log Analytics (下表中的「診斷」)
2. Azure 診斷至 Azure 儲存體至 Log Analytics (下表中的「儲存體」)
3. Azure 服務的連接器 (下表中的「連接器」)
4. 使用指令碼來收集，再將資料公佈至 Log Analytics (下表中的空格，適用於未列出的服務)


| 服務                 | 資源類型                           | 記錄檔        | 度量     | 解決方法 |
| --- | --- | --- | --- | --- |
| 應用程式閘道    | Microsoft.Network/applicationGateways   | 診斷 | 診斷 | [Azure 應用程式閘道分析](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application insights    |                                         | 連接器   | 連接器   | [Application Insights Connector (Application Insights 連接器)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (預覽) |
| 自動化帳戶     | Microsoft.Automation/AutomationAccounts | 診斷 |             | [詳細資訊](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch 帳戶          | Microsoft.Batch/batchAccounts           | 診斷 | 診斷 | |
| 傳統雲端服務  |                                         | 儲存體     |             | [詳細資訊](azure-storage-iis-table.md) |
| 認知服務      | Microsoft.CognitiveServices/accounts    |             | 診斷 | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | 診斷 |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | 診斷 |             | |
| 事件中樞命名空間     | Microsoft.EventHub/namespaces           | 診斷 | 診斷 | |
| IoT 中樞                | Microsoft.Devices/IotHubs               |             | 診斷 | |
| Key Vault               | Microsoft.KeyVault/vaults               | 診斷 |             | [金鑰保存庫分析](../../azure-monitor/insights/azure-key-vault.md) |
| 負載平衡器          | Microsoft.Network/loadBalancers         | 診斷 |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | 診斷 | 診斷 | |
| 網路安全性群組 | Microsoft.Network/networksecuritygroups | 診斷 |             | [Azure 網路安全性群組分析](../../azure-monitor/insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| 復原保存庫         | Microsoft.RecoveryServices/vaults       |             |             | [Azure 復原服務分析 (預覽)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| 搜尋服務         | Microsoft.Search/searchServices         | 診斷 | 診斷 | |
| 服務匯流排命名空間   | Microsoft.ServiceBus/namespaces         | 診斷 | 診斷 | [服務匯流排分析 (預覽)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | 儲存體     |             | [Service Fabric Analytics (Service Fabric 分析) (預覽)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | 診斷 | [Azure SQL Analytics (預覽)](../../azure-monitor/insights/azure-sql.md) |
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
許多 Azure 資源能夠直接將診斷記錄和度量寫入 Log Analytics，這是收集資料來分析的較佳做法。 使用 Azure 診斷時，資料會立即寫入 Log Analytics，不需要先將資料寫入儲存體。

支援 [Azure 監視器](../../azure-monitor/overview.md)的 Azure 資源可以直接將記錄和度量傳送至 Log Analytics。

> [!NOTE]
> 目前不支援透過診斷設定將多維度計量傳送至 Log Analytics。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> *例如*：可以依據每個佇列層級來瀏覽及繪製「事件中樞」上的「傳入訊息」計量。 不過，當您透過診斷設定匯出時，計量會呈現為事件中樞內所有佇列的所有內送郵件。
>
>

* 如需可用度量的詳細資訊，請參閱[支援 Azure 監視器的度量](../../azure-monitor/platform/metrics-supported.md)。
* 如需可用記錄檔的詳細資訊，請參閱[支援的服務以及診斷記錄檔的結構描述](../../azure-monitor/platform/diagnostic-logs-schema.md)。

### <a name="enable-diagnostics-with-powershell"></a>啟用 PowerShell 的診斷功能

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

您需要 2016 年 11 月 (v2.3.0) 或更新版本的 [Azure PowerShell](/powershell/azure/overview)。

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

從某些資源內收集記錄時，可以先將記錄傳送至 Azure 儲存體，再設定 Log Analytics 從儲存體讀取記錄。

對於下列資源和記錄，Log Analytics 可以使用這種方法，從 Azure 儲存體收集診斷︰

| 資源 | 記錄檔 |
| --- | --- |
| Service Fabric |ETWEvent <br> 運作事件 <br> Reliable Actor 事件 <br> Reliable Service 事件 |
| 虛擬機器 |Linux Syslog <br> Windows 事件 <br> IIS 記錄 <br> Windows ETWEvent |
| Web 角色 <br> 背景工作角色 |Linux Syslog <br> Windows 事件 <br> IIS 記錄 <br> Windows ETWEvent |

> [!NOTE]
> 當您將診斷傳送到儲存體帳戶時，或是當 Log Analytics 從您的儲存體帳戶讀取資料時，您將需要支付儲存體和交易的一般 Azure 資料費用。
>
>

若要深入了解 Log Analytics 如何收集這些記錄，請參閱[對 IIS 使用 Blob 儲存體，對事件使用表格儲存體](azure-storage-iis-table.md)。

## <a name="connectors-for-azure-services"></a>Azure 服務的連接器

Application Insights 有一個連接器，可以將 Application Insights 收集的資料傳送至 Log Analytics。

深入了解 [Application Insights 連接器](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)。

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>使用指令碼來收集，再將資料公佈至 Log Analytics

對於無法直接將記錄和度量傳送至 Log Analytics 的 Azure 服務，您可以使用 Azure 自動化指令碼來收集記錄和度量。 然後，指令碼可以使用[資料收集器 API](../../azure-monitor/platform/data-collector-api.md)，將資料傳送至 Log Analytics

Azure 範本庫包含[使用 Azure 自動化的範例](https://azure.microsoft.com/resources/templates/?term=OMS)，可從服務收集資料，再傳送至 Log Analytics。

## <a name="next-steps"></a>後續步驟

* [對 IIS 使用 Blob 儲存體，對事件使用表格儲存體](azure-storage-iis-table.md)，以讀取 Azure 服務 (將診斷寫入表格儲存體) 的記錄，或讀取寫入 Blob 儲存體的 IIS 記錄。
* [啟用解決方案](../../azure-monitor/insights/solutions.md) 以提供資料的深入見解。
* [使用搜尋查詢](../../azure-monitor/log-query/log-query-overview.md) 以分析資料。
