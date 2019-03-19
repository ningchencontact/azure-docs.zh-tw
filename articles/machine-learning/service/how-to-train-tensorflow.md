---
title: TensorFlow 與 Keras 的定型模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用來執行單一節點和分散式的 TensorFlow 和 Keras 模型定型 TensorFlow 和 Keras 估算
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/21/2019
ms.custom: seodec18
ms.openlocfilehash: b41098907f801f7dae839a470249834b02c8d519
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338547"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>定型 TensorFlow 和 Keras 的模型，使用 Azure Machine Learning 服務

為了能使用 TensorFlow 深度訓練類神經網路 (DNN)，Azure Machine Learning 提供了 `Estimator` 的自訂 `TensorFlow` 類別。 Azure SDK [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估計工具 (不到可使用 」 混為一談[ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator)類別) 可讓您輕鬆地提交 Azure 上執行的單一節點和分散式 TensorFlow 訓練作業計算。

## <a name="single-node-training"></a>單一節點定型
使用 `TensorFlow` 估算器來定型，類似於使用[基礎 `Estimator`](how-to-train-ml-models.md)，因此請先詳讀操作說明文章，並確定已了解文章中說明的概念。
  
若要執行 TensorFlow 作業，請具現化 `TensorFlow` 物件。 您應該已建立了自己的[計算目標](how-to-set-up-training-targets.md#amlcompute)物件`compute_target`。

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

我們在這裡會為 TensorFlow 建構函式指定以下參數：

參數 | 描述
--|--
`source_directory` | 包含定型作業所需之所有程式碼的本機目錄。 此資料夾是從您的本機電腦複製到遠端計算
`script_params` | 用於指定您的定型指令碼 `entry_script` 命令列引數的字典，格式為 <命令列引數, 值> 組
`compute_target` | 您的定型指令碼執行所在的遠端計算目標，在此案例中為 Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 叢集
`entry_script` | 要在遠端計算上執行之定型指令碼的檔案路徑 (相對於 `source_directory`)。 此檔案 (以及此檔案所相依的其他任何檔案) 都應位於此資料夾
`conda_packages` | 要透過 Conda 安裝的 Python 套件清單 (其中包含您的定型指令碼所需的套件)。 在此情況下，訓練指令碼會使用 `sklearn` 來載入資料，因此請指定安裝此套件。  建構函式有另一個名為 `pip_packages` 的參數，您可以視需要將此參數用於任何 pip 套件
`use_gpu` | 請將此旗標設定為 `True`，以利用 GPU 進行定型。 預設為 `False`。

由於您目前使用的是 TensorFlow 估算器，因此用於訓練的容器會預設納入 TensorFlow 套件，也會含有在 CPU 與 GPU 上進行訓練所需的相關相依性。

接著請提交 TensorFlow 作業：
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Keras 支援
[Keras](https://keras.io/)是熱門高階 DNN Python API，支援 TensorFlow、 CNTK、 或 Theano 作為後端。 如果您使用 TensorFlow 作為後端時，您可以輕鬆使用 TensFlow 估計工具來為 Keras 模型定型。 加入至它的 keras TensorFlow estimator 的範例如下：

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
上述的 TensorFlow 估算器建構函式會指示 Azure Machine Learning 服務，若要安裝 Keras，透過執行環境的 pip。 和您`keras_train.py`可以匯入至 Keras 模型定型的 Keras API。 如需完整的範例中，瀏覽[此 Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)。

## <a name="distributed-training"></a>分散式定型
TensorFlow 估算器也可讓您在 Azure VM 的 CPU 與 GPU 叢集上大規模訓練模型。 您可以使用少量 API 呼叫輕鬆地執行分散式 TensorFlow 訓練，而 Azure Machine Learning 會在背景管理執行這類工作負載所需的一切基礎結構和協調流程。

Azure Machine Learning 支援 TensorFlow 中兩種分散式訓練方法：
* 使用 [Horovod](https://github.com/uber/horovod) 架構的 MPI 型分散式訓練
* 透過參數伺服器方法的原生[分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) \(英文\) 是 Uber 開發的分散式定型專用開放原始碼 Ring-Allreduce 架構。

若要使用 Horovod 架構執行分散式 TensorFlow，請建立 TensorFlow 物件，如下所示：

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

上述程式碼會對 TensorFlow 建構函式公開以下新參數：

參數 | 描述 | 預設值
--|--|--
`node_count` | 用於定型作業的節點數目。 | `1`
`process_count_per_node` | 要在每個節點上執行的處理序 (或「背景工作角色」) 數目。|`1`
`distributed_backend` | 用於啟動分散式定型的後端，由估算器透過 MPI 提供。 如果您想要執行平行或分散式訓練 (例如`node_count`> 1 或`process_count_per_node`> 1 或這兩個) 利用 MPI （和 Horovod），設定`distributed_backend='mpi'`。 Azure Machine Learning 所使用的 MPI 實作是[開放式 MPI](https://www.open-mpi.org/) \(英文\)。 | `None`

上述範例將執行有兩個背景工作角色的分散式定型，每個節點都有一個背景工作角色。

Horovod 和其相依性將會安裝，因此您可以將它匯入您的訓練指令碼`train.py`，如下所示：

```Python
import tensorflow as tf
import horovod
```

最後請提交 TensorFlow 作業：
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>參數伺服器
您也可以執行[原生分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，其會使用參數伺服器模型。 在此方法中，您會在參數伺服器與工作者的整個叢集中進行訓練。 工作者會在訓練期間會計算升降度，而參數伺服器會彙總升降度。

建構 TensorFlow 物件：

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

請留意上述程式碼中 TensorFlow 建構函式的以下參數：

參數 | 描述 | 預設值
--|--|--
`worker_count` | 背景工作數目。 | `1`
`parameter_server_count` | 參數伺服器數目。 | `1`
`distributed_backend` | 用於分散式訓練的後端。 若要透過參數伺服器進行分散式訓練，請設定 `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>注意 `TF_CONFIG`
還需要 [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) 其叢集的網路位址與連接埠，如此 Azure Machine Learning 才能設定 `TF_CONFIG` 環境變數。

`TF_CONFIG` 環境變數為 JSON 字串。 以下為參數伺服器變數的範例：
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

如果您使用 TensorFlow 的高層級[ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API，TensorFlow 會剖析這`TF_CONFIG`變數和建置叢集為您的規格。 

如果改用 TensorFlow 較低層級的核心 API 進行訓練，則必須剖析 `TF_CONFIG` 變數，並自行在訓練程式碼中建置 `tf.train.ClusterSpec`。 在[本範例](https://aka.ms/aml-notebook-tf-ps)中，**訓練指令碼**即需如此處理，如下所示：

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

撰寫完訓練程式碼並建立 TensorFlow 物件後，即可提交您的訓練作業：
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>範例

探索各種[notebook 在 Github 上的分散式深入學習](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
