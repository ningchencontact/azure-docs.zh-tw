---
title: 資料集版本設定
titleSuffix: Azure Machine Learning service
description: 瞭解版本資料集的最佳作法，以及如何搭配機器學習管線使用版本控制
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901998"
---
# <a name="version-and-track-datasets-in-experiments"></a>版本和追蹤實驗中的資料集

在此操作說明中，您將瞭解如何針對重現性的 Azure Machine Learning 資料集進行版本和追蹤。 資料集版本設定是將資料的狀態加入書簽的方式，因此您可以套用特定版本的資料集，以供未來的實驗使用。

要考慮進行版本控制的一般案例：

* 當有新資料可供重新定型時。
* 當您套用不同的資料準備或功能工程方法時。

## <a name="prerequisites"></a>必要條件

針對此操作說明，您需要：

- [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括[azureml 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)封裝。
    
- [Azure Machine Learning 工作區](concept-workspace.md)。 使用下列程式碼取出現有的，或[建立新的工作區](how-to-manage-workspace.md)。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning 資料集](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>註冊並取出資料集版本

註冊資料集可讓您在實驗和同事之間進行版本、重複使用及共用。 您可以使用相同的名稱來註冊多個資料集，並依名稱和版本號碼來抓取特定版本。

### <a name="register-a-dataset-version"></a>註冊資料集版本

下列程式碼會將 `create_new_version` 參數設定為 `True`，以註冊新版本的資料集 `titanic_ds`。 如果沒有已向工作區註冊的現有 `titanic_ds`，它會建立名稱為 `titanic_ds` 的新資料集，並將其版本設定為1。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>依名稱取得資料集

根據預設，`Dataset` 類別上的[get_by_name （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法會傳回已向工作區註冊之資料集的最新版本。 

下列程式碼會取得 `titanic_ds` 資料集的第1版。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>版本控制最佳做法

建立資料集版本時，您**不**會使用工作區來建立額外的資料複本。 資料集是儲存體服務中的資料參考，因此您的儲存體服務只有一個真實的事實管理來源。 

>[!IMPORTANT]
> 如果覆寫或刪除您的資料集所參考的資料，呼叫特定版本的資料集就無法還原變更。 

從資料集載入資料時，它一律會載入資料集所參考的目前資料內容。 如果您想要確保每個資料集版本的重現性，建議您不要修改資料集版本所參考的資料內容。 當新資料進入時，將新的資料檔案儲存至不同的資料檔案夾，並建立新的資料集版本以包含該新的資料檔案夾中的資料。

下列影像和範例程式碼顯示建議的方式來結構您的資料檔案夾，並建立參考這些資料夾的資料集版本。

![資料夾結構](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>版本管線輸出資料集

您可以使用資料集做為每個機器學習（ML）管線步驟的輸入和輸出。 當您重新執行管線時，每個管線步驟的輸出都會註冊為新的資料集版本。 

因為 ML 管線會在每次重新執行管線時，將每個步驟的輸出填入新的資料夾，所以已建立版本的輸出資料集會重現。

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>在實驗中追蹤資料集

針對每個機器學習實驗，您可以透過已註冊模型的 `Run` 物件，輕鬆地追蹤用來做為輸入的資料集。

使用下列程式碼，向資料集註冊模型。

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

註冊之後，您就可以使用 Python 或[工作區登陸頁面](https://ml.azure.com/)來查看向資料集註冊的模型清單。

下列程式碼會使用[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--)方法來追蹤該實驗執行所使用的輸入資料集。

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

您也可以使用[工作區登陸頁面（預覽）](https://ml.azure.com/)，從實驗中尋找 `input_datasets`。 

下圖顯示在工作區登陸頁面上尋找實驗之輸入資料集的位置。 在此範例中，流覽至您的**實驗**窗格，然後開啟特定實驗執行的 **屬性** 索引標籤，`keras-mnist`。 

![輸入資料集](media/how-to-version-datasets/input-datasets.png)

您也可以使用工作區登陸頁面來尋找使用您的資料集的模型。 下列視圖來自 [資產] 底下的 [資料集] 分頁。 選取資料集，然後流覽至 [模型] 索引標籤，以取得使用該資料集之模型的清單。 

![輸入資料集模型](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>後續步驟

* [使用資料集進行定型](how-to-train-with-datasets.md)。
* [更多資料集的範例筆記本](https://aka.ms/dataset-tutorial)。
