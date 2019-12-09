---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: c288ea4a2c62999f27b3f07c39df40dae7ab1426
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926966"
---
`deploymentconfig.json` 檔中的專案會對應至 LocalWebservice 的參數。 [deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)。 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為本機目標，此值必須是 `local`。 |
| `port` | `port` | 要在其上公開服務之 HTTP 端點的本機埠。 |

此 JSON 是與 CLI 搭配使用的範例部署設定：

```json
{
    "computeType": "local",
    "port": 32267
}
```
