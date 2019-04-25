---
title: PowerShell Cdlet 參考- Azure 排程器
description: 了解 Azure 排程器的 PowerShell Cmdlet
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 53b68a5dc72277c9fd44b36d346e5b5c91b53a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344371"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure 排程器 PowerShell Cmdlet 參考

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 會取代 Azure 排程器，Azure 排程器之後將無法使用。 若要排定作業，請[改為試用 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要撰寫用於建立及管理排程器作業與作業集合的指令碼，您可以使用 PowerShell Cmdlet。 本文會列出 Azure 排程器的主要的 PowerShell cmdlet，其參考文件的連結。 若要為 Azure 訂用帳戶安裝 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。 如需 [Azure Resource Manager Cmdlet](/powershell/azure/overview) 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

| Cmdlet | 描述 |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |停用工作集合。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |啟用工作集合。 |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |取得排程器作業。 |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |取得工作集合。 |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |取得工作歷程記錄。 |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |建立 HTTP 工作。 |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |建立工作集合。 |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | 建立服務匯流排佇列作業。 |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |建立服務匯流排主題作業。 |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |建立儲存體佇列作業。 |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |移除排程器工作。 |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |移除工作集合。 |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |修改排程器 HTTP 工作。 |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |修改工作集合。 |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |修改服務匯流排佇列作業。 |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |修改服務匯流排主題作業。 |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |修改儲存體佇列作業。 |
||| 

如需詳細資訊，您可以執行下列任何 Cmdlet： 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>請參閱

* [什麼是 Azure 排程器？](scheduler-intro.md)
* [概念、術語及實體階層](scheduler-concepts-terms.md)
* [建立及排定您的第一個作業 - Azure 入口網站](scheduler-get-started-portal.md)
* [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)
