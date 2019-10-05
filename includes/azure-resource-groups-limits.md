---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 1190798b234f9c73e02fda41c03ffa296246be63
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2019
ms.locfileid: "71975282"
---
| Resource | 預設限制 | 上限 |
| --- | --- | --- |
| 每個資源[群組](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)的資源（每個資源類型） |800 |某些資源類型可能會超過800限制。 請參閱[每個資源群組不限於800實例的資源](../articles/azure-resource-manager/resources-without-rg-limit.md)。 |
| 部署歷程記錄中每個資源群組的部署 |800<sup>1</sup> |800 |
| 每個部署的資源 |800 |800 |
| 每個唯一範圍的管理鎖定 |20 |20 |
| 每個資源或資源群組的標記數目 |50 |50 |
| 標記金鑰長度 |512 |512 |
| 標記值長度 |256 |256 |

<sup>1</sup>如果您達到每個資源群組的800部署限制，請從已不再需要的歷程記錄中刪除部署。 從部署歷程記錄中刪除專案並不會影響已部署的資源。 如需詳細資訊，請參閱[解決部署計數超過800時的錯誤](../articles/azure-resource-manager/deployment-quota-exceeded.md)。

#### <a name="template-limits"></a>範本限制

| 值 | 預設限制 | 上限 |
| --- | --- | --- |
| 參數 |256 |256 |
| 變數 |256 |256 |
| 資源 (包括複本計數) |800 |800 |
| outputs |64 |64 |
| 範本運算式 |24,576 個字元 |24,576 個字元 |
| 已匯出範本中的資源 |200 |200 | 
| 範本大小 |4 MB |4 MB |
| 參數檔案大小 |64 KB |64 KB |

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱在[部署 Azure 資源時使用連結的範本](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
