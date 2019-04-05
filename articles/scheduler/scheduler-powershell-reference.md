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
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044992"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure 排程器 PowerShell Cmdlet 參考

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 會取代 Azure 排程器，Azure 排程器之後將無法使用。 若要排定作業，請[改為試用 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要撰寫用於建立及管理排程器作業與作業集合的指令碼，您可以使用 PowerShell Cmdlet。 此文章列出主要 [Azure 排程器的 PowerShell Cmdlet](/powershell/module/azurerm.scheduler)，並提供其參考文章的連結。 若要為 Azure 訂用帳戶安裝 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。 如需 [Azure Resource Manager Cmdlet](/powershell/azure/overview) 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

| Cmdlet | 描述 |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |停用工作集合。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |啟用工作集合。 |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |取得排程器作業。 |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |取得工作集合。 |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |取得工作歷程記錄。 |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |建立 HTTP 工作。 |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |建立工作集合。 |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | 建立服務匯流排佇列作業。 |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |建立服務匯流排主題作業。 |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |建立儲存體佇列作業。 |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |移除排程器工作。 |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |移除工作集合。 |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |修改排程器 HTTP 工作。 |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |修改工作集合。 |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |修改服務匯流排佇列作業。 |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |修改服務匯流排主題作業。 |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |修改儲存體佇列作業。 |
||| 

如需詳細資訊，您可以執行下列任何 Cmdlet： 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>請參閱

* [何謂 Azure 排程器？](scheduler-intro.md)
* [概念、 術語及實體階層](scheduler-concepts-terms.md)
* [建立並排程第一個工作-Azure 入口網站](scheduler-get-started-portal.md)
* [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/mt629143)
