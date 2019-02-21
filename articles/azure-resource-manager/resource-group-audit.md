---
title: 檢視 Azure 活動記錄以監視資源 | Microsoft Docs
description: 使用活動記錄檢閱使用者動作和錯誤。 顯示 Azure 入口網站 PowerShell、Azure CLI 和 REST。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: e7bcfce4e9d23839c623be3b54e97d931e6454cb
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268289"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>檢視活動記錄以稽核對資源的動作

透過活動記錄檔，您可以判斷︰

* 訂用帳戶的資源在進行哪些作業
* 啟動作業的人員
* 作業進行的時間
* 作業的狀態
* 其他可能協助您研究作業的屬性值

活動記錄包含在您的資源上執行的所有寫入作業 (PUT、POST、DELETE)。 不包含讀取作業 (GET)。 如需資源動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md)。 您可以使用稽核記錄檔在進行疑難排解時發現錯誤，或是監視貴組織使用者修改資源的方式。

活動記錄會保留 90 天。 您可以查詢任何的日期範圍，只要開始日期不是在過去 90 天以前。

您可以透過入口網站、PowerShell、Azure CLI、Insights REST API 或 [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)擷取活動記錄檔中的資訊。

## <a name="the-azure-portal"></a>Azure 入口網站

1. 若要透過入口網站檢視活動記錄，請選取 [監視]。

    ![選取監視器](./media/resource-group-audit/select-monitor.png)

1. 選取 [活動記錄]。

    ![選取活動記錄](./media/resource-group-audit/select-activity-log.png)

1. 您會看到最近作業的摘要。 系統會將一組預設篩選套用至作業。

    ![檢視最近作業的摘要](./media/resource-group-audit/audit-summary.png)

1. 若要快速執行一組預先定義的篩選，請選取 [快速見解] 並挑選其中一個選項。

    ![選取查詢](./media/resource-group-audit/quick-insights.png)

1. 若要將焦點放在特定作業，請變更篩選或套用新的篩選。 例如，下圖顯示 [時間範圍] 和 [資源類型] 的新值已設為設為儲存體帳戶。 

    ![設定篩選選項](./media/resource-group-audit/set-filter.png)

1. 如果您稍後需要再執行查詢，請選取 [釘選目前的篩選]。

    ![釘選篩選](./media/resource-group-audit/pin-filters.png)

1. 指定篩選的名稱。

    ![名稱篩選](./media/resource-group-audit/name-filters.png)

1. 可在儀表板中使用篩選。

    ![在儀表板上顯示篩選](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 若要擷取記錄項目，請執行 **Get-AzLog** 命令。 您可提供額外的參數來篩選項目清單。 如果未指定開始和結束時間，則會傳回最後七天的項目。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    下列範例示範如何使用活動記錄來研究指定期間所採取的作業。 以日期格式指定開始和結束日期。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    或者，您可以使用日期函數來指定日期範圍，例如過去 14 天。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* 您可以查閱由特定使用者採取的動作，即使是針對已不存在的資源群組。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* 您可以篩選失敗的作業。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* 您可以查看該項目的狀態訊息，專注於一個錯誤。

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* 您可以選取特定值來限制傳回的資料。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* 視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串作業。 您可以提供搜尋準則，以篩選您所尋找的結果。 例如，您可以依照作業類型篩選。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Azure CLI

* 若要擷取記錄項目，請搭配位移執行 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令，以指示時間範圍。

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  下列範例示範如何使用活動記錄來研究指定期間所採取的作業。 以日期格式指定開始和結束日期。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* 您可以查閱由特定使用者採取的動作，即使是針對已不存在的資源群組。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* 您可以篩選失敗的作業。

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* 您可以查看該項目的狀態訊息，專注於一個錯誤。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* 您可以選取特定值來限制傳回的資料。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* 視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串作業。 您可以提供搜尋準則，以篩選您所尋找的結果。 例如，您可以依照作業類型篩選。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>REST API

可用來處理活動記錄檔的 REST 作業屬於 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)的一部分。 若要擷取活動記錄檔事件，請參閱 [列出訂用帳戶中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)。

## <a name="next-steps"></a>後續步驟

* Azure 活動記錄檔可以搭配 Power BI 用來更深入了解訂用帳戶中的動作。 請參閱 [在 Power BI 和其他工具中檢視和分析 Azure 活動記錄檔](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)。
* 如要了解如何設定安全性原則，請參閱 [Azure 角色型存取控制](../role-based-access-control/role-assignments-portal.md)。
* 若要深入了解檢視部署作業的命令，請參閱[檢視部署作業](resource-manager-deployment-operations.md)。
* 若要了解如何防止刪除所有使用者的資源，請參閱 [使用 Azure Resource Manager 鎖定資源](resource-group-lock-resources.md)。
* 若要查看每個 Microsoft Azure Resource Manager 提供者可用的作業清單，請參閱 [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md)
