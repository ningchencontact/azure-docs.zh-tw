---
title: 建立並向您的工作區中的資料集
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
ms.date: 05/02/19
ms.openlocfilehash: d3502219f03d4ad076a693ab990f2fadb0b5d558
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800837"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>建立並註冊 Azure Machine Learning 資料集 （預覽）

在本文中，您將了解 Azure Machine Learning 工作流程，以建立並註冊資料集，以及如何存取它們以供重複使用在本機和遠端的實驗。

Azure Machine Learning 資料集 （預覽） 讓您更輕鬆地存取及處理資料。 資料集來管理各種案例，例如模型訓練和管線建立中的資料。 使用[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，您可以使用熱門的格式中的資料，存取基礎儲存體、 探索和準備資料、 管理生命週期的不同資料集定義，以及比較中使用的資料集之間訓練和在生產環境中。

## <a name="prerequisites"></a>必要條件

若要建立並註冊您所需要的資料集：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure 機器學習服務工作區](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

* Azure 機器學習適用於 Python 的 SDK。 若要安裝或更新至最新版的 sdk，請參閱[安裝或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

> [!Note]
> 某些資料集類別 （預覽） 有相依性[azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)封裝 (GA)。 針對 Linux 使用者，只能在下列散發套件上支援這些類別：Red Hat Enterprise Linux、 Ubuntu、 Fedora 及 CentOS。

## <a name="create-datasets-from-local-files"></a>從本機檔案建立資料集

從本機電腦載入檔案，藉由指定的檔案或資料夾的路徑，與[ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-)方法從`Dataset`類別。  這個方法不需要您指定的檔案類型，或剖析引數，執行下列步驟：

* 推斷，設定分隔符號。
* 略過空記錄在檔案頂端。
* 推斷，設定標頭資料列。
* 推斷和轉換資料行資料類型。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

或者，使用檔案特有的函式來明確地控制 剖析您的檔案。 目前，資料集 SDK 支援[分隔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)， [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)， [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)，[二進位](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)，以及[json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)檔案格式。

## <a name="create-datasets-from-azure-datastores"></a>從 Azure 資料存放區中建立資料集

若要從 Azure 資料存放區中建立資料集，請務必：

* 請確認您有 「 參與者 」 或 「 擁有者存取已註冊的 Azure 資料存放區。

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

||ID|案例編號|Date|封鎖|IUCR|主要類型|說明|位置描述|阻止|國內|...|Ward|社群區域|FBI 程式碼|X 座標|Y 座標|Year|更新日期|緯度|經度|位置|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|詐騙的作法|透過美金 300 元的金融身分遭竊|OTHER|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|遭竊| 從建立|居住地|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S 沙加緬度 AVE|1154|詐騙的作法|金融身分遭竊美金 300 元歲以下|居住地|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|詐騙的作法|偽造|居住地|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|遭竊|從建立|學校、 公用，建置|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>註冊您的資料集與工作區

使用[ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)方法來註冊您的工作區的共用資料集，並重複使用您的組織內和跨各種不同的實驗。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 預設參數值`register()`是`exist_ok = False`。 如果您嘗試註冊具有相同名稱的資料集，而不需要變更此設定會發生錯誤。

`register()`方法會傳回已註冊的資料集使用參數設定時， `exist_ok = True`。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = True
                           )
```

使用`list()`若要查看所有已註冊的資料集，在您的工作區中。

```Python
Dataset.list(workspace_name)
```

上述程式碼是由下列項目所導致：

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>存取工作區中的資料集

已註冊的資料集可供存取和需求的可取用在本機、 遠端和會計算叢集，例如 Azure Machine Learning 計算。 在實驗中重複使用您已註冊的資料集和計算環境，請使用下列程式碼來依名稱取得您的工作區和已註冊的資料集。

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>後續步驟

* [探索及準備資料集](how-to-explore-prepare-data.md)。
* [管理資料集定義的生命週期](how-to-manage-dataset-definitions.md)。
* 如需使用資料集的範例，請參閱 <<c0> [ 範例 notebook](https://aka.ms/dataset-tutorial)。
