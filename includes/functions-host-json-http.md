---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 35b087d13099b975a1c9c6d2dbd449935f5f0d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66249075"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|routePrefix|api|適用於所有路由的路由前置詞。 若要移除預設前置詞，請使用空字串。 |
|maxOutstandingRequests|200<sup>*</sup>|在任何指定時間保留的未完成要求數目上限。 此限制包括已排入佇列但尚未開始執行的要求，以及任何進行中的執行。 會以 429「忙碌」回應來拒絕任何超過此限制的連入要求。 這樣可讓呼叫者採用以時間為基礎的重試策略，並且也協助您控制要求延遲的上限。 此動作只會控制在指令碼主機執行路徑內發生的佇列處理。 其他佇列 (例如 ASP.NET 要求佇列) 仍然有效，且不受此設定的影響。 <sup>*</sup>預設版本 1.x 未繫結 (`-1`)。 在取用方案中，版本 2.x 的預設值是 200。 預設版本 2.x 專用的方案中未繫結 (`-1`)。|
|maxConcurrentRequests|100<sup>*</sup>|要平行執行的 HTTP 函式數目上限。 這可讓您控制並行作業，幫助您管理資源使用率。 例如，您可能會有使用大量系統資源 (記憶體/CPU/通訊端) 的 HTTP 函式，以致於並行率太高時會造成問題。 或者，如果函式對第三方服務發出傳出要求，則需要限制這些呼叫的速率。 在這些情況下，套用節流會有所幫助。 <sup>*</sup>預設版本 1.x 未繫結 (`-1`)。 在取用方案中，版本 2.x 的預設值是 100。 預設版本 2.x 專用的方案中未繫結 (`-1`)。|
|dynamicThrottlesEnabled|true<sup>*</sup>|啟用時，此設定會促使要求處理管線定期檢查系統效能計數器，例如連線/執行緒/處理程序/記憶體/CPU/其他，而且如果這些計數器中任一個超過內建的臨界值上限 (80%)，則要求會遭到拒絕，並包含 429「忙碌」的回應，直到計數器回到正常水平。 <sup>*</sup>預設版本 1.x 為 false。 在取用方案中，版本 2.x 的預設值是 true。 在專屬方案中，版本 2.x 的預設值是 false。|
