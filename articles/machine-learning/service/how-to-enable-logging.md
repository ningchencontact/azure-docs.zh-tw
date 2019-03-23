---
title: 在 Azure Machine Learning 服務中啟用記錄
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務中使用預設 Python 記錄套件以及使用 SDK 特有的功能啟用記錄。
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/20/2019
ms.openlocfilehash: ce510168e2aa92758a3468fa55ff7b2a8d39b547
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360262"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>在 Azure Machine Learning 服務中啟用記錄

Azure Machine Learning Python SDK 可讓您使用預設 Python 記錄套件以及使用 SDK 特有的功能啟用記錄，兩者皆可用於本機記錄和記錄至您在入口網站中的工作區。 記錄可提供開發人員應用程式狀態的即時資訊，並可協助診斷錯誤或警告。 在本文中，您會了解在下列領域啟用記錄的不同方式：

> [!div class="checklist"]
> * 定型模型和計算目標
> * 映像建立
> * 已部署的模型
> * Python `logging` 設定

[建立 Azure 機器學習服務工作區](setup-create-workspace.md)。 使用[指南](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)如 SDK 的詳細資訊。

## <a name="training-models-and-compute-target-logging"></a>定型模型和計算目標記錄

在模型定型過程中，有多種方式可以啟用記錄，顯示的範例將說明常見的設計模式。 您可以使用 `Experiment` 類別上的 `start_logging` 函式，輕鬆地將執行相關的資料記錄至雲端的工作區。

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

請參閱參考文件[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)其他記錄函式的類別。

若要在定型過程中啟用應用程式狀態的本機記錄，請使用 `show_output` 參數。 啟用詳細記錄可讓您查看定型過程的詳細資料，以及任何遠端資源或計算目標的相關資訊。 使用下列程式碼來啟用記錄實驗提交。

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

您也可以針對產生結果的執行，在 `wait_for_completion` 函式中使用相同參數。

```python
run.wait_for_completion(show_output=True)
```

SDK 也支援針對定型的特定案例使用預設的 Python 記錄套件。 下列範例在 `AutoMLConfig` 物件中啟用 `INFO` 層級的記錄。

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

再建立持續性的計算目標時，您也可以使用 `show_output` 參數。 在 `wait_for_completion` 函式中指定參數以在計算目標建立期間啟用記錄。

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>映像建立期間的記錄

啟用映像期間的記錄將可讓您查看建置過程中的任何錯誤。 在 `wait_for_deployment()` 函式上設定 `show_output` 參數。

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>已部署模型的記錄

若要從先前部署的 Web 服務擷取記錄，請載入服務並使用 `get_logs()` 函式。 記錄可能包含部署期間發生之任何錯誤的相關詳細資訊。

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

您也可以啟用 Application Insights 來針對 Web 服務記錄自訂堆疊追蹤，這可讓您監視要求/回應次數、失敗率和例外狀況。 在現有 Web 服務上呼叫 `update()` 函式以啟用 Application Insights。

```python
service.update(enable_app_insights=True)
```

如需如何在 Azure 入口網站中使用 Application Insights 的詳細資訊，請參閱[作法](how-to-enable-app-insights.md#enable-and-disable-in-the-portal)。

## <a name="python-native-logging-settings"></a>Python 原生記錄設定

SDK 中的特定記錄可能包含錯誤，其指示您將記錄層級設為 DEBUG。 若要設定記錄層級，請將下列程式碼加入到您的指令碼中。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```