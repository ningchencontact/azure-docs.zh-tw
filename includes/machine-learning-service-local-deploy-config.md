---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: e8c0b35f2ac6d3468a5e38a90cc7f4de09d3682b
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348497"
---
`deploymentconfig.json`檔中的專案會對應至 LocalWebservice 的參數[。 deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)。 下表描述 JSON 檔中的實體與方法的參數之間的對應:

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為本機, 此值必須`local`是。 |
| `port` | `port` | 要在其上公開服務之 HTTP 端點的本機埠。 |

下列 JSON 是與 CLI 搭配使用的範例部署設定:

```json
{
    "computeType": "local",
    "port": 32267
}
```
