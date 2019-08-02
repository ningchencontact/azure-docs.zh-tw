---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: a87773c8f6ddda11b62064758b46d666740a03c2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556935"
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
