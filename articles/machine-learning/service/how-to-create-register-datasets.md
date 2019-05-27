---
title: 建立資料集來存取與 azureml 資料集的資料
titleSuffix: Azure Machine Learning service
description: 了解如何建立從各種來源的資料集，並向您的工作區中的資料集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 949468dfe26b076b5c5cf5cab8bbdc2038c7bd2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165889"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>建立及存取 Azure Machine Learning 中的資料集 （預覽）

在本文中，您將了解如何建立 Azure Machine Learning 資料集 （預覽），以及如何存取本機和遠端的實驗中的資料。

使用受管理的資料集，您可以： 
* **輕鬆存取資料，在模型定型期間**而不需要重新連接到基礎存放區

* **確保資料一致性和重現性**在實驗中使用相同的指標： notebook、 自動化的 ml、 管線、 視覺介面

* **共用資料與共同作業**與其他使用者

* **瀏覽資料**（& s) 管理的資料快照集和版本的生命週期

* **比較資料**訓練，以生產環境


## <a name="prerequisites"></a>必要條件

若要建立及使用資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure 機器學習服務工作區 Azure 機器學習服務工作區](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [機器學習服務適用於 Azure SDK 安裝的 Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含資料集的 azureml 封裝。

> [!Note]
> 某些資料集類別 （預覽） 有相依性[azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)封裝 (GA)。 針對 Linux 使用者，只能在下列散發套件上支援這些類別：Red Hat Enterprise Linux、 Ubuntu、 Fedora 及 CentOS。

## <a name="data-formats"></a>資料格式

您可以從下列資料來建立 Azure Machine Learning 資料集：
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake 一般。1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>建立資料集 

您可以與您的資料集使用中的資料集的 azureml 封裝互動[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)特別[`Dataset`類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py)。

### <a name="create-from-local-files"></a>建立從本機檔案

從本機電腦載入檔案，藉由指定的檔案或資料夾的路徑，與[ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-)方法從`Dataset`類別。  這個方法不需要您指定的檔案類型，或剖析引數，執行下列步驟：

* 推斷，設定分隔符號。
* 略過空記錄在檔案頂端。
* 推斷，設定標頭資料列。
* 推斷和轉換資料行資料類型。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

或者，使用檔案特有的函式來明確地控制 剖析您的檔案。 


### <a name="create-from-azure-datastores"></a>從 Azure 資料存放區建立

若要從 Azure 資料存放區中建立資料集：

* 請確認您擁有`contributor`或`owner`存取已註冊的 Azure 資料存放區。

* 匯入[ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)並[ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition)並`Dataset`來自 SDK 封裝。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()`方法會擷取工作區中的現有資料存放區。

```
dstore = Datastore.get(workspace, datastore_name)
```

使用`from_delimited_files()`讀取分隔的檔案，並建立取消註冊的資料集的方法。

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>註冊資料集

若要完成建立程序，請向工作區中註冊您的資料集：

使用[ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)註冊到您的工作區的資料集，以便將它們與其他人共用，並跨各種實驗重複使用的方法。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 如果`exist_ok = False`（預設值），而您嘗試註冊的資料集具有相同名稱與另一個，就會發生錯誤。 若要設定`True`覆寫現有的。

## <a name="access-data-in-datasets"></a>在 資料集的存取資料

已註冊的資料集可供存取和需求的可取用在本機、 遠端和會計算叢集，例如 Azure Machine Learning 計算。 在實驗中重複使用您已註冊的資料集和計算環境，請使用下列程式碼來依名稱取得您的工作區和已註冊的資料集。

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>後續步驟

* [探索及準備資料集](how-to-explore-prepare-data.md)。
* [管理資料集定義的生命週期](how-to-manage-dataset-definitions.md)。
* 如需使用資料集的範例，請參閱 <<c0> [ 範例 notebook](https://aka.ms/dataset-tutorial)。
