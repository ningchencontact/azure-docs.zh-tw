---
title: 使用估算程式將 ML 模型定型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務 Estimator 類別，執行單一節點與分散式傳統機器學習與深度學習模型的定型
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0ebb12df835cf1c32e02419989b21684e9884c18
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184350"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 將模型定型

將機器學習模型 (特別是深度類神經網路) 定型通常是必須耗費大量時間與計算資源的工作。 一旦完成您的定型指令碼撰寫並在您本機電腦的小型資料子集上執行，您可能會想要相應增加您的工作負載。

為協助您進行定型，Azure Machine Learning Python SDK 提供高層級抽象 (亦即預估器類別)，可讓您輕鬆地在 Azure 生態環境終將其模型定型。 您可以建立及使用 [`Estimator` 物件](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)來提交任何您要在遠端計算上執行的定型程式碼，不論它是單一節點執行或跨 GPU 叢集的分散式定型。 針對 PyTorch 和 TensorFlow 作業，Azure Machine Learning 也會提供各自的自訂 `PyTorch` 與 `TensorFlow` 估算器來簡化這些架構的使用。

## <a name="train-with-an-estimator"></a>使用預估器來定型

建立您的[工作區](concept-azure-machine-learning-architecture.md#workspace)並設定您的[開發環境](how-to-configure-environment.md)後，請將 Azure Machine Learning 中的模型定型牽涉到下列步驟：  
1. 建立[遠端計算目標](how-to-set-up-training-targets.md)
2. 上傳您的[定型資料](how-to-access-data.md) (選擇性)
3. 建立您的[定型指令碼](tutorial-train-models-with-aml.md#create-a-training-script)
4. 建立 `Estimator` 物件
5. 提交您的定型作業

此文章的重點在於步驟 4-5。 針對步驟 1-3，請參閱[模型定型教學課程](tutorial-train-models-with-aml.md)以取得範例。

### <a name="single-node-training"></a>單一節點定型

使用 `Estimator` 在 Azure 中遠端計算上針對 scikit-learn 模型執行單一節點定型。 您應該已經建立[計算目標](how-to-set-up-training-targets.md#amlcompute)物件 `compute_target` 與[資料存放區](how-to-access-data.md)物件 `ds`。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

此程式碼片段會將下列參數指定給 `Estimator` 建構函式。

參數 | 說明
--|--
`source_directory`| 包含定型作業所需之所有程式碼的本機目錄。 此資料夾是從您的本機電腦複製到遠端計算 
`script_params`| 用於指定您的定型指令碼 `entry_script` 命令列引數的字典，格式為 <命令列引數, 值> 組
`compute_target`| 您的定型指令碼執行所在的遠端計算目標，在此案例中為 Azure Machine Learning Compute ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 叢集
`entry_script`| 要在遠端計算上執行之定型指令碼的檔案路徑 (相對於 `source_directory`)。 此檔案 (以及此檔案所相依的其他任何檔案) 都應位於此資料夾
`conda_packages`| 要透過 Conda 安裝的 Python 套件清單 (其中包含您的定型指令碼所需的套件)。  
建構函式有另一個名為 `pip_packages` 的參數，您可視需要將此參數用於任何 pip 套件

您現在已建立 `Estimator` 物件，請透過在您的 [Experiment](concept-azure-machine-learning-architecture.md#experiment) 物件 `experiment` 上呼叫 `submit` 函式，提交要在遠端計算上執行的定型作業。 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **特殊資料夾** Azure Machine Learning 會特別對待 *outputs* 與 *logs*, 這兩個資料夾。 在定型期間，當您將檔案寫入到相對於根目錄且名為 *outputs* 與 *logs* 的資料夾 (分別是 `./outputs` 與 `./logs`) 時，這些檔案會自動上傳到執行歷程記錄，因此當您的回合完成之後，您就能存取它們。
>
> 若要在定型期間建立成品 (例如模型檔案、檢查點、資料檔案或繪製的影像)，請將這些成品寫入到 `./outputs` 資料夾。
>
> 同樣地，您也可以將來自您定型回合的任何記錄檔寫入到 `./logs` 資料夾。 若要利用 Azure Machine Learning 的 [TensorBoard 整合](https://aka.ms/aml-notebook-tb)，請務必將您的 TensorBoard 記錄檔寫入到此資料夾。 當您的回合在執行時，您將能啟動 TensorBoard 並串流這些記錄檔。  稍後，您也可以從任何先前的回合還原記錄檔。
>
> 例如，若要在您的遠端定型回合執行完成之後將寫入到 *outputs* 資料夾的檔案下載到您的本機電腦，請執行：`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分散式定型與自訂 Docker 映像

您可以使用 `Estimator` 執行兩個額外的定型案例：
* 使用自訂 Docker 映像
* 多節點叢集上的分散式定型

下列程式碼顯示如何執行 CNTK 模型的分散式定型。 此外，它會假設您是使用自己的自訂 Docker 映像進行定型，而不是使用預設的 Azure Machine Learning 映像。

您應該已建立了自己的[計算目標](how-to-set-up-training-targets.md#amlcompute)物件`compute_target`。 您會依照下列方式建立估算器：

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

上面的程式碼公開下列新參數給 `Estimator` 建構函式：

參數 | 說明 | 預設值
--|--|--
`custom_docker_base_image`| 您要使用的映像名稱。 只提供公用 Docker 存放庫 (在此案例中是 Docker Hub) 中可用的映像。 若要使用來自私人 Docker 存放庫的映像，請改為使用建構函式的 `environment_definition` 參數 | `None`
`node_count`| 用於定型作業的節點數目。 | `1`
`process_count_per_node`| 要在每個節點上執行的處理序 (或「背景工作角色」) 數目。 在此案例中，您會使用每個節點上可用的 `2` 個 GPU。| `1`
`distributed_backend`| 用於啟動分散式定型的後端，由估算器透過 MPI 提供。  若要執行平行或分散式定型 (例如 `node_count`>1 或 `process_count_per_node`>1 或兩者)，請設定 `distributed_backend='mpi'`。 AML 所使用的 MPI 實作是[Open MPI](https://www.open-mpi.org/)。| `None`

最後，提交定型作業：
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>範例
如需將 sklearn 模型定型的 Notebook，請參閱：
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

如需有關分散式深入學習的 Notebook，請參閱：
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [將 PyTorch 模型定型](how-to-train-pytorch.md)
* [將 TensorFlow 模型定型](how-to-train-tensorflow.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
