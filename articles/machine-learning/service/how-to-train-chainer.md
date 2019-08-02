---
title: 定型和註冊 Chainer 模型
titleSuffix: Azure Machine Learning service
description: 本文說明如何使用 Azure Machine Learning 服務來定型和註冊 Chainer 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 7cf5650708cd951e872e3df6ea533a62bde0389d
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618336"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務大規模定型和註冊 Chainer 模型

本文說明如何使用 Azure Machine Learning 服務來定型和註冊 Chainer 模型。 它會使用熱門的[MNIST 資料集](http://yann.lecun.com/exdb/mnist/), 利用以[numpy](https://www.numpy.org/)上執行的[Chainer Python 程式庫](https://Chainer.org)所建立的深度類神經網路 (DNN) 來分類手寫數位。

Chainer 是高階類神經網路 API, 能夠在其他熱門的 DNN 架構上執行, 以簡化開發工作。 使用 Azure Machine Learning 服務, 您可以使用彈性雲端計算資源, 快速地相應放大定型作業。 您也可以追蹤定型回合、版本模型、部署模型, 以及其他更多功能。

無論您是從頭開始開發 Chainer 模型, 或是將現有的模型帶入雲端, Azure Machine Learning 服務都能協助您建立符合生產需求的模型。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先決條件

在下列任一環境中執行此程式碼:

- Azure Machine Learning 筆記本 VM-不需要下載或安裝

    - 完成以[雲端為基礎的筆記本快速入門](quickstart-run-cloud-notebook.md), 以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [samples] 資料夾中, 尋找已完成的筆記本和 chainer 的使用方式/訓練-**具有深度學習/定型-超參數-微調-部署-with-** 資料夾中的檔案。  此筆記本包含擴充的章節, 涵蓋智慧型超參數微調、模型部署和筆記本 widget。

- 您自己的 Jupyter Notebook 伺服器

    - [安裝適用于 Python 的 Azure Machine Learning SDK](setup-create-workspace.md#sdk)
    - [建立工作區設定檔](setup-create-workspace.md#write-a-configuration-file)
    - 下載範例指令檔[chainer_mnist. .py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - 您也可以在 GitHub 範例頁面上找到本指南的完整[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)。 此筆記本包含擴充的章節, 涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節會藉由載入所需的 python 套件、將工作區初始化、建立實驗, 以及上傳定型資料和定型腳本, 來設定定型實驗。

### <a name="import-packages"></a>匯入套件

首先, 匯入 azureml. core Python 程式庫並顯示版本號碼。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 服務工作區](concept-workspace.md)是服務的最上層資源。 它可為您提供一個集中的位置, 以處理您建立的所有成品。 在 Python SDK 中, 您可以藉由建立[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件來存取工作區構件。

藉由讀取`config.json`在[必要條件一節](#prerequisites)中建立的檔案來建立工作區物件:

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>建立專案目錄
建立一個目錄, 其中包含您的本機電腦上需要存取遠端資源的所有必要程式碼。 這包括定型腳本和定型腳本所依賴的任何其他檔案。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>準備訓練腳本

在本教學課程中, 已為您提供訓練腳本**chainer_mnist. .py** 。 在實務上, 您應該能夠採用任何自訂訓練腳本, 並使用 Azure ML 加以執行, 而不需要修改您的程式碼。

若要使用 Azure ML 的追蹤和計量功能, 請在您的定型腳本內新增少量的 Azure ML 程式碼。  訓練腳本**chainer_mnist**會示範如何使用腳本中的`Run`物件, 將一些計量記錄到您的 Azure ML 執行。

提供的定型腳本會使用 chainer `datasets.mnist.get_mnist`函數中的範例資料。  針對您自己的資料, 您可能需要使用[上傳資料集和腳本](how-to-train-keras.md#upload-dataset-and-scripts)等步驟, 讓資料可在定型期間使用。

將訓練腳本**chainer_mnist**複製到您的專案目錄。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗。 在此範例中, 建立名為 "chainer-mnist" 的實驗。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>建立或取得計算目標

您需要用來定型模型的[計算目標](concept-compute-target.md)。 在此範例中, 您會將 Azure ML 受控計算 (AmlCompute) 用於您的遠端訓練計算資源。

**建立 AmlCompute 大約需要5分鐘的時間**。 如果具有該名稱的 AmlCompute 已經在您的工作區中, 此程式碼會略過建立進程。  

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

如需計算目標的詳細資訊, 請參閱[什麼是計算目標一](concept-compute-target.md)文。

## <a name="create-a-chainer-estimator"></a>建立 Chainer 估計工具

[Chainer 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)提供簡單的方法, 讓您在計算目標上啟動 Chainer 訓練作業。

Chainer 估計工具是透過泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別來執行, 它可以用來支援任何架構。 如需使用泛型估計工具定型模型的詳細資訊, 請參閱[使用估計工具以 Azure Machine Learning 定型模型](how-to-train-ml-models.md)

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

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)會在作業執行時和完成後, 提供介面給執行歷程記錄。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行時, 它會經歷下列階段:

- **準備**:Docker 映射是根據 Chainer 估計工具建立的。 映射會上傳至工作區的容器登錄, 並快取以供稍後執行。 記錄也會串流處理至執行歷程記錄, 並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要執行比目前可用的更多節點, 則叢集會嘗試相應增加。

- **Running**：腳本資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區, 以及執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄, 並可用來監視執行。

- **後置處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="save-and-register-the-model"></a>儲存並註冊模型

定型模型之後, 您可以將它儲存並註冊到您的工作區。 模型註冊可讓您在工作區中儲存模型並為其建立版本, 以簡化[模型管理和部署](concept-model-management-and-deployment.md)。


在模型定型完成之後, 使用下列程式碼將模型註冊到您的工作區。  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> 如果您收到錯誤, 指出找不到模型, 請提供一分鐘, 然後再試一次。  在定型回合結束與輸出目錄中的模型可用性之間, 有時會稍微延遲一段時間。

您也可以下載模型的本機複本。 這適用于在本機執行額外的模型驗證工作。 在定型腳本`chainer_mnist.py`中, 「保護」物件會將模型保存到本機資料夾 (計算目標的本機)。 您可以使用執行物件從資料存放區下載複本。

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

在本文中, 您已在 Azure Machine Learning 服務上訓練了 Chainer 模型。 

* 若要瞭解如何部署模型, 請繼續進行我們的[模型部署](how-to-deploy-and-where.md)一文。

* [調整超參數](how-to-tune-hyperparameters.md)

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
