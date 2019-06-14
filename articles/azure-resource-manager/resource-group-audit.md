---
title: 檢視 Azure 活動記錄以監視資源 | Microsoft Docs
description: 使用活動記錄檢閱使用者動作和錯誤。 顯示 Azure 入口網站 PowerShell、 Azure CLI 和 REST。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 7ff45be4eea5c6e8ab83093847164ede0e94579a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606639"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>檢視活動記錄以監視對資源的動作

透過活動記錄，您可以判斷︰

* 訂用帳戶的資源在進行哪些作業
* 啟動作業的人員
* 作業進行的時間
* 作業的狀態
* 其他可能協助您研究作業的屬性值

活動記錄檔包含所有寫入作業 (PUT、 POST、 DELETE) 為您的資源。 不包含讀取作業 (GET)。 如需資源動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md)。 您可以使用活動記錄在進行疑難排解時發現錯誤，或是監視貴組織使用者修改資源的方式。

活動記錄會保留 90 天。 您可以查詢任何的日期範圍，只要開始日期不是在過去 90 天以前。

您可以透過入口網站、PowerShell、Azure CLI、Insights REST API 或 [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)擷取活動記錄中的資訊。

## <a name="azure-portal"></a>Azure 入口網站

1. 若要透過入口網站檢視活動記錄，請選取 [監視]  。

    ![選取監視器](./media/resource-group-audit/select-monitor.png)

1. 選取 [活動記錄]  。

    ![選取活動記錄](./media/resource-group-audit/select-activity-log.png)

1. 您會看到最近作業的摘要。 系統會將一組預設篩選套用至作業。 請注意摘要的資訊包含使用者啟動動作，以及何時會發生什麼情況。

    ![檢視最近作業的摘要](./media/resource-group-audit/audit-summary.png)

1. 若要快速執行一組預先定義的篩選器，請選取**快速深入剖析**。

    ![選取 快速深入剖析](./media/resource-group-audit/select-quick-insights.png)

1. 選取其中一個選項。 例如，選取**次失敗的部署**若要查看部署錯誤。

    ![選取部署失敗](./media/resource-group-audit/select-failed-deployments.png)

1. 請注意篩選條件中已變更將焦點放在部署錯誤過去 24 小時。 會顯示符合篩選條件的作業。

    ![檢視篩選條件](./media/resource-group-audit/view-filters.png)

1. 若要將焦點放在特定作業，請變更篩選或套用新的篩選。 例如，下圖顯示 [時間範圍]  和 [資源類型]  的新值已設為設為儲存體帳戶。 

    ![設定篩選選項](./media/resource-group-audit/set-filter.png)

1. 如果您稍後需要再執行查詢，請選取 [釘選目前的篩選]  。

    ![釘選篩選](./media/resource-group-audit/pin-filters.png)

1. 指定篩選的名稱。

    ![名稱篩選](./media/resource-group-audit/name-filters.png)

1. 可在儀表板中使用篩選。

    ![在儀表板上顯示篩選](./media/resource-group-audit/show-dashboard.png)

1. 您可以從入口網站中，檢視資源的變更。 移設回預設值，在監視器中檢視，然後選取相關變更資源的作業。

    ![選取作業](./media/resource-group-audit/select-operation.png)

1. 選取 **修訂歷程記錄 （預覽）** 和挑選其中一個可用的作業。

    ![選取 變更歷程記錄](./media/resource-group-audit/select-change-history.png)

1. 會顯示在資源中的變更。

    ![顯示變更](./media/resource-group-audit/show-changes.png)

若要深入了解變更歷程記錄，請參閱[取得資源變更](../governance/resource-graph/how-to/get-resource-changes.md)。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要擷取記錄項目，請執行 **Get-AzLog** 命令。 您可提供額外的參數來篩選項目清單。 如果未指定開始和結束時間，則會傳回最後七天的項目。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

下列範例示範如何使用活動記錄來研究指定期間所採取的作業。 以日期格式指定開始和結束日期。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

或者，您可以使用日期函數來指定日期範圍，例如過去 14 天。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

您可以查閱特定使用者所採取的動作。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

您可以篩選失敗的作業。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

您可以查看該項目的狀態訊息，專注於一個錯誤。

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

您可以選取特定值來限制傳回的資料。

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串作業。 您可以提供搜尋準則，以篩選您所尋找的結果。 例如，您可以依照作業類型篩選。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

您可以使用資源圖表以查看資源變更歷程記錄。 如需詳細資訊，請參閱 <<c0> [ 取得資源變更](../governance/resource-graph/how-to/get-resource-changes.md)。

## <a name="azure-cli"></a>Azure CLI

若要擷取記錄項目，請搭配位移執行 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令，以指示時間範圍。

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

下列範例示範如何使用活動記錄來研究指定期間所採取的作業。 以日期格式指定開始和結束日期。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

您可以查閱由特定使用者採取的動作，即使是針對已不存在的資源群組。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

您可以篩選失敗的作業。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

您可以查看該項目的狀態訊息，專注於一個錯誤。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

您可以選取特定值來限制傳回的資料。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串作業。 您可以提供搜尋準則，以篩選您所尋找的結果。 例如，您可以依照作業類型篩選。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

您可以使用資源圖表以查看資源變更歷程記錄。 如需詳細資訊，請參閱 <<c0> [ 取得資源變更](../governance/resource-graph/how-to/get-resource-changes.md)。

## <a name="rest-api"></a>REST API

可用來處理活動記錄檔的 REST 作業屬於 [Insights REST API](/rest/api/monitor/)的一部分。 若要擷取活動記錄檔事件，請參閱 [列出訂用帳戶中的管理事件](/rest/api/monitor/activitylogs)。

## <a name="next-steps"></a>後續步驟

* Azure 活動記錄可以搭配 Power BI 用來更深入了解訂用帳戶中的動作。 請參閱 [在 Power BI 和其他工具中檢視和分析 Azure 活動記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)。
* 如要了解如何設定安全性原則，請參閱 [Azure 角色型存取控制](../role-based-access-control/role-assignments-portal.md)。
* 若要深入了解檢視部署作業的命令，請參閱[檢視部署作業](resource-manager-deployment-operations.md)。
* 若要了解如何防止刪除所有使用者的資源，請參閱 [使用 Azure Resource Manager 鎖定資源](resource-group-lock-resources.md)。
* 若要查看每個 Microsoft Azure Resource Manager 提供者可用的作業清單，請參閱 [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md)
