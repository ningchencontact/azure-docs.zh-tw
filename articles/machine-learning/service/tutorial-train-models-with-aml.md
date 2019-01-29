---
title: 映像分類教學課程：將模型定型
titleSuffix: Azure Machine Learning service
description: 本教學課程會示範如何使用 Azure Machine Learning 服務，搭配 scikit-learn 在 Python Jupyter Notebook 中將影像分類模型定型。 本教學課程是兩部分系列的第一部分。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a9fc0655a3666f09fed342af5b4f14e2097290ab
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828244"
---
# <a name="tutorial-train-an-image-classification-model-with-azure-machine-learning-service"></a>教學課程：使用 Azure Machine Learning 服務將映像分類模型定型

在本教學課程中，您會在本機和遠端計算資源，將機器學習模型定型。 您會在 Python Jupyter Notebook 中使用 Azure Machine Learning 服務的定型和部署工作流程。 然後，您可以使用 Notebook 作為範本，以自己的資料將您自己的機器學習服務模型定型。 本教學課程是**兩部分教學課程系列的第一部分**。  

本教學課程會搭配 Azure Machine Learning 服務使用 [MNIST](http://yann.lecun.com/exdb/mnist/) 資料集和 [scikit-learn](https://scikit-learn.org) 來進行簡單的羅吉斯迴歸定型。 MNIST 是熱門的資料集，由 70,000 個灰階影像所組成。 每個影像都是 28 x 28 像素的手寫數字，代表 0 到 9 的數字。 目標是要建立多類別分類器，以識別特定影像所代表的數字。 

了解如何執行下列動作：

> [!div class="checklist"]
> * 設定您的開發環境。
> * 存取及檢查資料。
> * 使用熱門的 scikit-learn 機器學習程式庫，在本機進行簡單的羅吉斯迴歸定型。 
> * 在遠端叢集上將多個模型定型。
> * 檢閱定型結果並註冊最佳模型。

您會在[本教學課程的第二部分](tutorial-deploy-models-with-aml.md)中，了解如何選取模型及部署模型。 

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

>[!NOTE]
> 本文中的程式碼使用 Azure Machine Learning SDK 1.0.2 版進行測試。

## <a name="get-the-notebook"></a>取得 Notebook

為了方便起見，此教學課程以 [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb) 形式提供。 在 [Azure Notebooks](https://notebooks.azure.com/) 或您自己的 Jupyter Notebook 伺服器中執行 `tutorials/img-classification-part1-training.ipynb` Notebook。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]


## <a name="set-up-your-development-environment"></a>設定開發環境

針對您開發工作的所有設定都可以在 Python Notebook 中完成。 設定包含下列動作：

* 匯入 Python 套件。
* 連線到工作區，以便讓本機電腦能夠與遠端資源進行通訊。
* 建立實驗來追蹤所有執行。
* 建立要用於定型的遠端計算目標。

### <a name="import-packages"></a>匯入套件

匯入在本工作階段所需的 Python 套件。 此外，也顯示 Azure Machine Learning SDK 版本：

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>連線到工作區

從現有的工作區建立工作區物件。 `Workspace.from_config()` 會讀取 **config.json** 檔案，並將詳細資料載入到名為 `ws` 的物件：

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗，以追蹤您工作區中的執行。 一個工作區可以有多個實驗： 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-amlcompute"></a>建立或連結現有的 AMlCompute

資料科學家可藉由使用 Azure Machine Learning Compute (AmlCompute) 這項受控服務，在 Azure 虛擬機器叢集上將機器學習模型定型。 範例包括具有 GPU 支援的 VM。 在本教學課程中，您可以建立 AmlCompute 作為訓練環境。 如果您的工作區中尚未有計算叢集，則此程式碼會為您建立計算叢集。

 **建立計算需要大約 5 分鐘的時間。** 如果工作區中已經有計算，則此程式碼會使用它而略過建立程序：


```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use the 'status' property    
    print(compute_target.status.serialize())
```

您現在具有必要的套件和計算資源，可以在雲端將模型定型。 

## <a name="explore-data"></a>探索資料

在您將模型定型之前，必須先了解用來將模型定型的資料。 此外，您也需要將資料複製到雲端。 如此，您的雲端定型環境便可以存取該資料。 在本節中，您會了解如何執行下列動作：

* 下載 MNIST 資料集。
* 顯示一些範例影像。
* 將資料上傳至雲端。

### <a name="download-the-mnist-dataset"></a>下載 MNIST 資料集

下載 MNIST 資料集，並將檔案儲存到 `data` 本機目錄。 這會同時下載定型和測試用的影像與標籤：


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>顯示一些範例影像

將壓縮的檔案載入到 `numpy` 陣列。 然後使用 `matplotlib` 來繪製 30 個來自資料集的隨機影像，並且在其上加上標籤。 此步驟需要 `util.py` 檔案中所包含的 `load_data` 函式。 這個檔案包含在範例資料夾。 請確定將它放在與此 Notebook 相同的資料夾中。 `load_data` 函式會直接將壓縮檔案剖析為 numpy 陣列：



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the training set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

影像的隨機範例會顯示：

![隨機的影像範例](./media/tutorial-train-models-with-aml/digits.png)

現在您已了解這些影像外觀與預期的預測結果。

### <a name="upload-data-to-the-cloud"></a>將資料上傳至雲端

現在，請將資料從本機電腦上傳至 Azure，以讓資料可供遠端存取。 如此便可存取該資料來進行遠端定型。 資料存放區是與您工作區相關的便利建構，可供您上傳/下載資料。 您也可以從遠端計算目標與其進行互動。 它受到 Azure Blob 儲存體帳戶支援。

MNIST 檔案會上傳到資料存放區根目錄中名為 `mnist` 的目錄：

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
您現在已經具備開始將模型定型所需的一切。 

## <a name="train-a-local-model"></a>定型本機模型

使用 scikit-learn 在本機進行簡單的羅吉斯迴歸模型定型。

**在本機定型可能需要一或兩分鐘的時間**，視您的電腦設定而定：

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

接著，使用測試集來進行預測，並計算精確度： 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

本機模型精確度會顯示：

`0.9202`

只要幾行程式碼，您便可達到 92% 的精確度。

## <a name="train-on-a-remote-cluster"></a>在遠端叢集上定型

現在您可以建置具有不同正規化速率的模型，展開這個簡單的模型。 這次，您會在遠端資源上進行模型定型。  

針對這項工作，將工作提交至您稍早設定的遠端定型叢集。 若要提交作業，您需執行下列步驟：
* 建立目錄。
* 建立定型指令碼。
* 建立估算器物件。
* 提交作業。

### <a name="create-a-directory"></a>建立目錄

建立目錄，以從您的電腦將必要程式碼傳遞給遠端資源：

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>建立定型指令碼

若要將工作提交到叢集，請先建立定型指令碼。 請執行下列程式碼，以在您建立的目錄中建立名為 `train.py` 的定型指令碼。 此定型會為定型演算法新增正規化率。 因此會產生與本機版本稍有不同的模型：

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

請注意指令碼取得資料並儲存模型的方式：

+ 定型指令碼會讀取引數以尋找包含資料的目錄。 在您稍後提交工作時，會指向這個引數的資料存放區：`parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')`。

+ 定型指令碼會將模型儲存到名為 **outputs** 的目錄中： <br/>
`joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')` 。<br/>
寫入此目錄之任何項目會自動上傳到您的工作區。 稍後在本教學課程中，您會從這個目錄存取您的模型。
會從定型指令碼參考檔案 `utils.py`，以便正確載入資料集。 請將這個指令碼複製到指令碼資料夾中，以便可以一起存取它及遠端資源上的定型指令碼。


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>建立估計工具

估計工具物件用來提交執行。 請執行下列程式碼來定義下列項目，以建立您的估算器：

* 估算器物件的名稱 (`est`)。
* 包含指令碼的目錄。 在此目錄中的所有檔案都會上傳到叢集節點以便執行。 
* 計算目標。 在此案例中，您會使用您所建立的 Azure Machine Learning 計算叢集。
* 定型指令碼名稱 (**train.py**)。
* 來自定型指令碼的必要參數。 
* 定型所需的 Python 套件。

在本教學課程中，這個目標會是 AmlCompute。 指令碼資料夾中的所有檔案都會上傳到叢集節點以便執行。 **data_folder** 會設定為使用資料存放區 `ds.as_mount()`：

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>將工作提交至叢集

提交估算器物件以執行實驗：

```python
run = exp.submit(config=est)
run
```

由於呼叫是非同步的，因此只要工作一啟動，它就會傳回 **Preparing** 或 **Running** 狀態。

## <a name="monitor-a-remote-run"></a>監視遠端執行

第一次執行總計會花費**大約 10 分鐘的時間**。 但針對後續的執行，只要指令碼相依性不變，便會重複使用相同的影像。 因此容器啟動時間會快許多。

在您等候時，會發生什麼事：

- **影像建立**：系統會建立一個符合估算器所指定 Python 環境的 Docker 映像。 影像會上傳到工作區。 映像的建立和上傳會花費**大約 5 分鐘的時間**。 

  這個階段會針對每個 Python 環境發生一次，因為系統會快取容器以供後續執行使用。 在影像建立期間，會將記錄串流至執行歷程記錄中。 您可以使用這些記錄來監視映像建立程序。

- **調整**：如果遠端叢集需要比目前可用節點數更多的節點來進行執行，將會自動新增額外的節點。 調整通常會花費**大約 5 分鐘的時間**。

- **Running**：在這個階段，會將必要的指令碼與檔案傳送到計算目標。 然後會掛接或複製資料存放區。 接著會執行 **entry_script**。 當作業執行時，**stdout** 和 **./logs** 目錄會串流至執行歷程記錄。 您可以使用這些記錄來監視執行的進度。

- **後處理**：執行的 **./outputs** 目錄會複製到您工作區中的執行歷程記錄，以便您存取這些結果。


您可以透過數種方式檢查執行中作業的進度。 本教學課程會使用 Jupyter 小工具和 `wait_for_completion` 方法。 

### <a name="jupyter-widget"></a>Jupyter 小工具

使用 Jupyter 小工具觀看執行的進度。 與提交執行相同，小工具會以非同步方式作業，並且會每隔 10 到 15 秒提供即時更新，直到作業完成為止：


```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

以下的靜止快照是定型結束時所顯示的小工具：

![Notebook 小工具](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>在完成時取得記錄檔結果

模型定型和監視會在背景中發生。 請等到模型完成定型之後，再執行更多程式碼。 使用 `wait_for_completion` 可顯示模型定型何時完成： 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>顯示執行結果

您現在在遠端叢集上已有定型的模型。 擷取模型的精確度：

```python
print(run.get_metrics())
```
輸出顯示遠端模型的精確度稍微高於本機模型，因為在定型期間新增了正規化率：  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

在下一個教學課程中，您將更詳細地探索這個模型。

## <a name="register-model"></a>註冊模型

定型指令碼的最後一個步驟會在工作執行所在叢集之 VM 中名為 `outputs` 的目錄內，寫入 `outputs/sklearn_mnist_model.pkl` 檔案。 `outputs` 目錄的特殊之處，在於此目錄中的所有內容都會自動上傳到您的工作區。 此內容會出現在您工作區下，實驗的執行記錄中。 因此，模型檔案現在也在您的工作區中可供使用。

您可以看到與該執行相關聯的檔案：

```python
print(run.get_file_names())
```

請在工作區中註冊模型，以便您或其他共同作業者稍後可以查詢、檢查和部署此模型：

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

您也可以只刪除 Azure Machine Learning Compute 叢集。 不過，自動調整已開啟，且叢集最小值為零。 因此，這個特定資源在不處於使用中狀態時，並不會產生額外的計算費用：


```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>後續步驟

在這個 Azure Machine Learning 服務教學課程中，您已使用 Python 來進行下列工作：

> [!div class="checklist"]
> * 設定您的開發環境。
> * 存取及檢查資料。
> * 使用熱門的 scikit-learn 機器學習程式庫，在本機進行簡單的羅吉斯迴歸定型。
> * 在遠端叢集上將多個模型定型。
> * 檢閱定型詳細資料並註冊最佳的模型。

您已準備好使用教學課程系列下一個部分中的指示，來部署這個已註冊的模型：

> [!div class="nextstepaction"]
> [教學課程 2 - 部署模型](tutorial-deploy-models-with-aml.md)
