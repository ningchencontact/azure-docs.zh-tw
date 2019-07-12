---
title: 訓練及註冊 TensorFlow 上執行的 Keras 模型
titleSuffix: Azure Machine Learning service
description: 這篇文章說明您如何訓練並註冊在 TensorFlow Keras 模型使用 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840056"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>定型，並向 Azure Machine Learning 服務的規模的 Keras 模型

這篇文章示範如何訓練及註冊 Keras 所建立的模型在 TensorFlow 上使用 Azure Machine Learning 服務。 它會使用熱門[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)分類使用深度類神經網路 (DNN) 使用所建置的手寫數字[Keras Python 程式庫](https://keras.io)之上執行[TensorFlow](https://www.tensorflow.org/overview).

Keras 是高層級類神經網路能夠執行其他熱門 DNN 架構的頂端的 API，來簡化開發。 使用 Azure Machine Learning 服務時，您可以快速擴充使用彈性的雲端計算資源的教育訓練作業。 您也可以追蹤您的定型執行、 版本模型部署的模型，以及其他等等。

不論您正在開發從頭 Keras 模型，或是您要將現有的模型帶到雲端，Azure Machine Learning 服務可協助您建置可實際執行的模型。

## <a name="prerequisites"></a>先決條件

在這兩個環境上執行此程式碼：

 - Azure Machine Learning Notebook VM-不需要下載或必要的安裝

     - 完成[雲端為基礎的 notebook 快速入門](quickstart-run-cloud-notebook.md)建立專用的 notebook 伺服器 SDK 與範例存放庫中預先載入。
    - 在 notebook 伺服器上的 [samples] 資料夾，請瀏覽至這個目錄中找到已完成，並展開 notebook:**作法-要-使用-azureml > 訓練與深度學習 > train-hyperparameter-tune-deploy-with-keras**資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

     - [安裝 Azure Machine Learning 適用於 Python 的 SDK](setup-create-workspace.md#sdk)
    - [建立工作區的設定檔](setup-create-workspace.md#write-a-configuration-file)
    - [下載範例指令碼檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)`mnist-keras.py`和 `utils.py`
     
    您也可以找到已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)本指南的 GitHub 範例頁面。 此 notebook 還包含擴充的各節涵蓋智慧型的超參數微調、 模型部署和 notebook widget。

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

建立實驗和資料夾以保存您的訓練指令碼。 在此範例中，建立稱為"keras mnist"實驗。

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>上傳資料集和指令碼

[資料存放區](how-to-access-data.md)是可以儲存和存取掛接，或將資料複製到計算目標資料的位置。 每個工作區提供的預設資料存放區。 將上傳的資料和訓練指令碼至資料存放區，以便他們可以輕鬆地在定型期間存取。

1. 下載在本機的 MNIST 資料集。

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. 將 MNIST 資料集上傳到預設資料存放區。

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Keras 訓練指令碼中上, 傳`keras_mnist.py`，並協助程式檔案， `utils.py`。

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>建立計算目標

建立計算目標上執行 TensorFlow 作業。 在此範例中，建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

如需有關計算目標的詳細資訊，請參閱[什麼是計算目標](concept-compute-target.md)文章。

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>建立 TensorFlow 估算器並匯入 Keras

[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供簡單的方式啟動 TensorFlow 訓練作業，在計算目標上。 因為 Keras 是在 TensorFlow 之上執行，您可以使用 TensorFlow 估計工具，並匯入 Keras 程式庫使用`pip_packages`引數。

TensorFlow 估算器透過泛型實作[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別，可用來支援任何架構。 如需有關訓練模型使用的泛型的估計工具的詳細資訊，請參閱[定型的模型，使用 Azure Machine Learning 使用估計工具](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>提交執行

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)作業執行時，並完成後，提供介面，以執行歷程記錄。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行會執行，它會經歷下列階段：

- **正在準備**:Docker 映像會根據 TensorFlow 估計工具。 映像已上傳至工作區的容器登錄，並更新版本執行快取。 記錄檔也會串流處理至執行歷程記錄，您可以檢視來監視進度。

- **調整**：叢集會嘗試相應增加，如果 Batch AI 叢集需要更多的節點比目前可用來執行的執行。

- **Running**：在 [指令碼] 資料夾中的所有指令碼會上傳到計算目標、 資料存放區會掛接，或複製，並執行 entry_script。 從 stdout 的輸出和。 / logs 資料夾會串流處理至執行歷程記錄，而且可用來監視執行。

- **後置處理**：。 / 執行中的資料夾複製到執行歷程記錄會輸出。

## <a name="register-the-model"></a>註冊模型

一旦您已在定型模型，可以將其註冊到您的工作區中。 模型註冊可讓您存放區和版本來簡化您的工作區中的模型[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

您也可以下載此模型的本機副本。 這可以是可用來執行本機其他模型的驗證工作項目。 定型指令碼，在`mnist-keras.py`，TensorFlow 保護裝置物件保存至本機資料夾 （本機的計算目標） 模型。 若要從資料存放區下載複本，您可以使用執行物件。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>後續步驟

在本文中，您可以接受訓練，並且註冊 Azure Machine Learning 服務的 Keras 模型。 若要了解如何部署模型，請繼續閱讀我們的模型部署文件。

> [!div class="nextstepaction"]
> [如何及在何處部署模型](how-to-deploy-and-where.md)
