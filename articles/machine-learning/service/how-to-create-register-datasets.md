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
ms.date: 05/21/2019
ms.openlocfilehash: c5b423fca3e0ec116fceefb6867189f4f8413b96
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856094"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>在 Azure Machine Learning 中建立和存取資料集 (預覽)

在本文中, 您將瞭解如何建立 Azure Machine Learning 資料集 (預覽), 以及如何從本機或遠端實驗存取資料。

有了 Azure Machine Learning 資料集, 您可以: 

* 在資料集所參考的**儲存體中保留一份資料複本**

* 透過探索資料分析來**分析資料** 

* **在模型定型期間輕鬆存取資料**, 而不需要擔心連接字串或資料路徑

* **共用資料 &** 與其他使用者共同作業

## <a name="prerequisites"></a>先決條件

若要建立及使用資料集, 您需要:

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure Machine Learning 服務工作區](how-to-manage-workspace.md)

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), 其中包括 azureml 資料集封裝。

> [!Note]
> 某些資料集類別 (預覽) 在[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件 (GA) 上具有相依性。 針對 Linux 使用者, 只有下列散發版本才支援這些類別:Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="data-formats"></a>資料格式

您可以從下列格式建立 Azure Machine Learning 資料集:
+ [分隔](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [pandas 資料框架](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL query](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>建立資料集 

藉由建立資料集, 您可以建立資料來源位置的參考, 以及其中繼資料的複本。 資料會保留在現有的位置, 因此不會產生額外的儲存成本。

### <a name="create-from-local-files"></a>從本機檔案建立

藉由使用[`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) `Dataset`類別的方法指定檔案或資料夾路徑, 從本機電腦載入檔案。  這個方法會執行下列步驟, 而不需要您指定檔案類型或剖析引數:

* 推斷和設定分隔符號。
* 略過檔案頂端的空白記錄。
* 推斷和設定標頭資料列。
* 推斷和轉換資料行資料類型。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

或者, 使用檔案特有的函式來明確控制檔案的剖析。 


### <a name="create-from-azure-datastores"></a>從 Azure 資料存放區建立

若要從[Azure 資料](how-to-access-data.md)存放區建立資料集:

* 確認您擁有`contributor`或`owner`已註冊 Azure 資料存放區的存取權。

* 藉由參考資料存放區中的路徑來建立資料集 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

使用方法，從 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) 讀取分隔的檔案，並建立未註冊的資料集。`from_delimited_files()`

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>註冊資料集

若要完成建立程式, 請使用工作區註冊您的資料集:

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)使用方法將資料集註冊到您的工作區, 讓它們可以與其他人共用, 並在各種不同的實驗中重複使用。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 如果`exist_ok = False`為 (預設值), 而且您嘗試使用與另一個相同的名稱來註冊資料集, 就會發生錯誤。 將設定`True`為以覆寫現有的。

## <a name="access-data-in-datasets"></a>存取資料集中的資料

已註冊的資料集會在計算叢集 (例如 Azure Machine Learning 計算) 上, 從本機和遠端存取。 若要跨實驗存取已註冊的資料集, 請使用下列程式碼, 依名稱取得您的工作區和已註冊的資料集。

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>後續步驟

* [探索和準備資料集](how-to-explore-prepare-data.md)。
* 如需使用資料集的範例, 請參閱[範例筆記本](https://aka.ms/dataset-tutorial)。
