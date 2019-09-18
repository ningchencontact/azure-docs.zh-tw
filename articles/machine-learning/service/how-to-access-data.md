---
title: 存取 Azure 儲存體服務中的資料
titleSuffix: Azure Machine Learning
description: 瞭解如何使用資料存放區在使用 Azure Machine Learning 進行定型期間存取 Azure 儲存體服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: eaed6e7b0ea044ba39a1055ad14de13d5deb9b05
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035303"
---
# <a name="access-data-in-azure-storage-services"></a>存取 Azure 儲存體服務中的資料

 在本文中，您將瞭解如何透過 Azure Machine Learning 資料存放區，輕鬆地存取 Azure 儲存體服務中的資料。 資料存放區是用來儲存連線資訊（例如您的訂用帳戶識別碼和權杖授權）來存取您的儲存體，而不需要在腳本中硬式編碼該資訊。

本操作說明示範下列工作的範例:
* [註冊資料存放區](#access)
* [從工作區取得資料存放區](#get)
* [使用資料存放區上傳和下載資料](#up-and-down)
* [在定型期間存取資料](#train)

## <a name="prerequisites"></a>必要條件

若要使用資料存放區，必須先有[工作區](concept-workspace.md)。

從[建立新工作區](how-to-manage-workspace.md)，或擷取現有工作區開始：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>註冊資料存放區

所有的暫存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別上，並以 register_azure_ * 形式呈現。

下列範例會示範如何將 Azure Blob 容器或 Azure 檔案共用註冊為數據存放區。

+ 針對**Azure Blob 容器資料**存放區, 請使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ 針對**Azure 檔案共用資料存放區**, [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)請使用。 例如: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>儲存體指引

我們建議 Azure Blob 容器。 Standard 和 premium 儲存體都適用于 blob。 雖然較昂貴, 但我們建議使用高階儲存體, 因為輸送量速度較快, 可以改善定型執行的速度, 特別是當您針對大型資料集進行定型時。 如需儲存體帳戶的成本資訊, 請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從您的工作區取得資料存放區

若要取得在目前工作區中註冊的特定資料存放[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)區，請在資料存放區類別上使用靜態方法：

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要取得向指定工作區註冊的資料存放區清單，您可以使用工作`datastores`區物件上的屬性：

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

當您建立工作區時，azure Blob 容器和 azure 檔案共用會分別註冊到名為`workspaceblobstore`和`workspacefilestore`的工作區。 它們會儲存 Blob 容器的連線資訊，以及在附加至工作區的儲存體帳戶中布建的檔案共用。 `workspaceblobstore`設定為預設資料存放區。

取得工作區的預設資料存放區：

```Python
datastore = ws.get_default_datastore()
```

若要為目前的工作區定義不同的預設資料[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)存放區，請在工作區物件上使用方法：

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上傳 & 下載資料
下列[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)範例[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)中所述的和方法, 是針對[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類別的特定和運作方式。

### <a name="upload"></a>上傳

 使用 Python SDK 將目錄或個別檔案上傳至資料存放區。

上傳目錄至資料存放區 `datastore`：

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path`參數會指定要上傳之檔案共用（或 blob 容器）中的位置。 該位置預設為 `None`，這表示會將資料上傳至根目錄。 當`overwrite=True`任何現有的`target_path`資料遭到覆寫時。

或透過`upload_files()`方法，將個別檔案清單上傳至資料存放區。

### <a name="download"></a>下載

同樣地，將資料從資料存放區下載至本機檔案系統。

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path`參數是要將資料下載到其中的本機目錄位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果 `prefix` 是 `None`，表示將會下載檔案共用 (或 Blob 容器) 的所有內容。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>在定型期間存取您的資料

若要在定型期間存取資料，您可以透過資料存放區將資料從您的 Azure 儲存體服務下載或掛接至計算目標。

下表列出指示計算目標如何在執行期間使用資料存放區的方法。 

方式|方法|描述|
----|-----|--------
掛接| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 使用在計算目標上掛接資料存放區。
下載|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|使用將資料存放區的內容下載至所指定`path_on_compute`的位置。 <br> 此下載會在執行之前進行。
上傳|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| 使用將檔案從指定`path_on_compute`的位置上傳至您的資料存放區。 <br> 此上傳會在您執行之後發生。

若要參考資料存放區中的特定資料夾或檔案，並將其提供給計算目標，請[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-)使用資料存放區方法。

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Any `datastore` `"$AZUREML_DATAREFERENCE_XXXX"`或`datastore.path` object 會解析為格式的環境變數名稱，其值代表目標計算上的掛接/下載路徑。 目標計算上的資料存放區路徑可能不會與定型腳本的執行路徑相同。

### <a name="examples"></a>範例 

下列程式碼範例是在定型期間[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)存取資料的類別特有的。 

`script_params`這是包含 entry_script 之參數的字典。 您可以使用它傳入資料存放區，並描述計算目標上的資料應如何提供。 深入瞭解我們的端對端[教學](tutorial-train-models-with-aml.md)課程。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

您也可以將資料存放區清單傳遞至估計工具`inputs`的「檢查點」參數，以在資料存放區中裝載或複製資料。 此程式碼範例:
* 在您的定型腳本`datastore1` `train.py`執行之前，將中的所有內容下載到計算目標
* 在執行之前`'./foo'` `datastore2` ，將中的資料夾下載至計算`train.py`目標
* 在您的腳本執行`datastore3`之後，將檔案從計算目標上傳至`'./bar.pkl'`

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>計算和資料存放區矩陣

資料存放區目前支援將連接資訊儲存至下列矩陣中所列的儲存體服務。 此矩陣會顯示不同計算目標和資料存放區案例的可用資料存取功能。 深入瞭解 Azure Machine Learning 的[計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。

|計算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 本機|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning Compute |[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管線](concept-ml-pipelines.md)|[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管線](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| 虛擬機器               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 資料傳輸                  |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)            |[ML&nbsp;管線](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;管線](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake 分析       |N/A                                           |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 在某些情況下, 可能會有高度反復的大型資料處理程式`as_download()`以較`as_mount()`快的速度執行, 而不是, 這可以用 experimentally 驗證。

### <a name="accessing-source-code-during-training"></a>在定型期間存取原始程式碼

Azure blob 儲存體的輸送量速度高於 Azure 檔案共用，而且會調整為以平行方式啟動的大量作業。 基於這個理由，我們建議您將執行設定為使用 blob 儲存體來傳送原始程式碼檔案。

下列程式碼範例會在執行設定中指定要用於來來源程式代碼傳輸的 blob 資料存放區。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在評分期間存取資料

Azure Machine Learning 提供數種方式來使用您的模型進行評分。 其中一些方法不會提供資料存放區的存取權。 使用下表瞭解哪些方法可讓您在計分期間存取資料存放區:

| 方法 | 資料存放區存取 | 描述 |
| ----- | :-----: | ----- |
| [批次預測](how-to-run-batch-predictions.md) | ✔ | 以非同步方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 web 服務。 |
| [IoT Edge 模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署到 IoT Edge 裝置。 |

針對 SDK 不提供資料存放區存取權的情況, 您可以使用相關的 Azure SDK 來建立自訂程式碼來存取資料。 例如, 使用適用于[Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)來存取儲存在 blob 中的資料。


## <a name="next-steps"></a>後續步驟

* [將模型定型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
