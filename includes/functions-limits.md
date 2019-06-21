---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: f2470f937d2d812bf79cea3c23d89a50717a5a92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277164"
---
| 資源 | [取用方案](../articles/azure-functions/functions-scale.md#consumption-plan) | [進階方案](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service 方案](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 相應放大 | 事件驅動 | 事件驅動 | [手動/自動調整規模](../articles/app-service/web-sites-scale.md) | 
| 執行個體上限 | 200 | 20 | 10-20 |
|預設值[逾時持續時間](../articles/azure-functions/functions-scale.md#timeout)（分鐘） |5 | 30 |30<sup>2</sup> |
|最大[逾時持續時間](../articles/azure-functions/functions-scale.md#timeout)（分鐘） |10 | 無限制 | 未繫結<sup>3</sup> |
| 輸出連線數目上限 （每個執行個體） | 600 個作用中 （1200年總計） | 無限制 | 無限制 |
| 最大要求大小 (MB)<sup>4</sup> | 100 | 100 | 100 |
| 查詢字串的最大長度<sup>4</sup> | 4096 | 4096 | 4096 |
| 要求 URL 的最大長度<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md)每個執行個體 | 100 | 210-840 | 100-840 |
| 最大記憶體 (每個執行個體的 GB) | 1.5 | 3.5-14 | 1.75-14 |
| 每個計劃的函式應用程式 |100 |100 |unbounded<sup>5</sup> |
| [App Service 方案](../articles/app-service/overview-hosting-plans.md) | 每 100 個[區域](https://azure.microsoft.com/global-infrastructure/regions/) |每個資源群組 100 個 |每個資源群組 100 個 |
| 儲存體<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| 每個應用程式的自訂網域</a> |500<sup>7</sup> |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |包含無限制的 SNI SSL 連線 | 無限制的 SNI SSL 和 1 包含的 IP SSL 連線 |無限制的 SNI SSL 和 1 包含的 IP SSL 連線 | 

<sup>1</sup>如需各種 App Service 計劃選項的特定限制，請參閱[App Service 方案限制](../articles/azure-subscription-service-limits.md#app-service-limits)。  
<sup>2</sup>根據預設，App Service 方案中的 Functions 1.x 執行階段的逾時是未繫結。  
<sup>3</sup>需要 App Service 方案設為[Always On](../articles/azure-functions/functions-scale.md#always-on)。 用多少付多少標準[費率](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>這些限制[主應用程式中設定](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)。  
<sup>5</sup>函式應用程式可裝載的實際數目取決於應用程式的活動、 機器執行個體和對應的資源使用率的大小。
<sup>6</sup>的儲存體限制是總內容大小暫存儲存體中跨所有應用程式相同的 App Service 方案中。 取用方案會使用 Azure 檔案的暫存儲存體。  
<sup>7</sup>函式應用程式中的裝載時[耗用量計劃](../articles/azure-functions/functions-scale.md#consumption-plan)，支援只有 [CNAME] 選項。 函式中的應用程式[進階方案](../articles/azure-functions/functions-scale.md#premium-plan)該[App Service 方案](../articles/azure-functions/functions-scale.md#app-service-plan)，您可以將自訂網域使用 CNAME 或 A 記錄對應。
