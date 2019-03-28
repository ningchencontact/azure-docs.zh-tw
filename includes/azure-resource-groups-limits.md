---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3dc09de6afaddeb06b0243eb46e888b673109545
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505858"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每個資源[資源群組](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)，每個資源類型 |800 |每個資源類型而異 |
| 部署歷程記錄中每個資源群組的部署 |800 |800 |
| 每個部署的資源 |800 |800 |
| 每個唯一的範圍的管理鎖定 |20 |20 |
| 每個資源或資源群組的標記數目 |15 |15 |
| 標記金鑰長度 |512 |512 |
| 標記值長度 |256 |256 |


#### <a name="template-limits"></a>範本限制

| 值 | 預設限制 | 上限 |
| --- | --- | --- |
| 參數 |256 |256 |
| 變數 |256 |256 |
| 資源，包括複本計數 |800 |800 |
| 輸出 |64 |64 |
| 範本運算式 |24,576 個字元 |24,576 个字符 |
| 已导出模板中的资源 |200 |200 | 
| 範本大小 |1 MB |1 MB |
| 參數檔案大小 |64 KB |64 KB |

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱 <<c0> [ 當您部署 Azure 資源時使用連結的範本](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。

如果每個資源群組的部署達到 800 個數量限制，請從歷程記錄中刪除不再需要的部署。 您可以使用歷程記錄中刪除項目[az 群組部署刪除](/cli/azure/group/deployment)Azure cli。 您也可以使用[移除 AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)在 PowerShell 中。 從部署歷程記錄中刪除的項目不會影響部署資源。 