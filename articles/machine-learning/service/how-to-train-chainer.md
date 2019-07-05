---
title: 訓練及註冊 Chainer 模型
titleSuffix: Azure Machine Learning service
description: 這篇文章會示範如何訓練並註冊 Chainer 模型使用 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488663"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>定型，並向 Azure Machine Learning 服務的規模的 Chainer 模型

這篇文章會示範如何訓練並註冊 Chainer 模型使用 Azure Machine Learning 服務。 它會使用熱門[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)分類使用深度類神經網路 (DNN) 使用所建置的手寫數字[Chainer Python 程式庫](https://Chainer.org)之上執行[numpy](https://www.numpy.org/)。

Chainer 是高層級類神經網路頂端的其他熱門 DNN 架構上執行的 API，來簡化開發。 使用 Azure Machine Learning 服務時，您可以快速擴充使用彈性的雲端計算資源的教育訓練作業。 您也可以追蹤您的定型執行、 版本模型部署的模型，以及其他等等。

不論您正在開發從頭 Chainer 模型，或是您要將現有的模型帶到雲端，Azure Machine Learning 服務可協助您建置可實際執行的模型。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必要條件

在這兩個環境上執行此程式碼：

- Azure Machine Learning Notebook VM-不需要下載或必要的安裝

    - 完成[雲端為基礎的 notebook 快速入門](quickstart-run-cloud-notebook.md)建立專用的 notebook 伺服器 SDK 與範例存放庫中預先載入。
    - 在 notebook 伺服器上的 samples 資料夾中，找到完整的 notebook 和中的檔案**how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer**資料夾。  此 notebook 還包含擴充的各節涵蓋智慧型的超參數微調、 模型部署和 notebook widget。

- 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure Machine Learning 適用於 Python 的 SDK](setup-create-workspace.md#sdk)
    - [建立工作區的設定檔](setup-create-workspace.md#write-a-configuration-file)
    - 下載範例指令碼檔案[chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - 您也可以找到已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)GitHub 範例頁面本指南。 此 notebook 還包含擴充的各節涵蓋智慧型的超參數微調、 模型部署和 notebook widget。

## <a name="set-up-the-experiment"></a>設定實驗

此區段會設定載入所需的 python 套件、 初始化工作區、 建立實驗，並上傳的訓練資料和訓練指令碼的訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入 azureml.core Python 程式庫 ad 顯示版本號碼。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure 機器學習服務工作區](concept-workspace.md)是服務的最上層資源。 它會讓您提供 使用您所建立的所有成品的集中式位置。 在 Python SDK 中，您可以藉由建立存取工作區成品[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件。

建立工作區的物件，從`config.json`中建立的檔案[必要條件 > 一節](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>建立專案目錄
建立目錄，並將包含所有必要的程式碼從本機電腦，您必須對遠端資源的存取權。 這包括定型指令碼和您的訓練指令碼相依於任何其他檔案。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>準備訓練指令碼

在本教學課程，訓練指令碼**chainer_mnist.py**已經提供給您。 在實務上，您應該能夠採用任何自訂訓練指令碼，並使用 Azure ML 執行而不需要修改您的程式碼。

若要使用 Azure ML 的追蹤和計量功能，您必須新增少量的 Azure ML 訓練指令碼內的程式碼。  定型指令碼**chainer_mnist.py**示範如何將記錄到您的 Azure ML 執行的某些度量資訊。 若要這樣做，您可以存取 Azure ML`Run`指令碼內的物件。

複製定型指令碼**chainer_mnist.py**到您的專案目錄。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗和資料夾以保存您的訓練指令碼。 在此範例中，建立稱為"chainer mnist"實驗。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>建立或取得計算目標

您必須[計算目標](concept-compute-target.md)來定型模型。 在本教學課程中，您將使用遠端定型計算資源的受管理的 Azure ML 計算 (AmlCompute)。

**建立的 AmlCompute 大約需要 5 分鐘**。 如果 AmlCompute 具有該名稱已在您的工作區中，此程式碼會略過建立程序。  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

如需有關計算目標的詳細資訊，請參閱[什麼是計算目標](concept-compute-target.md)文章。

## <a name="create-a-chainer-estimator"></a>建立 Chainer 估計工具

[Chainer 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)提供簡單的方式啟動您的計算目標上的 Chainer 訓練作業。

Chainer 估算器透過泛型實作[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別，可用來支援任何架構。 如需有關訓練模型使用的泛型的估計工具的詳細資訊，請參閱[定型的模型，使用 Azure Machine Learning 使用估計工具](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>提交執行

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)作業執行時，並完成後，提供介面，以執行歷程記錄。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行會執行，它會經歷下列階段：

- **正在準備**:Docker 映像會根據 Chainer 估計工具。 映像已上傳至工作區的容器登錄，並更新版本執行快取。 記錄檔也會串流處理至執行歷程記錄，您可以檢視來監視進度。

- **調整**：叢集會嘗試相應增加，如果 Batch AI 叢集需要更多的節點比目前可用來執行的執行。

- **Running**：在 [指令碼] 資料夾中的所有指令碼會上傳到計算目標、 資料存放區會掛接，或複製，並執行 entry_script。 從 stdout 的輸出和。 / logs 資料夾會串流處理至執行歷程記錄，而且可用來監視執行。

- **後置處理**：。 / 執行中的資料夾複製到執行歷程記錄會輸出。

## <a name="save-and-register-the-model"></a>儲存並註冊模型

一旦您已在定型模型，您可以儲存，並向您的工作區。 模型註冊可讓您存放區和版本來簡化您的工作區中的模型[模型管理和部署](concept-model-management-and-deployment.md)。

將下列程式碼新增至您的訓練指令碼， **chainer_mnist.py**、 儲存模型。 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

為下列程式碼中註冊您的工作區的模型。

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>後續步驟

在本文中，您訓練 Azure Machine Learning 服務的 Chainer 模型。 

* 若要了解如何部署模型，繼續前往我們[模型部署](how-to-deploy-and-where.md)文章。

* [調整超參數](how-to-tune-hyperparameters.md)

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
