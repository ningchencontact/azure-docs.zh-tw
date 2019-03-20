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
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: e6e1b304b90b37c93bed22bcb720a646680ee083
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223609"
---
# <a name="access-data-from-your-datastores"></a>從您的資料存放區存取資料

資料存放區可讓您與互動，並存取您的資料是否在執行您的程式碼在本機的計算叢集上，或在虛擬機器上。 在本文中，您會了解 Azure Machine Learning 工作流程，以確保您的資料存放區都可以存取及開放給您的計算內容。

此操作說明會顯示下列工作的範例：
* [選擇資料存放區](#access)
* [取得資料](#get)
* [上傳和下載資料至資料存放區](#up-and-down)
* [在定型期間存取資料存放區](#train)

## <a name="prerequisites"></a>必要條件

若要使用資料存放區，您需要[工作區](concept-azure-machine-learning-architecture.md#workspace)第一次。 

從[建立新工作區](quickstart-create-workspace-with-python.md)，或擷取現有工作區開始：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

或者，[遵循本 Python 快速入門](quickstart-create-workspace-with-python.md)若要建立您的工作區，並開始使用 SDK。

<a name="access"></a>

## <a name="choose-a-datastore"></a>選擇資料存放區

您可以使用的預設資料存放區，或自備。

### <a name="use-the-default-datastore-in-your-workspace"></a>在 您的工作區中使用的預設資料存放區

不是需要建立或設定儲存體帳戶，因為每個工作區的預設資料存放區。 您可以使用資料存放區立即因為它已註冊的工作區中。 

取得工作區的預設資料存放區：
```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>註冊您自己的資料存放區與工作區
如果您有現有的 Azure 儲存體，可以在工作區中將它註冊為資料存放區。   上的所有註冊的方法都都[ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別，並有表單 register_azure_ *。 

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

+ 針對**Azure 檔案共用資料存放區**，使用[ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 例如︰ 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
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
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

一旦您對您的資料存放區可以使用遠端計算，則您可以直接將路徑傳遞給它訓練指令碼中的參數定型執行 （例如，訓練或驗證資料） 期間存取它。

下表列出常見[ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)使資料存放區在遠端計算中可用的方法。

# #

方法|方法|描述
----|-----|--------
掛接| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 使用遠端計算上掛接的資料存放區。 資料存放區的預設模式。
下載|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|用來從所指定的位置下載資料`path_on_compute`您資料存放區的遠端電腦上。
上傳|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 若要將資料上傳至您的資料存放區的根目錄中所指定的位置使用`path_on_compute`。

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

若要參照資料存放區中的特定資料夾或檔案，請使用資料存放區的 [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) 函式。

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> 任何 `ds` 或 `ds.path` 物件都會解析為 `"$AZUREML_DATAREFERENCE_XXXX"`，其值代表遠端計算上的掛接/下載路徑。 在遠端計算的資料存放區路徑可能不會當做定型指令碼的執行路徑相同。

### <a name="examples"></a>範例 

下列說明範例特有[ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別可用於在定型期間存取您的資料存放區。


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

由於`as_mount()`是預設模式針對資料存放區中，您可能也會直接傳遞`ds`到`'--data_dir'`引數。

傳入的資料存放區清單，估算器建構函式或`inputs`參數，以裝載，或從您的資料來回複製。 此程式碼範例：
* 資料存放區中的所有內容都下載`ds1`訓練指令碼的遠端電腦`train.py`執行
* 下載資料夾`'./foo'`在資料存放區`ds2`之前的遠端電腦`train.py`執行
* 將檔案上傳`'./bar.pkl'`從遠端計算，最多資料存放區`ds3`指令碼執行之後

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>後續步驟

* [將模型定型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
