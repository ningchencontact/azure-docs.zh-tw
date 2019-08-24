---
title: 建立資料集以使用 azureml-dataset 來存取資料
titleSuffix: Azure Machine Learning service
description: 瞭解如何從各種來源建立資料集, 並向您的工作區註冊資料集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 497a00570d85ab83f71416e979e485db4685b64a
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992122"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>在 Azure Machine Learning 中建立和存取資料集 (預覽)

在本文中, 您將瞭解如何建立 Azure Machine Learning 資料集 (預覽), 以及如何從本機或遠端實驗存取資料。

有了 Azure Machine Learning 資料集, 您可以: 

* 在資料集所參考的**儲存體中保存一份資料**。 

* **在模型定型期間輕鬆存取資料**, 而不需要擔心連接字串或資料路徑。

* **共用資料 &** 與其他使用者共同作業。

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集, 您需要:

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure Machine Learning 服務工作區](how-to-manage-workspace.md)

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), 其中包括 azureml 資料集封裝。

> [!Note]
> 某些資料集類別 (預覽) 在[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件上具有相依性。 針對 Linux 使用者, 只有下列散發版本才支援這些類別:Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="dataset-types"></a>資料集類型

資料集會根據使用者在定型中取用它們的方式, 分類成各種類型。 目前我們支援以表格格式表示資料的[TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) , 方法是剖析提供的檔案或檔案清單。 這讓您能夠將資料具體化為 pandas 資料框架。 您可以從 csv、tsv、parquet 檔案、SQL 查詢結果等建立物件。`TabularDataset`如需完整清單, 請流覽我們的檔。

若要深入瞭解即將推出的 API 變更, 請參閱[什麼是 Azure Machine Learning 服務？](https://aka.ms/tabular-dataset) 

## <a name="create-datasets"></a>建立資料集 

藉由建立資料集, 您可以建立資料來源位置的參考, 以及其中繼資料的複本。 資料會保留在現有的位置, 因此不會產生額外的儲存成本。

若要讓 Azure Machine Learning 服務可存取的資料, 必須從[Azure 資料存放區](how-to-access-data.md)或公用 web url 中的路徑建立資料集。

若要從[Azure 資料](how-to-access-data.md)存放區建立資料集:

* 確認您擁有`contributor`或`owner`已註冊 Azure 資料存放區的存取權。

* 藉由參考資料存放區中的路徑來建立資料集。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>建立 TabularDatasets

在類別上`TabularDatasetFactory`使用方法來讀取csv或tsv格式的檔案,並建立未註冊的TabularDataset。`from_delimited_files()` 如果您從多個檔案讀取, 結果將會匯總成一個表格式標記法。

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|式|Pclass|Name|性別|Age|SibSp|Parch|票|費用|插槽|著手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22。0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs John Bradley (Florence Briggs Th 。|female|38。0|1|0|電腦17599|71.2833|C85|C
2|3|1|3|Heikkinen, 錯過。 Laina|female|26.0|0|0|STON/O2。 3101282|7.9250||S

## <a name="register-datasets"></a>註冊資料集

若要完成建立程式, 請使用工作區註冊您的資料集:

`register()`使用方法將資料集註冊到您的工作區, 讓它們可以與其他人共用, 並在各種不同的實驗中重複使用。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>版本資料集

您可以建立新的版本, 以在相同名稱下註冊新的資料集。 [資料集版本] 是將資料的狀態設為書簽的方式, 因此您可以套用特定版本的資料集進行實驗或未來的複製。 考慮版本控制的一般案例: 
* 當有新資料可供重新定型時。
* 當您套用不同的資料準備或功能工程方法時。

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>在定型期間存取您的資料

已註冊的資料集會在計算叢集 (例如 Azure Machine Learning 計算) 上, 從本機和遠端存取。 若要跨實驗存取已註冊的資料集, 請使用下列程式碼, 依名稱取得您的工作區和已註冊的資料集。 根據預設, [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 類別上的方法會傳回已向工作區註冊之資料集的最新版本。`Dataset`

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>後續步驟

* 使用自動化機器學習來[訓練 TabularDatasets](https://aka.ms/automl-dataset)。
* 如需使用資料集進行定型的更多範例, 請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
