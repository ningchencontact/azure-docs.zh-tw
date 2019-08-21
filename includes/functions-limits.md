---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: d00af77a2d502cb534a4ab4a0db9231e5e8748b7
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636428"
---
| Resource | [耗用量方案](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium 方案](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service 計畫](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 相應放大 | 事件驅動 | 事件驅動 | [手動/自動調整](../articles/app-service/manage-scale-up.md) | 
| 執行個體數上限 | 200 | 20 | 10-20 |
|預設[超時](../articles/azure-functions/functions-scale.md#timeout)時間 (分鐘) |5 | 30 |30<sup>2</sup> |
|最大[時間輸出持續時間](../articles/azure-functions/functions-scale.md#timeout)(分鐘) |10 | 無限制 | 無界限<sup>3</sup> |
| 最大輸出連線數 (每個實例) | 600作用中 (1200 總計) | 無限制 | 無限制 |
| 要求大小上限 (MB)<sup>4</sup> | 100 | 100 | 100 |
| 最大查詢字串長度<sup>4</sup> | 4096 | 4096 | 4096 |
| 要求 URL 長度上限<sup>4</sup> | 8192 | 8192 | 8192 |
| 每個實例的[ACU](../articles/virtual-machines/windows/acu.md) | 100 | 210-840 | 100-840 |
| 最大記憶體 (每個實例 GB) | 1.5 | 3.5-14 | 1.75-14 |
| 每個方案的函數應用程式 |100 |100 |無界限<sup>5</sup> |
| [App Service 方案](../articles/app-service/overview-hosting-plans.md) | 每個[區域](https://azure.microsoft.com/global-infrastructure/regions/)100 |每個資源群組 100 個 |每個資源群組 100 個 |
| 儲存體<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| 每個應用程式的自訂網域</a> |500<sup>7</sup> |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |包含未系結的 SNI SSL 連線 | 包含未系結的 SNI SSL 和1個 IP SSL 連線 |包含未系結的 SNI SSL 和1個 IP SSL 連線 | 

<sup>1</sup>如需各種 App Service 計畫選項的特定限制, 請參閱[App Service 方案限制](../articles/azure-subscription-service-limits.md#app-service-limits)。  
<sup>2</sup>根據預設, App Service 計畫中的函式1.x 執行時間的超時不受限制。  
<sup>3</sup>要求 App Service 計畫必須設定為[Always On](../articles/azure-functions/functions-scale.md#always-on)。 以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費。  
<sup>4</sup> [主機中設定](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)了這些限制。  
<sup>5</sup>您可以裝載的函式應用程式實際數目, 取決於應用程式的活動、電腦實例的大小, 以及對應的資源使用率。  
<sup>6</sup>儲存限制是相同 App Service 方案中所有應用程式的暫存儲存體中的總內容大小。 取用方案會使用 Azure 檔案儲存體暫存儲存體。  
<sup>7</sup>當您的函數應用程式裝載于取用[方案](../articles/azure-functions/functions-scale.md#consumption-plan)時, 只支援 CNAME 選項。 針對高階[方案](../articles/azure-functions/functions-scale.md#premium-plan)或[App Service 方案](../articles/azure-functions/functions-scale.md#app-service-plan)中的函數應用程式, 您可以使用 CNAME 或 a 記錄來對應自訂網域。
