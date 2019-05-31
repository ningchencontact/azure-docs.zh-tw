---
title: 訓練及註冊 TensorFlow 模型
titleSuffix: Azure Machine Learning service
description: 這篇文章會示範如何訓練及註冊 Azure Machine Learning 服務在 TensorFlow 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: 314917ce91407206d786b191df118893696ac82c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417137"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>定型，並向 Azure Machine Learning 服務的規模 TensorFlow 模型

這篇文章會示範如何訓練及註冊 Azure Machine Learning 服務在 TensorFlow 模型。 我們將使用熱門[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)來分類使用 TensorFlow 上建置的深度類神經網路的手寫數字。

使用 Azure Machine Learning 服務時，您將能夠快速相應放大您使用彈性的雲端計算資源的開放原始碼訓練作業。 您也可以追蹤您的定型執行、 版本模型、 部署模型，以及其他更多。 

不論您正在開發從頭 TensorFlow 模型，或是您要將現有的模型帶到雲端，您可以建置使用 Azure Machine Learning 服務的生產就緒模型。

## <a name="prerequisites"></a>必要條件

- 安裝適用於 Python 的 Azure Machine Learning SDK
- 選用：建立工作區組態檔
- 下載[範例指令碼檔案](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)`mnist-tf.py`和 `utils.py`

您可以依照[Python SDK 安裝手冊 》](setup-create-workspace.md#sdk)如需有關設定您的環境的逐步指示。 範例訓練檔案都位於我們[範例的 GitHub 頁面](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)與本指南的擴充，Juypter Notebook 版本。

## <a name="set-up-the-experiment"></a>設定實驗

### <a name="import-packages"></a>匯入套件

首先，我們必須匯入必要的 Python 程式庫。

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

Workspace 物件是服務的最上層資源。 它會讓您提供 使用您所建立的所有成品的集中式位置。

如果您已完成的選擇性步驟[必要條件 > 一節](#prerequisites)，您可以使用`Workspace.from_config()`從儲存在組態檔的詳細資料的 快速建立工作區的物件。

```Python
ws = Workspace.from_config()
```

您可以也可以建立工作區明確。

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗和資料夾以保存您的訓練指令碼。 在此範例中，建立稱為"tf mnist"實驗

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>上傳資料集和指令碼

[資料存放區](how-to-access-data.md)是可以儲存和存取掛接，或將資料複製到計算目標資料的位置。 每個工作區提供的預設資料存放區。 我們將上傳我們的資料和訓練指令碼，以便他們可以輕鬆地在定型期間存取。

1. 下載在本機的 MNIST 資料集

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

1. TensorFlow 訓練指令碼中上, 傳`tf_mnist.py`，並協助程式檔案， `utils.py`。

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>建立計算目標

建立計算目標上執行 TensorFlow 作業。 在此範例中，我們會建立已啟用 GPU 功能 AmlCompute 叢集。 取得一份可用的訓練計算目標，請參閱[這篇文章](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>建立 TensorFlow 估計工具

[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供簡單的方式啟動 TensorFlow 訓練工作的計算目標上。 它會自動提供具有 TensorFlow 安裝的 docker 映像。

您也可以將透過其名稱傳遞產生的 docker 映像中包含其他的 pip 或 conda 套件`pip_packages`和`conda_packages`引數。

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='tf_mnist.py',
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

- **調整**：叢集會嘗試相應增加如果 Batch AI 叢集需要更多的節點比目前可用來執行的執行。

- **Running**：在 [指令碼] 資料夾中的所有指令碼會上傳到計算目標、 資料存放區會掛接，或複製，並執行 entry_script。 從 stdout 的輸出和。 / logs 資料夾會串流處理至執行歷程記錄，而且可用來監視執行。

- **後置處理**：。 / 執行中的資料夾複製到執行歷程記錄會輸出。

## <a name="register-or-download-a-model"></a>註冊，或下載模型

一旦您已在定型模型，可以將其註冊到您的工作區中。 模型註冊可讓您存放區和版本來簡化您的工作區中的模型[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

您也可以使用執行物件，以下載模型的本機副本。 定型指令碼中`mnist-tf.py`，TensorFlow 保護裝置物件保存至本機資料夾 （本機的計算目標） 模型。 若要下載，我們可以使用執行物件。

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

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估計工具也支援分散式的訓練多個 CPU 和 GPU 叢集。 您可以輕鬆執行分散式的 TensorFlow 作業和 Azure Machine Learning 服務會管理您的協調流程。

Azure Machine Learning 服務支援在 TensorFlow 的分散式訓練的兩個方法：

* [MPI 型](https://www.open-mpi.org/)分散式訓練使用[Horovod](https://github.com/uber/horovod) framework
* 原生[分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)使用參數伺服器方法

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) Uber 所開發的分散式的訓練是一種開放原始碼架構。 它提供分散式 GPU TensorFlow 作業的簡單路徑。

若要使用 Horovod，指定`mpi`針對`distributed_training`TensorFlow 估算器建構函式中的參數。 您可以使用訓練指令碼中，將會安裝 Horovod。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',
                      use_gpu=True)
```

### <a name="parameter-server"></a>參數伺服器

您也可以執行[原生分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，其會使用參數伺服器模型。 在此方法中，您會在參數伺服器與工作者的整個叢集中進行訓練。 工作者會在訓練期間會計算升降度，而參數伺服器會彙總升降度。

若要使用參數伺服器方法，指定`ps`針對`distributed_training`TensorFlow 估算器建構函式中的參數。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='ps',
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>注意 `TF_CONFIG`

您還需要的網路位址和連接埠的叢集[ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)，因此 Azure Machine Learning 設定`TF_CONFIG`為您的環境變數。

`TF_CONFIG` 環境變數為 JSON 字串。 以下為參數伺服器變數的範例：

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

用於 TensorFlow 的高層級[ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API，TensorFlow 會剖析這`TF_CONFIG`變數和建置叢集為您的規格。

適用於 TensorFlow 的較低層級 core Api 進行訓練，剖析`TF_CONFIG`變數和建置`tf.train.ClusterSpec`您訓練程式碼中。 在[本範例](https://aka.ms/aml-notebook-tf-ps)中，**訓練指令碼**即需如此處理，如下所示：

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>後續步驟

在本文中，您可以接受訓練，並且註冊 Azure Machine Learning 服務的 TensorFlow 模型。 若要了解如何部署模型，請繼續閱讀我們的模型部署文件。

> [!div class="nextstepaction"]
> [如何及在何處部署模型](how-to-deploy-and-where.md)
