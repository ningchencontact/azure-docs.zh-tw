---
title: 使用 PyTorch 將模型定型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 PyTorch 估算器執行 PyTorch 的單一節點和分散式定型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 02463ba47a48f5e86055b9ecb4e0edbdf9608769
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024563"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務將 PyTorch 模型定型

對於使用 PyTorch 深度類神經網路 (DNN) 訓練，Azure Machine Learning 提供可自訂[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)類別的`Estimator`。 Azure SDK 的 `PyTorch` 估算器可讓您輕鬆提交在 Azure 計算上執行的單一節點和分散式 PyTorch 定型工作。

## <a name="single-node-training"></a>單一節點定型
使用 `PyTorch` 估算器來定型，類似於使用[基礎 `Estimator`](how-to-train-ml-models.md)，因此請先詳讀操作說明文章，並確定已了解文章中說明的概念。
  
若要執行 PyTorch 作業，請將 `PyTorch` 物件具現化。 您應該已經建立[計算目標](how-to-set-up-training-targets.md#amlcompute)物件 `compute_target` 與[資料存放區](how-to-access-data.md)物件 `ds`。

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

我們在這裡會為 PyTorch 建構函式指定下列參數：

參數 | 描述
--|--
`source_directory` |  包含定型作業所需之所有程式碼的本機目錄。 此資料夾是從您的本機電腦複製到遠端計算
`script_params` |  指定您的訓練指令碼的命令列引數的字典`entry_script`，形式 < 命令列引數的值 > 組。  若要指定的詳細資訊的旗標，在`script_params`，使用`<command-line argument, "">`。
`compute_target` |  您的定型指令碼執行所在的遠端計算目標，在此案例中為 Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 叢集
`entry_script` |  要在遠端計算上執行之定型指令碼的檔案路徑 (相對於 `source_directory`)。 此檔案 (以及此檔案所相依的其他任何檔案) 都應位於此資料夾
`conda_packages` |  要透過 Conda 安裝的 Python 套件清單 (其中包含您的定型指令碼所需的套件)。 建構函式有另一個名為 `pip_packages` 的參數，您可以視需要將此參數用於任何 pip 套件
`use_gpu` |  請將此旗標設定為 `True`，以利用 GPU 進行定型。 預設為 `False`

由於您目前使用的是 `PyTorch` 估算器，因此用於定型的容器會包含 PyTorch 套件，以及在 CPU 與 GPU 上進行定型所需的相關相依性。

接著，提交 PyTorch 作業：
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>分散式定型
`PyTorch` 估算器也可以讓您在 Azure VM 的 CPU 與 GPU 叢集上大規模將模型定型。 您可以使用少量 API 呼叫輕鬆地執行分散式 PyTorch 定型，而 Azure Machine Learning 會在背景管理執行這類工作負載所需的一切基礎結構和協調流程。

Azure Machine Learning 目前支援使用 Horovod 架構的 PyTorch MPI 型分散式定型。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) \(英文\) 是 Uber 開發的分散式定型專用開放原始碼 Ring-Allreduce 架構。

若要執行使用 Horovod 架構的分散式 PyTorch，請建立 PyTorch 物件，如下所示：

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

此程式碼公開下列新參數給 PyTorch 建構函式：

參數 | 描述 | 預設值
--|--|--
`node_count` |  用於定型作業的節點數目。 | `1`
`process_count_per_node` |  要在每個節點上執行的處理序 (或「背景工作角色」) 數目。 | `1`
`distributed_backend` |  用於啟動分散式定型的後端，由估算器透過 MPI 提供。  若要使用 MPI (與 Horovod) 執行平行或分散式定型 (例如 `node_count`>1 或 `process_count_per_node`>1，或兩者)，請設定 `distributed_backend='mpi'`。 Azure Machine Learning 所使用的 MPI 實作是[開放式 MPI](https://www.open-mpi.org/) \(英文\)。 | `None`

上述範例將執行有兩個背景工作角色的分散式定型，每個節點都有一個背景工作角色。

系統會為您安裝 Horovod 與其相依性，因此您只要在定型指令碼 `train.py` 中匯入 Horovod 即可，如下所示：
```Python
import torch
import horovod
```

最後，提交您的分散式 PyTorch 作業：
```Python
run = exp.submit(pt_est)
```

## <a name="export-to-onnx"></a>匯出至 ONNX

若要取得與最佳化的推斷[ONNX Runtime](concept-onnx.md)，您可以將您的定型的 PyTorch 模型轉換成 ONNX 格式。 請參閱[教學課程](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)的範例。

## <a name="examples"></a>範例

如需有關分散式深入學習的 Notebook，請參閱：
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
