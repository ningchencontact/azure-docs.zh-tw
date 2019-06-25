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
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: e1f587785b06f10bab42a425b0910d4f25fddf9f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165548"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>定型，並向 Azure Machine Learning 服務的規模 TensorFlow 模型

這篇文章會示範如何訓練及註冊 Azure Machine Learning 服務在 TensorFlow 模型。 它會使用熱門[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)分類手寫數字使用深度類神經網路使用建置[TensorFlow Python 程式庫](https://www.tensorflow.org/overview)。

TensorFlow 是開放原始碼運算架構，通常用來建立深度類神經網路 (DNN)。 使用 Azure Machine Learning 服務時，您可以快速擴充使用彈性的雲端計算資源的開放原始碼訓練作業。 您也可以追蹤您的定型執行、 版本模型部署的模型，以及其他等等。

不論您正在開發從頭 TensorFlow 模型，或是您要將現有的模型帶到雲端，Azure Machine Learning 服務可協助您建置可實際執行的模型。

## <a name="prerequisites"></a>必要條件

在這兩個環境上執行此程式碼：

 - Azure Machine Learning Notebook VM-不需要下載或必要的安裝

     - 完成[雲端為基礎的 notebook 快速入門](quickstart-run-cloud-notebook.md)建立專用的 notebook 伺服器 SDK 與範例存放庫中預先載入。
    - 在 notebook 伺服器上的 [samples] 資料夾，請瀏覽至這個目錄中找到已完成，並展開 notebook:**作法-要-使用-azureml > 訓練與深度學習 > train-hyperparameter-tune-deploy-with-tensorflow**資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

     - [安裝 Azure Machine Learning 適用於 Python 的 SDK](setup-create-workspace.md#sdk)
    - [建立工作區的設定檔](setup-create-workspace.md#write-a-configuration-file)
    - [下載範例指令碼檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)`mnist-tf.py`和 `utils.py`
     
    您也可以找到已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)本指南的 GitHub 範例頁面。 此 notebook 還包含擴充的各節涵蓋智慧型的超參數微調、 模型部署和 notebook widget。

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

建立實驗和資料夾以保存您的訓練指令碼。 在此範例中，建立稱為"tf mnist"實驗。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
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

1. TensorFlow 訓練指令碼中上, 傳`tf_mnist.py`，並協助程式檔案， `utils.py`。

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator"></a>建立 TensorFlow 估計工具

[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供簡單的方式啟動 TensorFlow 訓練工作的計算目標上。

TensorFlow 估算器透過泛型實作[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別，可用來支援任何架構。 如需有關訓練模型使用的泛型的估計工具的詳細資訊，請參閱[定型的模型，使用 Azure Machine Learning 使用估計工具](how-to-train-ml-models.md)

如果您的訓練指令碼需要其他的 pip 或 conda 套件執行，您可以藉由傳遞它們的名稱，透過產生的 docker 映像上安裝的套件`pip_packages`和`conda_packages`引數。

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
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

## <a name="register-or-download-a-model"></a>註冊，或下載模型

一旦您已在定型模型，可以將其註冊到您的工作區中。 模型註冊可讓您存放區和版本來簡化您的工作區中的模型[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

您也可以使用執行物件，以下載模型的本機副本。 定型指令碼中`mnist-tf.py`，TensorFlow 保護裝置物件保存至本機資料夾 （本機的計算目標） 模型。 若要下載，您可以使用執行物件。

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

- [MPI 型](https://www.open-mpi.org/)分散式訓練使用[Horovod](https://github.com/uber/horovod) framework
- 原生[分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)使用參數伺服器方法

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) Uber 所開發的分散式的訓練是一種開放原始碼架構。 它提供分散式 GPU TensorFlow 作業的簡單路徑。

若要使用 Horovod，指定[ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)物件`distributed_training`TensorFlow 建構函式的參數。 此參數可確保 Horovod 程式庫會為您要用於訓練指令碼安裝。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>參數伺服器

您也可以執行[原生分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，其會使用參數伺服器模型。 在此方法中，您會在參數伺服器與工作者的整個叢集中進行訓練。 工作者會在訓練期間會計算升降度，而參數伺服器會彙總升降度。

若要使用參數伺服器方法，指定[ `TensorflowConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py)物件`distributed_training`TensorFlow 建構函式的參數。

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>定義在 'TF_CONFIG' 中的叢集規格

您也需要網路位址和連接埠的叢集[ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)，因此 Azure Machine Learning 設定`TF_CONFIG`為您的環境變數。

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

用於 TensorFlow 的高層級[ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API，TensorFlow 剖析`TF_CONFIG`變數和叢集規格為您的組建。

適用於 TensorFlow 的較低層級 core Api 進行訓練，剖析`TF_CONFIG`變數和建置`tf.train.ClusterSpec`您訓練程式碼中。

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
