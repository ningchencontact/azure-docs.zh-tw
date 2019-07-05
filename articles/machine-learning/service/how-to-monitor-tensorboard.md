---
title: 使用 TensorBoard 和 Azure 機器學習服務實驗以視覺化方式檢視
description: 啟動 TensorBoard 來視覺化實驗執行歷程記錄，並找出潛在的超參數微調和重新訓練的部分。
services: machine-learning
author: maxluk
ms.author: maxluk
ms.reviewer: nibaccam
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: babd4cdf8b7ed9e04b4bd975d840688b27439c4f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560860"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>以視覺化方式檢視實驗執行和使用 TensorBoard 和 Azure Machine Learning 的計量

在本文中，您了解如何檢視您的實驗執行和度量使用 TensorBoard [`tensorboard`封裝](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)中主要的 Azure Machine Learning 服務 SDK。 一旦您已檢查您的實驗執行，可進一步調整，並重新定型機器學習模型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview)是一套可檢查及了解您的實驗結構和效能的 web 應用程式。

如何使用 Azure Machine Learning 實驗來啟動 TensorBoard 視實驗的類型而定：
+ 如果您的實驗原生輸出記錄檔，適用於 TensorBoard，例如 PyTorch、 Chainer 和 TensorFlow 的實驗，則您可以[直接啟動 TensorBoard](#direct)從實驗的執行歷程記錄。 

+ 用於不原生輸出 TensorBoard 可取用的檔案，例如像是 scikit-learn 或 Azure Machine Learning 實驗的實驗[`export_to_tensorboard()`方法](#export)TensorBoard 記錄在記錄檔匯出執行歷程記錄，並啟動從該處 TensorBoard。 

## <a name="prerequisites"></a>必要條件

* 若要啟動 TensorBoard 並檢視您的實驗執行歷程記錄，您的實驗必須先前已啟用記錄功能以追蹤其度量和效能。  

* 在下列環境之一，可以執行此操作說明中的程式碼： 

    * Azure Machine Learning Notebook VM-不需要下載或必要的安裝

        * 完成[雲端為基礎的 notebook 快速入門](quickstart-run-cloud-notebook.md#create-notebook)建立專用的 notebook 伺服器 SDK 與範例存放庫中預先載入。

        * 在 notebook 伺服器上的 samples 資料夾中，找到兩個已完成和瀏覽至這個目錄中展開 notebook:**作法-要-使用-azureml > 訓練與深度學習**。
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * 您自己的 Juptyer notebook 伺服器
      * 使用[建立工作區文章](setup-create-workspace.md)至
          * [安裝 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)與`tensorboard`額外
          * 建立工作區和其組態檔 (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>選項 1：直接在 TensorBoard 中執行歷程記錄的檢視

這個選項適用於實驗，原生輸出記錄可取用的檔案，依據 TensorBoard，例如 PyTorch，Chainer 和 TensorFlow 實驗。 如果不是實驗的情況下，使用[`export_to_tensorboard()`方法](#export)改。

下列範例程式碼會使用[MNIST 示範實驗](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)從遠端計算目標，Azure Machine Learning 計算 TensorFlow 的儲存機制。 接下來，我們會訓練我們的模型，使用 SDK 的自訂[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)，然後啟動 TensorBoard，針對此 TensorFlow 實驗中，也就是原生輸出 TensorBoard 事件檔案的實驗。

### <a name="set-experiment-name-and-create-project-folder"></a>設定實驗名稱，並建立專案資料夾

這裡我們命名實驗，並建立其資料夾。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下載 TensorFlow 示範實驗程式碼

TensorFlow 的存放庫具有與廣泛的 TensorBoard 檢測 MNIST 示範。 我們未這麼做，也不需要變更任何這段示範影片，才能使用 Azure Machine Learning 服務的程式碼。 下列程式碼中，我們會下載 MNIST 程式碼，並將它儲存在我們新建立的實驗資料夾中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
在整個 MNIST 程式碼檔，mnist_with_summaries.py，請注意，有行呼叫`tf.summary.scalar()`， `tf.summary.histogram()`，`tf.summary.FileWriter()`等等。這些方法群組、 記錄和標記您的實驗執行歷程記錄到的關鍵計量。 `tf.summary.FileWriter()`是特別重要，因為它會將資料從您記錄的實驗的計量，可用來產生視覺效果，加點 TensorBoard 序列化。

 ### <a name="configure-experiment"></a>設定實驗

在下列程式碼，方法，我們可以設定我們的實驗，並設定為記錄檔和資料的目錄。 這些記錄檔將會上傳至 「 成品 」 服務，TensorBoard 稍後存取。

>[!Note]
> 針對此 TensorFlow 範例中，您必須安裝 TensorFlow，本機電腦上。 此外，TensorBoard 模組 （也就是一個隨附 TensorFlow） 必須能夠存取此 notebook 核心，因為本機電腦是執行 TensorBoard 什麼。

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>建立叢集實驗
不過，可以在任何環境中建立您的實驗，而且仍然能夠啟動 TensorBoard 針對實驗執行歷程記錄，我們會建立這項實驗，AmlCompute 叢集。 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>使用 TensorFlow 估算器提交執行

TensorFlow 估計工具提供簡單的方式啟動 TensorFlow 訓練工作的計算目標上。 它透過泛型實作[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別，可用來支援任何架構。 如需有關訓練模型使用的泛型的估計工具的詳細資訊，請參閱[定型的模型，使用 Azure Machine Learning 使用估計工具](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>啟動 TensorBoard

您在執行期間或完成後，您可以啟動 TensorBoard。 在下列程式碼，我們會建立 TensorBoard 物件的執行個體， `tb`，會在實驗執行歷程記錄中載入`run`，，然後啟動使用 TensorBoard`start()`方法。 
  
[TensorBoard 建構函式](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)採用陣列的執行，因此請務必也將它傳入做為單一元素陣列。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>選項 2：若要檢視在 TensorBoard 中記錄為匯出記錄

下列程式碼範例實驗會設定、 開始使用 Azure Machine Learning 執行歷程記錄的 Api，在記錄處理，並將匯出執行歷程記錄記錄到記錄檔取用 TensorBoard 視覺效果的實驗。 

### <a name="set-up-experiment"></a>設定實驗

下列程式碼會設定新的實驗，並命名執行的目錄`root_run`。 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

這裡我們載入 diabetes 資料集-內建的小型資料集所隨附 scikit-learn-了解，並將它分割成測試集和定型集。

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>執行實驗，並記錄計量

此程式碼中，我們訓練線性迴歸模型，並記錄關鍵計量，alpha 係數中，`alpha`和均方根誤差， `mse`，在執行歷程記錄。

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>匯出執行 TensorBoard

使用 SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py)方法中，我們可以匯出到 TensorBoard 記錄檔，Azure 機器學習服務實驗的執行歷程記錄，因此我們可以透過 TensorBoard 來檢視它們。  

在下列程式碼中，我們會建立資料夾`logdir`我們目前的工作目錄中。 此資料夾是我們將在此匯出我們的實驗執行歷程記錄，並從記錄`root_run`，然後將標示為已完成執行。 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 您也可以匯出到 TensorBoard 特定的執行，藉由指定的執行中的名稱  `export_to_tensorboard(run_name, logdir)`

啟動和停止 TensorBoard 一次匯出我們對於這項實驗的執行歷程記錄，我們就可以啟動使用 TensorBoard [start](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-)方法。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

當您完成時，請務必呼叫[了 stop （)](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) TensorBoard 物件的方法。 否則，TensorBoard 會繼續執行，直到您關閉 notebook 核心。 

```python
tb.stop()
```

## <a name="next-steps"></a>後續步驟

在此使用說明，建立兩個實驗並了解如何啟動 TensorBoard 對其執行歷程記錄識別為潛在的調整和重新訓練的區域。 

* 如果您滿意您的模型時，請移至我們[如何將模型部署](how-to-deploy-and-where.md)文章。 
* 深入了解[超參數微調](how-to-tune-hyperparameters.md)。 
