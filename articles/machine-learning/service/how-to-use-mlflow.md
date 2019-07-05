---
title: MLflow 搭配 Azure Machine Learning 服務
titleSuffix: Azure Machine Learning service
description: 了解如何記錄計量和使用 Azure Machine Learning 服務的 MLflow 程式庫的成品
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: d0bc4620d0c55d6e94a3d99c39ab405dab2743e5
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461656"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>MLflow 搭配 Azure Machine Learning 服務 （預覽）

這篇文章示範如何使用 MLflow 的追蹤 URI 和記錄 API，以下合稱也稱為 MLflow 追蹤，Azure Machine Learning 服務，可追蹤及記錄您的實驗計量和中的成品與您[Azure Machine Learning服務工作區](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace)。 如果您已經使用 MLflow 追蹤為您的實驗，工作區會提供集中式、 安全且可擴充的位置來儲存訓練計量和模型。

[MLflow](https://www.mlflow.org)是用於管理您的機器學習服務實驗生命週期的開放原始碼程式庫。 [MLFlow 追蹤](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)是元件的記錄，並追蹤您的訓練執行計量的 MLflow 和模型成品是否會在本機執行您的實驗虛擬機器上，或在遠端計算叢集。
![使用 azure machine learning 圖表 mlflow](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比較 MLflow 和 Azure Machine Learning 的用戶端

 下表摘要說明不同的用戶端可以使用 Azure Machine Learning 服務和其各自的函式功能。

 MLflow 追蹤提供計量的記錄和構件儲存體功能，就只能使用透過[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

| | MLflow 追蹤 | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> CLI | Azure 入口網站|
|-|-|-|-|-|
| 管理工作區 |   | ✓ |  ✓ | ✓  |
| 使用資料存放區  |   | ✓ |  ✓ |    |
| 記錄度量      | ✓ | ✓ |    |    |
| 上傳成品 | ✓ | ✓ |    |    |
| 檢視計量     | ✓ | ✓ | ✓  | ✓ |
| 管理計算   |   | ✓ | ✓  | ✓ |
| 部署模型    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>必要條件

* [安裝 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* [您的本機電腦上安裝 Azure Machine Learning Python SDK，並建立 Azure Machine Learning 工作區](setup-create-workspace.md#sdk)。 SDK 會提供如 MLflow 來存取您的工作區的連線。

## <a name="track-local-runs"></a>追蹤本機執行

安裝`azureml-contrib-run`MLflow 追蹤與 Azure Machine Learning 上使用您在本機執行的 Jupyter Notebook 或程式碼編輯器中的實驗的封裝。

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>當我們正致力改善本服務，但經常變更 azureml.contrib 命名空間。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

匯入`mlflow`並[ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)類別，以存取 MLflow 的追蹤 URI，並設定您的工作區。

下列程式碼中，`get_mlflow_tracking_uri()`方法會將唯一的追蹤 URI 位址指派至工作區`ws`，和`set_tracking_uri()`點 MLflow 追蹤該位址的 URI。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>追蹤 URI 是有效長達一小時或更少。 如果段閒置時間之後重新啟動您的指令碼，使用 get_mlflow_tracking_uri API 來取得新的 URI。

設定 MLflow 實驗名稱，與`set_experiment()`並開始執行訓練`start_run()`。 然後使用`log_metric()`啟用 MLflow 記錄 API 並開始記錄您執行計量的訓練。

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>追蹤遠端執行

遠端執行，可讓您定型更強大的計算模型，例如已啟用 GPU 的虛擬機器，或 Machine Learning 計算叢集。 請參閱[設定用於定型模型的計算目標](how-to-set-up-training-targets.md)來了解不同的計算選項。

設定您的計算及定型執行環境[ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)類別。 包含`mlflow`並`azure-contrib-run`pip 的環境中的封裝[ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)一節。 然後建構[ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)使用遠端計算作為計算目標。

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

在您訓練指令碼中，匯入`mlflow`使用 MLflow 記錄 Api，並開始記錄您執行的計量。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

此計算及定型執行組態中，使用`Experiment.submit("train.py")`提交執行的方法。 這會自動設定追蹤 URI MLflow，並將導向至您的工作區的記錄時 MLflow。

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>檢視計量與您的工作區中的成品

計量和成品從 MLflow 記錄會保留在您的工作區[Azure 入口網站](https://portal.azure.com)。 若要檢視任何時間，請瀏覽至您的工作區，並依名稱尋找實驗。

## <a name="clean-up-resources"></a>清除資源

如果您不打算使用您的工作區中的記錄的計量和成品，個別地刪除它們的功能目前無法使用。 相反地，刪除資源群組，其中包含儲存體帳戶和工作區中，因此您不會產生任何費用：

1. 在 Azure 入口網站中，選取最左邊的 [資源群組]  。

   ![在 Azure 入口網站中刪除](media/how-to-use-mlflow/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 選取 [刪除資源群組]  。

1. 輸入資源群組名稱。 然後選取 [刪除]  。

## <a name="example-notebooks"></a>Notebook 範例

[MLflow 搭配 Azure ML notebook](https://aka.ms/azureml-mlflow-examples)示範這篇文章中的概念。

## <a name="next-steps"></a>後續步驟

* [如何將模型部署](how-to-deploy-and-where.md)。
