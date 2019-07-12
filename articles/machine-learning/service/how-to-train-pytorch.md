---
title: 訓練及註冊 PyTorch 模型
titleSuffix: Azure Machine Learning service
description: 這篇文章會示範如何訓練並註冊 PyTorch 模型使用 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840090"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>定型，並向 Azure Machine Learning 服務的規模 PyTorch 模型

這篇文章會示範如何訓練並註冊 PyTorch 模型使用 Azure Machine Learning 服務。 它根據[PyTorch 的遷移學習教學課程](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)，建置影像 chickens 和火雞深度類神經網路 (DNN) 分類器。

[PyTorch](https://pytorch.org/)是一個開放原始碼運算架構，通常用來建立深度類神經網路 (DNN)。 使用 Azure Machine Learning 服務時，您可以快速擴充使用彈性的雲端計算資源的開放原始碼訓練作業。 您也可以追蹤您的定型執行、 版本模型部署的模型，以及其他等等。

不論您正在開發從頭 PyTorch 模型，或是您要將現有的模型帶到雲端，Azure Machine Learning 服務可協助您建置可實際執行的模型。

## <a name="prerequisites"></a>必要條件

在這兩個環境上執行此程式碼：

 - Azure Machine Learning Notebook VM-不需要下載或必要的安裝

    - 完成[雲端為基礎的 notebook 快速入門](quickstart-run-cloud-notebook.md)建立專用的 notebook 伺服器 SDK 與範例存放庫中預先載入。
    - 在 notebook 伺服器上的 [samples] 資料夾，請瀏覽至這個目錄中找到已完成，並展開 notebook:**作法-要-使用-azureml > 訓練與深度學習 > train-hyperparameter-tune-deploy-with-pytorch**資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure Machine Learning 適用於 Python 的 SDK](setup-create-workspace.md#sdk)
    - [建立工作區的設定檔](setup-create-workspace.md#write-a-configuration-file)
    - [下載範例指令碼檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    您也可以找到已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)本指南的 GitHub 範例頁面。 此 notebook 還包含擴充的各節涵蓋智慧型的超參數微調、 模型部署和 notebook widget。

## <a name="set-up-the-experiment"></a>設定實驗

此區段會設定載入所需的 python 套件、 初始化工作區、 建立實驗，並上傳的訓練資料和訓練指令碼的訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure 機器學習服務工作區](concept-workspace.md)是服務的最上層資源。 它會讓您提供 使用您所建立的所有成品的集中式位置。 在 Python SDK 中，您可以藉由建立存取工作區成品[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件。

建立工作區的物件，從`config.json`中建立的檔案[必要條件 > 一節](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗和資料夾以保存您的訓練指令碼。 在此範例中，建立稱為 「 pytorch 鳥"實驗。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>取得資料

資料集是由約 120 訓練和映像每個火雞 chickens，100 驗證映像，每個類別所組成。 我們將會下載並擷取資料集做為我們的訓練指令碼的一部分`pytorch_train.py`。 映像都是的子集[開啟的映像 v5 資料集](https://storage.googleapis.com/openimages/web/index.html)。

### <a name="prepare-training-scripts"></a>準備訓練指令碼

在本教學課程，訓練指令碼`pytorch_train.py`，已提供。 在實務上，您可以採用，任何自訂訓練指令碼，並執行使用 Azure Machine Learning 服務。

Pytorch 訓練指令碼中上, 傳`pytorch_train.py`。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

不過，如果您想要使用 Azure Machine Learning 服務的追蹤和計量功能，您必須新增的少量程式碼，在訓練指令碼。 追蹤的計量的範例可在`pytorch_train.py`。

## <a name="create-a-compute-target"></a>建立計算目標

建立您的 PyTorch 作業上執行的計算目標。 在此範例中，建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

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

## <a name="create-a-pytorch-estimator"></a>建立 PyTorch 估計工具

[PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)提供簡單的方式啟動計算目標上的 PyTorch 訓練作業。

PyTorch 估算器透過泛型實作[ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別，可用來支援任何架構。 如需有關訓練模型使用的泛型的估計工具的詳細資訊，請參閱[定型的模型，使用 Azure Machine Learning 使用估計工具](how-to-train-ml-models.md)

如果您的訓練指令碼需要其他的 pip 或 conda 套件執行，您可以藉由傳遞它們的名稱，透過產生的 docker 映像上安裝的套件`pip_packages`和`conda_packages`引數。

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>提交執行

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)作業執行時，並完成後，提供介面，以執行歷程記錄。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

執行會執行，它會經歷下列階段：

- **正在準備**:Docker 映像會根據 PyTorch 估計工具。 映像已上傳至工作區的容器登錄，並更新版本執行快取。 記錄檔也會串流處理至執行歷程記錄，您可以檢視來監視進度。

- **調整**：叢集會嘗試相應增加，如果 Batch AI 叢集需要更多的節點比目前可用來執行的執行。

- **Running**：在 [指令碼] 資料夾中的所有指令碼會上傳到計算目標、 資料存放區會掛接，或複製，並執行 entry_script。 從 stdout 的輸出和。 / logs 資料夾會串流處理至執行歷程記錄，而且可用來監視執行。

- **後置處理**：。 / 執行中的資料夾複製到執行歷程記錄會輸出。

## <a name="register-or-download-a-model"></a>註冊，或下載模型

一旦您已在定型模型，可以將其註冊到您的工作區中。 模型註冊可讓您存放區和版本來簡化您的工作區中的模型[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

您也可以使用執行物件，以下載模型的本機副本。 定型指令碼中`pytorch_train.py`，PyTorch 儲存物件保存至本機資料夾 （本機的計算目標） 模型。 若要下載，您可以使用執行物件。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>分散式定型

[ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)估計工具也支援分散式的訓練多個 CPU 和 GPU 叢集。 您可以輕鬆執行分散式的 PyTorch 作業和 Azure Machine Learning 服務會管理您的協調流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod)是的開放原始碼，全都會降低 framework，若是 Uber 所開發的分散式的訓練。 它提供分散式 GPU PyTorch 作業的簡單路徑。

若要使用 Horovod，指定[ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)物件`distributed_training`PyTorch 建構函式的參數。 此參數可確保 Horovod 程式庫會為您要用於訓練指令碼安裝。


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod 和其相依性將會安裝，因此您可以將它匯入您的訓練指令碼`train.py`，如下所示：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>匯出至 ONNX

若要最佳化與推斷[ONNX Runtime](concept-onnx.md)，將您的定型的 PyTorch 模型轉換成 ONNX 格式。 推斷，或模型計分，是階段已部署的模型用於預測，最常在實際執行資料的位置。 請參閱[教學課程](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)的範例。

## <a name="next-steps"></a>後續步驟

在本文中，您可以接受訓練，並且註冊 Azure Machine Learning 服務的 PyTorch 模型。 若要了解如何部署模型，請繼續閱讀我們的模型部署文件。

> [!div class="nextstepaction"]
> [如何及在何處部署模型](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
