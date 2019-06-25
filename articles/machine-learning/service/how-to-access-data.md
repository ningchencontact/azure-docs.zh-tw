---
title: 存取資料存放區/Blob 中的資料以進行訓練
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務，在訓練期間使用資料存放區來存取 Blob 資料儲存體
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 638d7bfb0e396874415c1055c4b707a65caffa4e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269295"
---
# <a name="access-data-from-your-datastores"></a>從您的資料存放區存取資料

 在 Azure Machine Learning 服務中，資料存放區會計算與位置無關的機制，來存取儲存體，而不需要變更您的原始程式碼。 無論您撰寫訓練程式碼，以做為參數，使用路徑或資料存放區直接提供給 estimator，Azure Machine Learning 工作流程會確保您的資料存放區位置可供存取，且提供給您的計算內容。

此操作說明會顯示下列工作的範例：
* [選擇資料存放區](#access)
* [取得資料](#get)
* [上傳和下載資料至資料存放區](#up-and-down)
* [在定型期間存取資料存放區](#train)

## <a name="prerequisites"></a>必要條件

若要使用資料存放區，必須先有[工作區](concept-workspace.md)。

從[建立新工作區](setup-create-workspace.md#sdk)，或擷取現有工作區開始：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>選擇資料存放區

您可以使用的預設資料存放區，或自備。

### <a name="use-the-default-datastore-in-your-workspace"></a>在 您的工作區中使用的預設資料存放區

 每個工作區都有一個已註冊，預設資料存放區，您可以立即使用。

取得工作區的預設資料存放區：

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>註冊您自己的資料存放區與工作區

如果您有現有的 Azure 儲存體，可以在工作區中將它註冊為資料存放區。 

<a name="store"></a>

####  <a name="storage-guidance"></a>儲存體指引

我們建議您 blob 儲存體和 blob 資料存放區。 標準和進階儲存體可供使用 blob。 雖然昂貴，我們建議在進階儲存體輸送量速度可能會改善您的訓練的速度，因為執行 particlularly 是否您針對大型資料集進行訓練。 請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)成本資訊的儲存體帳戶。

>[!NOTE]
> Azure Machine Learning 服務支援其他類型的資料存放區，這可能是適用於特定案例。 例如，如果您必須定型使用儲存在資料庫中的資料，您可能會使用 AzureSQLDatabaseDatastore 或 AzurePostgreSqlDatastore。 請參閱[本表](#matrix)可用的資料存放區類型。

#### <a name="register-your-datastore"></a>註冊您的資料存放區
上的所有註冊的方法都都[ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別，並有表單 register_azure_ *。

下列範例將示範以 Azure Blob 容器或 Azure 檔案共用註冊為資料存放區。

+ 針對**Azure Blob 容器資料存放區**，使用 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ 針對**Azure 檔案共用資料存放區**，使用[ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 例如: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>尋找與定義資料存放區

若要取得指定的資料存放區中目前的工作區註冊，請使用[ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

若要在指定的工作區中取得的所有資料存放區清單，請使用此程式碼：

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

若要定義不同的預設資料存放區目前的工作區，請使用[ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上傳與下載資料
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)並[ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)在下列範例中所述的方法是專用的而且完全一樣的[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)並[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類別。

### <a name="upload"></a>上傳

 使用 Python SDK 將目錄或個別檔案上傳至資料存放區。

上傳目錄至資料存放區 `ds`：

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` 可指定檔案共用 (或 Blob 容器) 中的位置以進行上傳。 該位置預設為 `None`，這表示會將資料上傳至根目錄。 `overwrite=True` 將會覆寫位於 `target_path` 的任何現有資料。

或者，透過資料存放區的 `upload_files()` 方法，將個別檔案清單上傳至資料存放區。

### <a name="download"></a>下載
同樣地，將資料從資料存放區下載至本機檔案系統。

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` 是儲存下載資料的本機目錄位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果 `prefix` 是 `None`，表示將會下載檔案共用 (或 Blob 容器) 的所有內容。

<a name="train"></a>
## <a name="access-datastores-during-training"></a>在定型期間存取資料存放區

一旦您提供您的資料存放區上定型的計算目標，您可以在定型執行 （例如，訓練或驗證資料） 中存取它，藉由直接將路徑傳遞給它訓練指令碼中的參數。

下表列出[ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)告知如何在執行期間使用的資料存放區的計算目標的方法。

方法|方法|描述|
----|-----|--------
掛接| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 若要計算目標上裝載資料存放區使用。
下載|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|使用您的資料存放區的內容下載到所指定的位置`path_on_compute`。 <br> 針對定型執行的內容，此下載發生之前執行。
上傳|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 從所指定的位置將檔案上傳使用`path_on_compute`到您的資料存放區。 <br> 針對定型執行的內容，此上傳會在您執行後執行。

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

若要參考特定的資料夾或檔案在您的資料存放區，並使其可在計算目標上，使用 資料存放區[ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-)函式。

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> 任何`ds`或是`ds.path`環境變數名稱的格式物件會解析`"$AZUREML_DATAREFERENCE_XXXX"`其值代表的目標計算上的掛接/下載路徑。 在目標電腦上的資料存放區路徑可能不會當做定型指令碼的執行路徑相同。

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>定型的計算和資料存放區的矩陣

下列的矩陣會顯示可用的資料存取功能，針對不同的訓練計算目標和資料存放區的案例。 深入了解[訓練 Azure Machine Learning 的計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。

|計算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 本機|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning Compute |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| 虛擬機器               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 資料傳輸                  |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)            |[ML&nbsp;管線](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;管線](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake Analytics       |N/A                                           |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 可能在其中重複性很高，大型的資料處理程序會執行更快的使用的案例`as_download()`而不是`as_mount()`; 這可以根據驗證。

### <a name="examples"></a>範例 

下列程式碼範例專屬於[ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別可用於在定型期間存取您的資料存放區。

此程式碼會建立使用定型指令碼 estimator `train.py`，從使用中定義的參數指定的來源目錄`script_params`，所有指定的訓練課程的計算目標。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

您也可以傳遞清單中的資料存放區估算器建構函式`inputs`參數，以裝載，或從您的資料來回複製。 此程式碼範例：
* 資料存放區中的所有內容都下載`ds1`到計算目標，您的訓練指令碼之前`train.py`執行
* 下載資料夾`'./foo'`在資料存放區`ds2`到計算目標之前`train.py`執行
* 將檔案上傳`'./bar.pkl'`最多資料存放區的計算目標`ds3`指令碼執行之後

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>後續步驟

* [將模型定型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
