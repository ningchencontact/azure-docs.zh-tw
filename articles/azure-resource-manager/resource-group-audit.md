---
title: View Azure activity logs to monitor resources
description: 使用活動記錄檢閱使用者動作和錯誤。 Shows Azure portal PowerShell, Azure CLI, and REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 7b70d2a8c158b6f8b3dc87c22e5ca90f2861aebb
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422291"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>View activity logs to monitor actions on resources

透過活動記錄，您可以判斷︰

* 訂用帳戶的資源在進行哪些作業
* 啟動作業的人員
* 作業進行的時間
* 作業的狀態
* 其他可能協助您研究作業的屬性值

The activity log contains all write operations (PUT, POST, DELETE) for your resources. 不包含讀取作業 (GET)。 如需資源動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md)。 您可以使用活動記錄在進行疑難排解時發現錯誤，或是監視貴組織使用者修改資源的方式。

活動記錄會保留 90 天。 您可以查詢任何的日期範圍，只要開始日期不是在過去 90 天以前。

您可以透過入口網站、PowerShell、Azure CLI、Insights REST API 或 [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)擷取活動記錄中的資訊。

## <a name="azure-portal"></a>Azure Portal

To view the activity logs through the portal, follow these steps:

1. On the Azure portal menu, select **Monitor**, or search for and select **Monitor** from any page.

    ![選取監視器](./media/resource-group-audit/select-monitor-from-menu.png)

1. 選取 [活動記錄]。

    ![選取活動記錄](./media/resource-group-audit/select-activity-log.png)

1. 您會看到最近作業的摘要。 系統會將一組預設篩選套用至作業。 Notice the information on the summary includes who started the action and when it happened.

    ![檢視最近作業的摘要](./media/resource-group-audit/audit-summary.png)

1. To quickly run a pre-defined set of filters, select **Quick Insights**.

    ![Select quick insights](./media/resource-group-audit/select-quick-insights.png)

1. 選取其中一個選項。 For example, select **Failed deployments** to see errors from deployments.

    ![Select failed deployments](./media/resource-group-audit/select-failed-deployments.png)

1. Notice the filters have been changed to focus on deployment errors in the last 24 hours. Only operations that match the filters are displayed.

    ![檢視篩選條件](./media/resource-group-audit/view-filters.png)

1. 若要將焦點放在特定作業，請變更篩選或套用新的篩選。 例如，下圖顯示 [時間範圍] 和 [資源類型] 的新值已設為設為儲存體帳戶。

    ![設定篩選選項](./media/resource-group-audit/set-filter.png)

1. 如果您稍後需要再執行查詢，請選取 [釘選目前的篩選]。

    ![釘選篩選](./media/resource-group-audit/pin-filters.png)

1. 指定篩選的名稱。

    ![名稱篩選](./media/resource-group-audit/name-filters.png)

1. 可在儀表板中使用篩選。 On the Azure portal menu, select **Dashboard**.

    ![在儀表板上顯示篩選](./media/resource-group-audit/activity-log-on-dashboard.png)

1. From the portal, you can view changes to a resource. Go back to the default view in Monitor, and select an operation that involved changing a resource.

    ![Select operation](./media/resource-group-audit/select-operation.png)

1. Select **Change history (Preview)** and pick one of the available operations.

    ![Select change history](./media/resource-group-audit/select-change-history.png)

1. The changes in the resource are displayed.

    ![Show changes](./media/resource-group-audit/show-changes.png)

To learn more about change history, see [Get resource changes](../governance/resource-graph/how-to/get-resource-changes.md).

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

You can look up the actions taken by a particular user.

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

You can use Resource Graph to see the change history for a resource. For more information, see [Get resource changes](../governance/resource-graph/how-to/get-resource-changes.md).

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

You can use Resource Graph to see the change history for a resource. For more information, see [Get resource changes](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

可用來處理活動記錄檔的 REST 作業屬於 [Insights REST API](/rest/api/monitor/)的一部分。 若要擷取活動記錄檔事件，請參閱 [列出訂用帳戶中的管理事件](/rest/api/monitor/activitylogs)。

## <a name="next-steps"></a>後續步驟

* Azure 活動記錄可以搭配 Power BI 用來更深入了解訂用帳戶中的動作。 請參閱 [在 Power BI 和其他工具中檢視和分析 Azure 活動記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)。
* 如要了解如何設定安全性原則，請參閱 [Azure 角色型存取控制](../role-based-access-control/role-assignments-portal.md)。
* To view more details about the changes to your applications from the infrastructure layer all the way to application deployment, see [Use Application Change Analysis in Azure Monitor](../azure-monitor/app/change-analysis.md).
* 若要深入了解檢視部署作業的命令，請參閱[檢視部署作業](resource-manager-deployment-operations.md)。
* 若要了解如何防止刪除所有使用者的資源，請參閱 [使用 Azure Resource Manager 鎖定資源](resource-group-lock-resources.md)。
* 若要查看每個 Microsoft Azure Resource Manager 提供者可用的作業清單，請參閱 [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md)
