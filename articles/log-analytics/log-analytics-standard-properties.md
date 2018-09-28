---
title: Azure 監視器 Log Analytics 記錄中的標準屬性 | Microsoft Docs
description: 說明 Azure 監視器 Log Analytics 中的多種資料類型通用的屬性。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955862"
---
# <a name="standard-properties-in-log-analytics-records"></a>Log Analytics 記錄中的標準屬性
[Log Analytics](../log-analytics/log-analytics-queries.md) 中的資料會儲存為一組記錄，每筆記錄分別屬於具有一組唯一屬性的特定資料類型。 有許多資料類型都具有多種類型之間通用的標準屬性。 本文將說明這些屬性，並提供在查詢中加以使用的範例。

其中有部分屬性尚在實作程序中，因此您可能會在某些資料類型中看到這些屬性，但在其他類型中卻沒看到。


## <a name="resourceid"></a>_ResourceId
**_ResourceId** 屬性會保存與記錄相關聯的資源所具備的唯一識別碼。 這可讓您有標準屬性可用來將查詢範圍限定於來自特定資源的記錄，或跨多個資料表聯結相關的資料。

就 Azure 資源而言，**_ResourceId** 的值為 [Azure 資源識別碼 URL](../azure-resource-manager/resource-group-template-functions-resource.md)。 此屬性目前僅適用於 Azure 資源，但未來將擴充至 Azure 以外的資源，例如內部部署電腦。 

### <a name="examples"></a>範例
下列範例顯示將每一部電腦的效能和事件資料相聯結的查詢。 其中顯示識別碼為 _101_ 且處理器使用率超過 50% 的所有事件。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

下列範例顯示將 _AzureActivity_ 記錄與 _SecurityEvent_ 記錄相聯結的查詢。 其中顯示已登入這些機器的使用者所產生的所有活動作業。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>後續步驟

- 深入了解 [Log Analytics 資料的儲存方式](../log-analytics/log-analytics-queries.md)。
- 參與[在 Log Analytics 中撰寫查詢](../log-analytics/query-language/get-started-queries.md)的課程。
- 參與[聯結 Log Analytics 查詢中的資料表](../log-analytics/query-language/joins.md)的課程。