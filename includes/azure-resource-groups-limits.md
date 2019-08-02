---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/30/2019
ms.author: tomfitz
ms.openlocfilehash: f027f5b295d75c105e81360a10718239e5b08e4d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698278"
---
| Resource | 預設限制 | 上限 |
| --- | --- | --- |
| 每個資源[群組](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)的資源 (每個資源類型) |800 |某些資源類型可能會超過800限制。 請參閱[每個資源群組不限於800實例的資源](../articles/azure-resource-manager/resources-without-rg-limit.md)。 |
| 部署歷程記錄中每個資源群組的部署 |800<sup>1</sup> |800 |
| 每個部署的資源 |800 |800 |
| 每個唯一範圍的管理鎖定 |20 |20 |
| 每個資源或資源群組的標記數目 |15 |15 |
| 標記金鑰長度 |512 |512 |
| 標記值長度 |256 |256 |

<sup>1</sup>如果您達到每個資源群組的800部署限制, 請從已不再需要的歷程記錄中刪除部署。 從部署歷程記錄中刪除專案並不會影響已部署的資源。 您可以使用 [az group deployment delete](/cli/azure/group/deployment) (Azure CLI) 或 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) (PowerShell) 以從歷程記錄中刪除項目。  如需在持續整合和持續傳遞 (CI/CD) 案例中自動刪除部署的 PowerShell 腳本, 請參閱[remove-deployments](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)。

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

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊, 請參閱在[部署 Azure 資源時使用連結的範本](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
