---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174345"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每個資源[資源群組](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)，每個資源類型 |800 |每個資源類型而異 |
| 部署歷程記錄中每個資源群組的部署 |800<sup>1</sup> |800 |
| 每個部署的資源 |800 |800 |
| 每個唯一的範圍的管理鎖定 |20 |20 |
| 每個資源或資源群組的標記數目 |15 |15 |
| 標記金鑰長度 |512 |512 |
| 標記值長度 |256 |256 |

<sup>1</sup>達到 800 的部署，每個資源群組的限制，如果從不再需要的歷程記錄中刪除部署。 從部署歷程記錄中刪除的項目不會影響已部署的資源。 您可以使用 [az group deployment delete](/cli/azure/group/deployment) (Azure CLI) 或 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) (PowerShell) 以從歷程記錄中刪除項目。  針對 PowerShell 自動化指令碼刪除部署中持續整合與持續傳遞 (CI/CD) 案例中，請參閱[移除 deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)。

#### <a name="template-limits"></a>範本限制

| 值 | 預設限制 | 上限 |
| --- | --- | --- |
| 參數 |256 |256 |
| 變數 |256 |256 |
| 資源 (包括複本計數) |800 |800 |
| 輸出 |64 |64 |
| 範本運算式 |24,576 個字元 |24,576 個字元 |
| 已匯出範本中的資源 |200 |200 | 
| 範本大小 |1 MB |1 MB |
| 參數檔案大小 |64 KB |64 KB |

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱 <<c0> [ 當您部署 Azure 資源時使用連結的範本](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
