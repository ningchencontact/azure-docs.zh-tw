---
title: 使用 azureml 進行定型-資料集
titleSuffix: Azure Machine Learning service
description: 瞭解如何在定型中使用資料集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/16/2019
ms.openlocfilehash: ceccc515b73bd41c7933889c61617c360c678eb7
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059290"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>在 Azure Machine Learning 中使用資料集（預覽）進行定型

在本文中，您將瞭解在遠端實驗訓練執行中取用[Azure Machine Learning 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)的兩種方式，而不需擔心連接字串或資料路徑。

- 選項 1：直接在定型腳本中傳遞資料集。

- 選項 2：使用資料集將檔掛接或下載至遠端計算以進行定型。

## <a name="prerequisites"></a>必要條件

若要使用資料集來建立和定型，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure Machine Learning 服務工作區](how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。

> [!Note]
> 某些資料集類別（預覽）在[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件上具有相依性。 針對 Linux 使用者，只有下列散發版本才支援這些類別：Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="option-1-pass-datasets-as-inputs-to-training-scripts"></a>選項 1：將資料集做為輸入傳遞至定型腳本

Azure Machine Learning 資料集提供與 Azure Machine Learning 訓練產品（例如[ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)和[HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)）的完美整合。 在此範例中，您會建立[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) ，並使用它做為`estimator`物件的輸入以進行定型。 

### <a name="create-a-tabulardataset"></a>建立 TabularDataset

下列程式碼會從 web url 建立未註冊的 TabularDataset。 您也可以從本機檔案或資料存放區中的路徑來建立資料集。 深入瞭解[如何建立資料集](https://aka.ms/azureml/howto/createdatasets)。

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="configure-the-estimator"></a>設定估計工具

[估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)物件是用來提交實驗執行。 Azure Machine Learning 已針對常見的機器學習架構和一般估計工具，預先設定估算器。

此程式碼會建立泛型估計工具物件`est`，它會指定

* 腳本的腳本目錄。 在此目錄中的所有檔案都會上傳到叢集節點以便執行。
* 訓練腳本， *train_titanic. .py*。
* 用於定型`titanic`的輸入資料集。
* 實驗的計算目標。
* 實驗的環境定義。

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)

```

### <a name="access-the-input-dataset-in-your-training-script"></a>存取定型腳本中的輸入資料集

TabularDataset 物件提供將資料載入 pandas 或 spark 資料框架的功能，讓您可以使用熟悉的資料準備和定型程式庫。 若要利用這項功能，您可以在定型設定中傳遞 TabularDataset 做為輸入，然後在您的腳本中加以取出。

若要這麼做，請透過定型腳本中[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py)的物件存取輸入資料集，然後[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--)使用方法。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>選項 2：將檔案掛接到遠端計算目標

如果您想要讓資料檔案可用於定型的計算目標，請使用[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)來掛接或下載其所參考的檔案。

當您掛接檔案系統時，您會將該檔案系統附加至目錄（掛接點），並將它提供給計算目標。 裝載會瞬間完成，因為檔案只會在處理時載入。 支援裝載並建議用於以 Linux 為基礎的計算，包括 Azure Machine Learning 計算、虛擬機器和 HDInsight。 若是非以 Linux 為基礎的計算，僅支援下載。  

>[!WARNING]
> 如果您的資料大小超過計算目標的儲存體限制，則下載將會失敗。

### <a name="create-a-filedataset"></a>建立 FileDataset

下列範例會從 web url 建立未註冊的 FileDataset。 深入瞭解如何從其他來源[建立資料集](https://aka.ms/azureml/howto/createdatasets)。

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>設定估計工具

除了透過估計工具中的`inputs`參數傳遞資料集，您也可以透過`script_params`引數傳遞資料集，並取得定型腳本中的資料路徑（掛接點）。 如此一來，您就可以避免從定型腳本 Azure Machine Learning SDK 的相依性。

[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)估計工具物件是用來提交 scikit-learn-學習實驗的執行。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>取得定型腳本中的資料

提交執行之後，資料`mnist`集所參考的資料檔案將會掛接至計算目標。 下列程式碼示範如何在您的腳本中取出資料。

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>筆記本範例

[範例筆記本](https://aka.ms/dataset-tutorial)會示範並擴充本文中的概念，例如，使用具有 ScriptRun 和[HyperdDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)物件的資料集。

## <a name="next-steps"></a>後續步驟

* 使用 TabularDatasets[自動訓練機器學習模型](how-to-auto-train-remote.md)。

* 使用 FileDatasets 將[影像分類模型定型](https://aka.ms/filedataset-samplenotebook)。
