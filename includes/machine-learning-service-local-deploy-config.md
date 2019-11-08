---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799714"
---
`deploymentconfig.json` 檔中的專案會對應至 LocalWebservice 的參數。 [deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)。 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 說明 |
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
