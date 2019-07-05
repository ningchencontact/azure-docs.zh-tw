---
title: 訓練及註冊 scikit-learn-了解模型
titleSuffix: Azure Machine Learning service
description: 這篇文章會示範如何訓練並註冊 scikit-learn-了解使用 Azure Machine Learning 服務的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: d2c9b104d1fe9333221bc20e7e23b436358c9ece
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514018"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>定型，並向 Azure Machine Learning 服務的規模的 scikit-learn 模型

這篇文章會示範如何訓練並註冊 scikit-learn 的模型，使用 Azure Machine Learning 服務。 它會使用熱門[鳶尾花資料集](https://archive.ics.uci.edu/ml/datasets/iris)分類鳶尾花映像，以自訂[scikit-learn-了解](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)類別。

Scikit-learn 等最了解是常用於機器學習服務的開放原始碼運算架構。 使用 Azure Machine Learning 服務時，您可以快速擴充使用彈性的雲端計算資源的開放原始碼訓練作業。 您也可以追蹤您的定型執行、 版本模型部署的模型，以及其他等等。

不論您正在開發由下而上 scikit-learn 模型，或是您要將現有的模型帶到雲端，Azure Machine Learning 服務可協助您建置可實際執行的模型。

## <a name="prerequisites"></a>必要條件

在這兩個環境上執行此程式碼：
 - Azure Machine Learning Notebook VM-不需要下載或必要的安裝

    - 完成[雲端為基礎的 notebook 快速入門](quickstart-run-cloud-notebook.md)建立專用的 notebook 伺服器 SDK 與範例存放庫中預先載入。
    - 在 notebook 伺服器上的 [samples] 資料夾，請瀏覽至這個目錄中找到已完成，並展開 notebook:**作法-要-使用-azureml > 訓練 > train-hyperparameter-tune-deploy-with-sklearn**資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure Machine Learning 適用於 Python 的 SDK](setup-create-workspace.md#sdk)
    - [建立工作區的設定檔](setup-create-workspace.md#write-a-configuration-file)
    - [下載範例指令碼檔案](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - 您也可以找到已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb)本指南的 GitHub 範例頁面。 此 notebook 還包含涵蓋智慧型的超參數微調和主要的計量中擷取最佳的模型區段。

## <a name="set-up-the-experiment"></a>設定實驗

此區段會設定載入所需的 python 套件、 初始化工作區、 建立實驗，並上傳的訓練資料和訓練指令碼的訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure 機器學習服務工作區](concept-workspace.md)是服務的最上層資源。 它會讓您提供 使用您所建立的所有成品的集中式位置。 在 Python SDK 中，您可以藉由建立存取工作區成品[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件。

建立工作區的物件，從`config.json`中建立的檔案[必要條件 > 一節](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗和資料夾以保存您的訓練指令碼。 在此範例中，建立稱為 「 sklearn 鳶尾花 」 實驗。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>上傳資料集和指令碼

[資料存放區](how-to-access-data.md)是可以儲存和存取掛接，或將資料複製到計算目標資料的位置。 每個工作區提供的預設資料存放區。 將上傳的資料和訓練指令碼至資料存放區，以便他們可以輕鬆地在定型期間存取。

1. 建立您的資料目錄。

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. 將鳶尾花資料集上傳到預設資料存放區。

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. 上傳 scikit-learn 定型指令碼， `train_iris.py`。

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>建立或取得計算目標

建立您 scikit-learn 的作業上執行的計算目標。 Scikit-learn 等最了解僅支援單一節點，CPU 運算。

下列程式碼中，建立您的遠端定型的計算資源的受管理的 Azure Machine Learning 計算 (AmlCompute)。 建立的 AmlCompute 需要大約 5 分鐘的時間。 如果 AmlCompute 具有該名稱已在您的工作區中，此程式碼會略過建立程序。

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

如需有關計算目標的詳細資訊，請參閱[什麼是計算目標](concept-compute-target.md)文章。

## <a name="create-a-scikit-learn-estimator"></a>建立 scikit-learn 估計工具

[Scikit-learn 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供簡單的方式啟動 scikit-learn 的計算目標上的訓練作業。 它透過實作[ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)類別，可用來支援單一節點的 CPU 訓練。

如果您的訓練指令碼需要其他的 pip 或 conda 套件執行，您可以藉由傳遞它們的名稱，透過產生的 docker 映像上安裝的套件`pip_packages`和`conda_packages`引數。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>提交執行

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)作業執行時，並完成後，提供介面，以執行歷程記錄。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

執行會執行，它會經歷下列階段：

- **正在準備**:Docker 映像會根據 TensorFlow 估計工具。 映像已上傳至工作區的容器登錄，並更新版本執行快取。 記錄檔也會串流處理至執行歷程記錄，您可以檢視來監視進度。

- **調整**：叢集會嘗試相應增加，如果 Batch AI 叢集需要更多的節點比目前可用來執行的執行。

- **Running**：在 [指令碼] 資料夾中的所有指令碼會上傳到計算目標、 資料存放區會掛接，或複製，並執行 entry_script。 從 stdout 的輸出和。 / logs 資料夾會串流處理至執行歷程記錄，而且可用來監視執行。

- **後置處理**：。 / 執行中的資料夾複製到執行歷程記錄會輸出。

## <a name="save-and-register-the-model"></a>儲存並註冊模型

一旦您已在定型模型，您可以儲存，並向您的工作區。 模型註冊可讓您存放區和版本來簡化您的工作區中的模型[模型管理和部署](concept-model-management-and-deployment.md)。

定型指令碼，train_iris.py，儲存模型中加入下列程式碼。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

為下列程式碼中註冊您的工作區的模型。

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>後續步驟

在本文中，您可以接受訓練，並且註冊 Azure Machine Learning 服務的 scikit-learn 模型。

* 若要了解如何部署模型，繼續前往我們[模型部署](how-to-deploy-and-where.md)文章。

* [調整超參數](how-to-tune-hyperparameters.md)

* [追蹤定型期間的執行計量](how-to-track-experiments.md)