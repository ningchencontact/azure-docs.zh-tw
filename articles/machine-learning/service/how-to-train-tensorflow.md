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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d5751ab96dc6b44229e2b18b832a570930058ca
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442341"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>定型 TensorFlow 和 Keras 的模型，使用 Azure Machine Learning 服務

您可以輕鬆在 Azure 計算上執行 TensorFlow 訓練作業使用[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估算器類別，在 Azure 機器學習服務 SDK。 `TensorFlow` Estimator 會指示 Azure Machine Learning 服務來訓練深度類神經網路 (DNN) 在 TensorFlow 啟用容器上執行您的作業。

`TensorFlow`估計工具也會提供一個抽象層執行，這表示，您可以輕易而不需要變更您的訓練指令碼設定在不同的計算目標上的參數化的執行。

## <a name="get-started"></a>開始使用

由於`TensorFlow`估算器類別是基底類似[ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)，我們建議您第一次讀取[基底的評估工具操作說明文章](how-to-train-ml-models.md)了解為名的概念。

若要開始使用 Azure Machine Learning 服務[完成此快速入門](quickstart-run-cloud-notebook.md)。 完成之後，就會看到[Azure Machine Learning 工作區](concept-azure-machine-learning-architecture.md#workspace)及其所有我們[範例 notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml)包括用於定型 TensorFlow 與 Keras 的 Dnn。

## <a name="single-node-training"></a>單一節點定型

若要執行 TensorFlow 作業，請具現化[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)物件，並將其當作實驗提交。

下列程式碼會具現化 TensorFlow 估計工具，並將它提交做一個試驗。 定型指令碼`train.py`會使用指定的指令碼參數來執行。 會執行上已啟用 GPU 功能的作業[計算目標](how-to-set-up-training-targets.md)，和 scikit-learn-了解將會安裝為相依性`train.py`。

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>分散式定型

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估計工具也支援分散式的訓練多個 CPU 和 GPU 叢集。 您可以輕鬆執行分散式的 TensorFlow 作業和 Azure Machine Learning 服務會管理基礎結構和為您的協調流程。

Azure Machine Learning 服務支援在 TensorFlow 的分散式訓練的兩個方法：

* [MPI 型](https://www.open-mpi.org/)分散式訓練使用[Horovod](https://github.com/uber/horovod) framework
* 原生[分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)使用參數伺服器方法

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) Uber 所開發的分散式的訓練是一種開放原始碼架構。 它提供分散式 GPU TensorFlow 作業的簡單路徑。

下列範例會執行 Horovod 使用兩個背景工作分散到兩個節點的分散式的訓練作業。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod 和其相依性將會安裝，因此您可以將它匯入您的訓練指令碼。

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>參數伺服器

您也可以執行[原生分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，其會使用參數伺服器模型。 在此方法中，您會在參數伺服器與工作者的整個叢集中進行訓練。 工作者會在訓練期間會計算升降度，而參數伺服器會彙總升降度。

下列範例會執行分散式的訓練作業分散到兩個節點的四個背景工作角色中使用參數伺服器方法。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>注意 `TF_CONFIG`

您還需要的網路位址和連接埠的叢集[ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)，因此 Azure Machine Learning 設定`TF_CONFIG`為您的環境變數。

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

## <a name="keras-support"></a>Keras 支援

[Keras](https://keras.io/)是受歡迎的高層級 DNN Python API，支援 TensorFlow、 CNTK、 和 Theano 作為後端。 如果您使用 TensorFlow 作為後端，新增 Keras 很簡單，只要包括`pip_package`建構函式參數。

下列範例會具現化[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估算器並將它提交做一個試驗。 估計工具執行 Keras 定型指令碼`keras_train.py`。 會執行上已啟用 gpu 功能的作業[計算目標](how-to-set-up-training-targets.md)keras 做為相依性，透過 pip 安裝。

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>匯出至 ONNX

若要取得與最佳化的推斷[ONNX Runtime](concept-onnx.md)，您可以將您的定型的 TensorFlow 模型轉換成 ONNX 格式。 查看[範例](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py)。

## <a name="examples"></a>範例

您可以使用各種架構上的單一節點和分散式 TensorFlow 執行找到實用的程式碼範例[我們的 GitHub 頁面](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)。

## <a name="next-steps"></a>後續步驟

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
