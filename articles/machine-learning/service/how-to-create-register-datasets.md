---
title: 建立資料集以使用 azureml-dataset 來存取資料
titleSuffix: Azure Machine Learning
description: 瞭解如何從各種來源建立資料集，並向您的工作區註冊資料集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 10/10/2019
ms.openlocfilehash: f85b286de1318181ab660d51d5f434375f1fa46f
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965333"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>在 Azure Machine Learning 中建立和存取資料集（預覽）

在本文中，您將瞭解如何建立 Azure Machine Learning 資料集（預覽），以及如何從本機或遠端實驗存取資料。

有了 Azure Machine Learning 資料集，您可以：

* 在資料集所參考的**儲存體中保存一份資料**。

* **在模型定型期間輕鬆存取資料**，而不需要擔心連接字串或資料路徑。

* **共用資料 &** 與其他使用者共同作業。

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。

> [!Note]
> 某些資料集類別（預覽）在[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件上具有相依性。 針對 Linux 使用者，只有下列散發版本才支援這些類別： Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="dataset-types"></a>資料集類型

資料集會根據使用者在定型中取用它們的方式，分類成兩種類型。

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)藉由剖析提供的檔案或檔案清單，以表格格式表示資料。 這讓您能夠將資料具體化為 pandas 或 spark 資料框架。 您可以從 csv、tsv、parquet 檔、SQL 查詢結果等建立 `TabularDataset` 物件。如需完整清單，請流覽我們的[檔](https://aka.ms/tabulardataset-api-reference)。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)會參考您資料存放區或公用 url 中的單一或多個檔案。 這讓您能夠將檔案下載或掛接至您的計算。 這些檔案可以是任何格式，讓更廣泛的機器學習服務案例，包括深度學習。

若要深入瞭解即將推出的 API 變更，請參閱[這裡](https://aka.ms/tabular-dataset)。

## <a name="create-datasets"></a>建立資料集

藉由建立資料集，您可以建立資料來源位置的參考，以及其中繼資料的複本。 資料會保留在現有的位置，因此不會產生額外的儲存成本。 您可以使用 Python SDK 或工作區登陸頁面（預覽）來建立 TabularDatasets 和 FileDatasets。 

若要讓 Azure Machine Learning 可存取的資料，必須從[Azure 資料存放區](how-to-access-data.md)或公用 web url 中的路徑建立資料集。

### <a name="using-the-sdk"></a>使用 SDK

若要使用 Python SDK 從[Azure 資料](how-to-access-data.md)存放區建立資料集：

* 確認您已 `contributor` 或 `owner` 存取已註冊的 Azure 資料存放區。

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
#### <a name="create-tabulardatasets"></a>建立 TabularDatasets

使用 `TabularDatasetFactory` 類別上的[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-)方法來讀取 csv 或 tsv 格式的檔案，並建立未註冊的 TabularDataset。 如果您從多個檔案讀取，結果將會匯總成一個表格式標記法。

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

根據預設，建立 TabularDataset 時，會自動推斷資料行資料類型。 如果推斷的類型不是預期的，您可以使用下列程式碼來指定資料行類型。 [在這裡](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)深入瞭解支援的資料類型。

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|式|Pclass|Name|性別|年齡|SibSp|Parch|票|費用|插槽|著手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|否|3|Braund，Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|是|1|Cumings，Mrs John Bradley （Florence Briggs Th 。|female|38.0|1|0|電腦17599|71.2833|C85|C
2|3|是|3|Heikkinen，錯過。 Laina|female|26.0|0|0|STON/O2。 3101282|7.9250||S

使用 `TabularDatasetFactory` 類別上的[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-)方法，從 Azure SQL Database 讀取。

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

在 TabularDatasets 中，您可以從資料中的資料行指定時間戳記，或將路徑模式資料儲存在中，以啟用時間序列特性，讓您能夠依時間輕鬆且有效率地進行篩選。

使用 `TabularDataset` 類別上的[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)方法來指定時間戳記資料行，並依時間啟用篩選。 您可以在[這裡](https://aka.ms/azureml-tsd-notebook)找到更多範例和詳細資料。

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-filedatasets"></a>建立 FileDatasets

使用 `FileDatasetFactory` 類別上的[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)方法，以任何格式載入檔案，並建立未註冊的 FileDataset。

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="using-the-workspace-landing-page"></a>使用工作區登陸頁面

登入[工作區登陸頁面](https://ml.azure.com)，即可透過 web 體驗來建立資料集。 工作區登陸頁面支援建立 TabularDatasets 和 FileDatasets。

下列動畫顯示如何在工作區登陸頁面中建立資料集。

首先，在左窗格的 [**資產**] 區段中選取 [**資料集**]。 然後，選取 [ **+ 建立資料集**] 來選擇資料集的來源;這可以是來自本機檔案、資料存放區或公用 web url。 選取**資料集類型**： * [表格式] 或 [檔案]。 [**設定] 和 [預覽**] 和 [**架構**表單] 會根據檔案類型以智慧方式填入。 選取 **[下一步]** 加以審查，或在建立前進一步設定您的資料集。 選取 [**完成**] 以完成資料集建立。

![使用 UI 建立資料集](media/how-to-create-register-datasets/create-dataset-ui.gif)

## <a name="register-datasets"></a>註冊資料集

若要完成建立程式，請向工作區註冊您的資料集。

使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)方法向您的工作區註冊資料集，讓它們可以與其他人共用，並在各種不同的實驗中重複使用。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> 透過工作區登陸頁面建立的資料集會自動註冊到工作區。

## <a name="version-datasets"></a>版本資料集

您可以建立新的版本，以在相同名稱下註冊新的資料集。 [資料集版本] 是將資料的狀態設為書簽的方式，因此您可以套用特定版本的資料集進行實驗或未來的複製。 深入瞭解[資料集版本](how-to-version-track-datasets.md)。
```Python
# create a TabularDataset from Titanic training data
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


## <a name="access-datasets-in-your-script"></a>存取腳本中的資料集

已註冊的資料集會在計算叢集（例如 Azure Machine Learning 計算）上，從本機和遠端存取。 若要跨實驗存取已註冊的資料集，請使用下列程式碼，依名稱取得您的工作區和已註冊的資料集。 根據預設，`Dataset` 類別上的[`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法會傳回已向工作區註冊之資料集的最新版本。

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

* 瞭解[如何使用資料集進行定型](how-to-train-with-datasets.md)。
* 使用自動化機器學習來[訓練 TabularDatasets](https://aka.ms/automl-dataset)。
* 如需使用資料集進行定型的更多範例，請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
