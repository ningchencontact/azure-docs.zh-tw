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
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319318"
---
# <a name="access-data-from-your-datastores"></a>從您的資料存放區存取資料

 在 Azure Machine Learning 服務中, 資料存放區是與計算位置無關的機制, 用來存取儲存體, 而不需要變更您的原始程式碼。 無論您撰寫訓練程式碼以取得路徑作為參數, 或將資料存放區直接提供給估計工具, Azure Machine Learning 工作流程都會確保您的資料存放區位置可供存取, 並可供您的計算內容使用。

本操作說明示範下列工作的範例:
* [選擇資料存放區](#access)
* [取得資料](#get)
* [將資料上傳並下載至資料存放區](#up-and-down)
* [在定型期間存取資料存放區](#train)

## <a name="prerequisites"></a>先決條件

若要使用資料存放區，必須先有[工作區](concept-workspace.md)。

從[建立新工作區](setup-create-workspace.md#sdk)，或擷取現有工作區開始：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>選擇資料存放區

您可以使用預設資料存放區或攜帶您自己的資料存放區。

### <a name="use-the-default-datastore-in-your-workspace"></a>使用工作區中的預設資料存放區

 每個工作區都有一個已註冊的預設資料存放區, 可供您立即使用。

取得工作區的預設資料存放區：

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>向工作區註冊您自己的資料存放區

如果您有現有的 Azure 儲存體，可以在工作區中將它註冊為資料存放區。 

<a name="store"></a>

####  <a name="storage-guidance"></a>儲存體指引

我們建議 blob 儲存體和 blob 資料存放區。 Standard 和 premium 儲存體都適用于 blob。 雖然較昂貴, 但我們建議使用高階儲存體, 因為輸送量速度較快, 可以改善定型執行的速度, 特別是當您針對大型資料集進行定型時。 如需儲存體帳戶的成本資訊, 請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

>[!NOTE]
> Azure Machine Learning 服務支援其他類型的資料存放區, 這在特定案例中可能會很有用。 例如, 如果您需要使用儲存在資料庫中的資料進行定型, 您可以使用 AzureSQLDatabaseDatastore 或 AzurePostgreSqlDatastore。 如需可用的資料存放區類型, 請參閱[此表格](#matrix)。

#### <a name="register-your-datastore"></a>註冊您的資料存放區
所有的暫存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別上, 並以 register_azure_ * 形式呈現。

下列範例會示範如何將 Azure Blob 容器或 Azure 檔案共用註冊為數據存放區。

+ 針對**Azure Blob 容器資料**存放區, 請使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ 針對**Azure 檔案共用資料存放區**, [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)請使用。 例如: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>尋找 & 定義資料存放區

若要取得在目前工作區中註冊的指定資料[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)存放區, 請使用:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

若要取得指定工作區中所有資料存放區的清單, 請使用下列程式碼:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

若要為目前的工作區定義不同的預設資料[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)存放區, 請使用:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上傳 & 下載資料
下列[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)範例[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)中所述的和方法, 是針對[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類別的特定和運作方式。

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

一旦將資料存放區提供給訓練計算目標之後, 您就可以在定型回合 (例如, 定型或驗證資料) 期間進行存取, 只要將其路徑傳遞為定型腳本中的參數即可。

下表列出指示計算[`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)目標如何在執行期間使用資料存放區的方法。

方式|方法|描述|
----|-----|--------
掛接| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 使用在計算目標上掛接資料存放區。
下載|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|使用將資料存放區的內容下載至所指定`path_on_compute`的位置。 <br> 針對定型執行內容, 此下載會在執行之前進行。
上傳|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 使用將檔案從指定`path_on_compute`的位置上傳至您的資料存放區。 <br> 針對定型執行內容, 此上傳會在您執行之後發生。

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

若要參考資料存放區中的特定資料夾或檔案, 並將其提供給計算目標, 請使用[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-)資料存放區的函式。

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> 任何`ds` `"$AZUREML_DATAREFERENCE_XXXX"`或`ds.path`物件都會解析成格式的環境變數名稱, 其值代表目標計算上的掛接/下載路徑。 目標計算上的資料存放區路徑可能不會與定型腳本的執行路徑相同。

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>訓練計算和資料存放區矩陣

下列矩陣會顯示不同訓練計算目標和資料存放區案例的可用資料存取功能。 深入瞭解 Azure Machine Learning 的[定型計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。

|計算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 本機|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning Compute |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管線](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管線](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| 虛擬機器               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 資料傳輸                  |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)            |[ML&nbsp;管線](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;管線](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake 分析       |N/A                                           |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 在某些情況下, 可能會有高度反復的大型資料處理程式`as_download()`以較`as_mount()`快的速度執行, 而不是, 這可以用 experimentally 驗證。

### <a name="examples"></a>範例 

下列程式碼範例專屬於在定型[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)期間存取資料存放區的類別。

這段程式碼會使用定型腳本, `train.py`從指定的來原始目錄使用中`script_params`定義的參數, 來建立估計工具, 其全都位於指定的定型計算目標上。

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

您也可以將資料存放區清單傳遞至估計工具`inputs`的「檢查點」參數, 以裝載或複製到資料存放區或從中複製。 此程式碼範例:
* 在您的定型腳本`ds1` `train.py`執行之前, 將資料存放區中的所有內容下載到計算目標
* 在執行之前`'./foo'` `ds2` ,將資料存放區中的資料夾下載到計算目標`train.py`
* 在您的腳本執行之後, 將檔案`ds3` 從計算目標上傳到資料存放區`'./bar.pkl'`

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>取得評分期間的存取權資料存放區

Azure Machine Learning 服務提供數種方式來使用您的模型進行評分。 其中一些方法不會提供資料存放區的存取權。 使用下表瞭解哪些方法可讓您在計分期間存取資料存放區:

| 方法 | 資料存放區存取 | 描述 |
| ----- | :-----: | ----- |
| [批次預測](how-to-run-batch-predictions.md) | ✔ | 以非同步方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 web 服務。 |
| [IoT Edge 模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署到 IoT Edge 裝置。 |

針對 SDK 不提供資料存放區存取權的情況, 您可以使用相關的 Azure SDK 來建立自訂程式碼來存取資料。 例如, 使用適用于[Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)來存取儲存在 blob 中的資料。

## <a name="next-steps"></a>後續步驟

* [將模型定型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
