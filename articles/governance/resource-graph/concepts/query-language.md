---
title: 了解查詢語言
description: 描述可與 Azure Resource Graph 搭配使用的可用 Kusto 運算子和函數。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 54bb0b4f21752b91ceb9d4004c153ff4d95006aa
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976768"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查詢語言

Azure Resource Graph 查詢語言支援多個運算子與函式。 每個工作和操作都會以 [Azure 資料總管](../../../data-explorer/data-explorer-overview.md)為依據。

了解 Resource Graph 所使用之查詢語言的最佳方式是從適用於 Azure 資料總管[查詢語言](/azure/kusto/query/index)的文件開始。 這份文件讓您能夠了解如何將語言結構化，以及各種支援的運算子和函式如何一起運作。

## <a name="supported-tabular-operators"></a>支援的表格式運算子

以下是 Resource Graph 中支援的表格式運算子清單：

- [計數](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [排序依據](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>支援的函式

以下是 Resource Graph 中支援的函式清單：

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Escape 字元

某些屬性名稱（例如包含 `.` 或 `$`）必須在查詢中包裝或換行，否則屬性名稱會不正確地轉譯，而且不會提供預期的結果。

- `.`-將屬性名稱換行，如下所示： `['propertyname.withaperiod']`
  
  包裝屬性 odata 的範例查詢 _。類型_：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-將屬性名稱中的字元轉義。 使用的逸出字元取決於從執行的 shell Resource Graph。

  - **bash** -  @ no__t-2

    在 bash 中 _@no__t 1type_屬性的範例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -不要將 `$` 個字元。

  - **PowerShell** - ``` ` ```

    在 PowerShell 中將屬性 _@no__t_轉義的範例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式
- 了解[探索資源](explore-resources.md)