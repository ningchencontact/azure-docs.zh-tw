---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: fef6225812980900ad55944644310433a6105de2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556871"
---
當您建立新的模型時, 您必須手動更新您想要使用新模型的每個服務。 若要更新 Web 服務，請使用 `update` 方法。 下列程式碼示範如何使用 SDK 來更新 web 服務的模型:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

您也可以使用 ML CLI 來更新 web 服務。 下列範例示範如何註冊新的模型, 然後將 web 服務更新為使用新的模型:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 在此範例中, 會使用 JSON 檔, 將來自註冊命令的模型資訊傳遞至 update 命令。