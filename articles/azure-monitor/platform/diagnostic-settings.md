---
title: 建立診斷設定以收集 Azure 中的記錄和計量 |Microsoft Docs
description: 建立診斷設定，以將 Azure 平臺記錄轉送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ec1842d534dcb1e9ddef149d3ae879677b29e715
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262514"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>建立診斷設定以收集 Azure 中的平臺記錄和計量
Azure 中的[平臺記錄](resource-logs-overview.md)會針對 azure 資源和它們所依賴的 azure 平臺，提供詳細的診斷和審核資訊。 本文提供有關建立及設定診斷設定的詳細資料，以將平臺記錄檔收集到不同的目的地。

每個 Azure 資源都需要自己的診斷設定。 診斷設定會針對該資源定義下列各項：

- 記錄檔和計量資料的分類會傳送至設定中定義的目的地。 可用的類別會因不同的資源類型而異。
- 一或多個要傳送記錄的目的地。 目前的目的地包括 Log Analytics 工作區、事件中樞和 Azure 儲存體。
- 儲存在 Azure 儲存體中的資料保留原則。
 
單一診斷設定可以定義每個目的地的其中一個。 如果您想要將資料傳送到一個以上的特定目的地類型（例如，兩個不同的 Log Analytics 工作區），請建立多個設定。 每個資源最多可以有5個診斷設定。

> [!NOTE]
> 活動記錄可以轉送到與其他平臺記錄相同的目的地，但不會使用診斷設定進行設定。 如需詳細資訊，請參閱[Azure 中的平臺記錄總覽](platform-logs-overview.md#destinations)。

> [!NOTE]
> 系統會自動收集[平臺計量](metrics-supported.md)以[Azure 監視器計量](data-platform-metrics.md)。 診斷設定可以用來將特定 Azure 服務的計量收集到 Azure 監視器記錄中，以便使用[記錄查詢](../log-query/log-query-overview.md)進行其他監視資料的分析。

## <a name="destinations"></a>Destinations 
平臺記錄可以傳送至下表中的目的地。 每個目的地的設定都是使用相同的程式來建立，這是本文所述的診斷設定。 請遵循下表中的每個連結，以取得將資料傳送至該目的地的詳細資訊。

| Destination | 描述 |
|:---|:---|
| [Log Analytics 工作區](resource-logs-collect-workspace.md) | 將記錄收集到 Log Analytics 工作區，可讓您使用功能強大的記錄查詢 Azure 監視器所收集的其他監視資料進行分析，也可以利用警示和視覺效果等其他 Azure 監視器功能。 |
| [事件中樞](resource-logs-stream-event-hubs.md) | 將記錄傳送至事件中樞可讓您將資料串流至外部系統，例如協力廠商 Siem 和其他 log analytics 解決方案。 |
| [Azure 儲存體帳戶](resource-logs-collect-storage.md) | 將記錄封存到 Azure 儲存體帳戶適用于 audit、靜態分析或備份。 |




## <a name="create-diagnostic-settings-in-azure-portal"></a>在 Azure 入口網站中建立診斷設定
您可以從 [Azure 監視器] 功能表或從資源的功能表，設定 Azure 入口網站中的診斷設定。

1. 從 Azure 入口網站的 [Azure 監視器] 功能表中，按一下 [**設定**] 底下的 [**診斷設定**]，然後按一下資源。

    ![診斷設定](media/diagnostic-settings/menu-monitor.png)

    或者，在 Azure 入口網站的 [資源] 功能表中，按一下 [**監視**] 底下的 [**診斷設定**]。

    ![診斷設定](media/diagnostic-settings/menu-resource.png)

2. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下 [開啟診斷]。

   ![新增診斷設定 - 無現有的設定](media/diagnostic-settings/add-setting.png)

   如果資源上有現有的設定，您會看到已設定的設定清單。 按一下 [新增**診斷設定**] 以加入新的設定或**編輯設定**，以編輯現有的設定。 每個設定都不能有一個以上的目的地類型。

   ![新增診斷設定 - 現有的設定](media/diagnostic-settings/edit-setting.png)

3. 如果您的設定還沒有名稱，請將其命名為。
4. 勾選每個目的地的方塊以傳送記錄。 按一下 [**設定**] 以指定其設定，如下表所述。

    | 設定 | 描述 |
    |:---|:---|
    | Log Analytics 工作區 | 工作區的名稱。 |
    | 儲存體帳戶 | 儲存體帳戶的名稱。 |
    | 事件中樞命名空間 | 建立事件中樞的命名空間（如果這是您第一次串流記錄），或串流處理至（如果已經有資源正在將該記錄類別串流至這個命名空間）。
    | 事件中樞名稱 | （選擇性）指定要在設定中傳送所有資料的事件中樞名稱。 如果您未指定名稱，則會為每個記錄類別建立一個事件中樞。 如果您要傳送多個類別，您可能會想要指定名稱，以限制所建立的事件中樞數目。 如需詳細資訊，請參閱[Azure 事件中樞配額和限制](../../event-hubs/event-hubs-quotas.md)。 |
    | 事件中樞原則名稱 | 定義資料流程機制所擁有的許可權。 |

    ![新增診斷設定 - 現有的設定](media/diagnostic-settings/setting-details.png)

5. 勾選每個資料類別的方塊，以傳送到指定的目的地。 如果您已選取要封存**至儲存體帳戶**的選項，則您也必須指定[保留期限](resource-logs-collect-storage.md#data-retention)。



> [!NOTE]
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> *例如*：可以依據每個佇列層級來瀏覽及繪製「事件中樞」上的「傳入訊息」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。

4. 按一下 [儲存]。

幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且記錄會在產生新的事件資料時串流處理至指定的目的地。 請注意，事件發出的時間和[記錄分析工作區中出現](data-ingestion-time.md)的時間，最多可能會有15分鐘的時間。



## <a name="create-diagnostic-settings-using-powershell"></a>使用 PowerShell 建立診斷設定
使用[set-azdiagnosticsetting 指令程式](https://docs.microsoft.com/en-us/powershell/module/az.monitor/set-azdiagnosticsetting)建立具有[Azure PowerShell](powershell-quickstart-samples.md)的診斷設定。 如需其參數的描述，請參閱此 Cmdlet 的檔。

以下是使用這三個目的地來建立診斷設定的範例 PowerShell Cmdlet。


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>使用 Azure CLI 建立診斷設定
使用[az monitor [診斷-設定](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create)] [建立] 命令，以[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)建立診斷設定。 如需其參數的描述，請參閱此命令的檔。

以下是使用這三個目的地來建立診斷設定的範例 CLI 命令。



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>使用 REST API 設定診斷設定
請參閱[診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)，以使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)來建立或更新診斷設定。


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>使用 Resource Manager 範本設定診斷設定
請參閱[使用 Resource Manager 範本，在建立資源時自動啟用診斷設定](diagnostic-settings-template.md)，以使用 Resource Manager 範本來建立或更新診斷設定。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure 平臺記錄](resource-logs-overview.md)