---
title: 存取 Azure 儲存體 services 中的資料
titleSuffix: Azure Machine Learning
description: 瞭解如何使用資料存放區安全地連線到 Azure 儲存體服務，並使用 Azure Machine Learning 進行定型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540941"
---
# <a name="access-data-in-azure-storage-services"></a>存取 Azure 儲存體服務中的資料
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何透過 Azure Machine Learning 資料存放區，輕鬆地在 Azure 儲存體服務中存取您的資料。 資料存放區是用來儲存連接資訊，例如您的訂用帳戶識別碼和權杖授權。 當您使用資料存放區時，您可以存取儲存體，而不需要在腳本中硬編碼連接資訊。 

您可以從[這些 Azure 儲存體解決方案](#matrix)建立資料存放區。 針對不支援的儲存體解決方案，以及在機器學習實驗期間儲存資料輸出成本，建議您[將資料移](#move)至支援的 Azure 儲存體解決方案。 

## <a name="prerequisites"></a>必要條件
您需要：
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 具有[azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 azure 儲存體帳戶。

- [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning studio](https://ml.azure.com/)的存取權。

- Azure Machine Learning 工作區。
  
  請[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區：

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>建立並註冊資料存放區

當您將 Azure 儲存體解決方案註冊為數據存放區時，會自動在特定工作區中建立該資料存放區。 您可以使用 Python SDK 或 Azure Machine Learning studio 建立資料存放區，並將其註冊至工作區。

### <a name="python-sdk"></a>Python SDK

所有的暫存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別上，並具有 `register_azure_*`的表單。

您可以使用[Azure 入口網站](https://portal.azure.com)來尋找填入 `register()` 方法所需的資訊：

1. 在左窗格中選取 [**儲存體帳戶**]，然後選擇您要註冊的儲存體帳戶。 
2. 如需帳戶名稱、容器和檔案共用名稱之類的資訊，請移至 [**總覽**] 頁面。 如需驗證資訊（如帳戶金鑰或 SAS 權杖），請移至 [**設定**] 窗格上的 [**存取金鑰**]。 

> [!IMPORTANT]
> 如果您的儲存體帳戶位於虛擬網路中，則只支援建立 Azure blob 資料存放區。 若要將您的工作區存取權授與您的儲存體帳戶，請將參數 `grant_workspace_access` 設定為 `True`。

下列範例示範如何將 Azure blob 容器、Azure 檔案共用和 Azure SQL 資料註冊為數據存放區。

#### <a name="blob-container"></a>Blob 容器

若要將 Azure blob 容器註冊為數據存放區，請使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

下列程式碼會建立 `blob_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用所提供的帳戶金鑰，存取 `my-account-name` 儲存體帳戶上的 `my-container-name` blob 容器。

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>檔案共用

若要將 Azure 檔案共用註冊為數據存放區，請使用[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

下列程式碼會建立 `file_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用所提供的帳戶金鑰，存取 `my-account-name` 儲存體帳戶上的 `my-fileshare-name` 檔案共用。

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>SQL 資料

針對 Azure SQL 資料存放區，請使用[register_azure_sql_database （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-)來註冊使用 SQL 驗證或服務主體許可權連線到 Azure SQL 資料庫的認證資料存放區。 

若要透過 SQL 驗證進行註冊：

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

若要透過服務主體進行註冊：

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>儲存體指引

我們建議使用 Azure blob 容器。 Standard 和 premium 儲存體都適用于 blob。 雖然高階儲存體的成本較高，但其更快速的輸送量速度可能會改善定型執行的速度，特別是當您針對大型資料集進行定型時。 如需儲存體帳戶成本的相關資訊，請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

在 Azure Machine Learning studio 中的幾個步驟中建立新的資料存放區：

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。
1. 在左窗格的 [**管理**] 底下選取 [**資料存放區**]。
1. 選取 [ **+ 新增資料**存放區]。
1. 完成新資料存放區的表單。 表單會根據您選取的 Azure 儲存體類型和驗證類型，以智慧方式自行更新。
  
您可以在  [Azure 入口網站](https://portal.azure.com)上找到填入表單所需的資訊。 在左窗格中選取 [**儲存體帳戶**]，然後選擇您要註冊的儲存體帳戶。 [**總覽**] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。 如需驗證專案（如帳戶金鑰或 SAS 權杖），請移至 [**設定**] 窗格上的 [**帳戶金鑰**]。

下列範例示範當您建立 Azure blob 資料存放區時，表單看起來的樣子： 
    
![新資料存放區的表單](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從您的工作區取得資料存放區

若要取得在目前工作區中註冊的特定資料存放區，請在 `Datastore` 類別上使用[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)靜態方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要取得向指定工作區註冊的資料存放區清單，您可以使用工作區物件上的[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)屬性：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

當您建立工作區時，會自動向工作區註冊 Azure blob 容器和 Azure 檔案共用。 它們分別命名為 `workspaceblobstore` 和 `workspacefilestore`。 它們會儲存 blob 容器的連線資訊，以及在附加至工作區的儲存體帳戶中布建的檔案共用。 `workspaceblobstore` 容器會設定為預設資料存放區。

若要取得工作區的預設資料存放區，請使用以下這一行：

```Python
datastore = ws.get_default_datastore()
```

若要為目前的工作區定義不同的預設資料存放區，請在工作區物件上使用[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)方法：

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上傳及下載資料

下列範例中所述的[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)和[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)方法，都是[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類別的特定和運作方式。

### <a name="upload"></a>上傳

使用 Python SDK 將目錄或個別檔案上傳至資料存放區：

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` 參數會指定檔案共用（或 blob 容器）中要上傳的位置。 預設為 `None`，因此資料會上傳到根。 如果 `overwrite=True`，則會覆寫 `target_path` 的任何現有資料。

您也可以透過 `upload_files()` 方法，將個別檔案清單上傳至資料存放區。

### <a name="download"></a>下載

將資料從資料存放區下載到本機檔案系統：

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 參數是要下載資料的本機目錄位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果 `None``prefix`，則會下載檔案共用（或 blob 容器）的所有內容。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>在定型期間存取您的資料

> [!IMPORTANT]
> 我們現在建議使用[Azure Machine Learning 資料集](how-to-create-register-datasets.md)，以在定型中存取您的資料。 資料集提供將表格式資料載入 pandas 或 Spark 資料框架的功能。 資料集也能讓您從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫下載或掛接任何格式的檔案。 [深入瞭解如何使用資料集進行定型](how-to-train-with-datasets.md)。

下表列出指示計算目標如何在執行期間使用資料存放區的方法： 

路|方法|說明|
----|-----|--------
掛接| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 使用在計算目標上掛接資料存放區。 裝載資料存放區時，您的資料存放區的所有檔案都可供您的計算目標存取。
下載|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|使用將資料存放區的內容下載至 `path_on_compute`所指定的位置。 <br><br> 此下載會在執行之前進行。
上傳|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| 使用，將檔案從 `path_on_compute` 所指定的位置上傳至您的資料存放區。 <br><br> 這個上傳會在執行之後發生。

若要參考資料存放區中的特定資料夾或檔案，並將其提供給計算目標，請使用資料存放區[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-)方法：

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 任何指定的 `datastore` 或 `datastore.path` 物件都會解析為 `"$AZUREML_DATAREFERENCE_XXXX"`格式的環境變數名稱。 此名稱的值代表計算目標上的掛接/下載路徑。 計算目標上的資料存放區路徑可能不是定型腳本的執行路徑。

### <a name="examples"></a>範例 

我們建議使用[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別，在定型期間存取資料。 

`script_params` 變數是一個字典，其中包含要 `entry_script`的參數。 使用它來傳入資料存放區，並描述如何在計算目標上提供資料。 深入瞭解[端對端教學](tutorial-train-models-with-aml.md)課程。

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

您也可以將資料存放區清單傳入 `Estimator` 的 `inputs` 參數，以在資料存放區中裝載或複製資料。 此程式碼範例：
* 在執行 `train.py` 訓練腳本之前，將 `datastore1` 中的所有內容下載到計算目標。
* 在執行 `train.py` 之前，將 `datastore2` 中的 `'./foo'` 資料夾下載到計算目標。
* 在您的腳本執行之後，將 `'./bar.pkl'` 檔案從計算目標上傳至 `datastore3`。

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
如果您想要使用 `RunConfig` 物件進行定型，您需要設定[`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)物件。 

下列程式碼示範如何使用估計管線中的 `DataReference` 物件。 如需完整範例，請參閱[此筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)。

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>計算和資料存放區矩陣

資料存放區目前支援將連接資訊儲存至下列矩陣中所列的儲存體服務。 此矩陣會針對不同的計算目標和資料存放區案例，顯示可用的資料存取功能。 [深入瞭解 Azure Machine Learning 的計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。

|計算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 地方|[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning Compute |[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [Machine Learning 管線](concept-ml-pipelines.md)|[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [Machine Learning 管線](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| 虛擬機器               |[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Azure HDInsight                      |[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 資料轉送                  |[Machine Learning 管線](concept-ml-pipelines.md)                                               |N/A                                           |[Machine Learning 管線](concept-ml-pipelines.md)            |[Machine Learning 管線](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Machine Learning 管線](concept-ml-pipelines.md)                                              |N/A                                           |[Machine Learning 管線](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Machine Learning 管線](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure Data Lake Analytics       |N/A                                           |N/A                                           |[Machine Learning 管線](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 在某些情況下，使用 `as_download()` 而不是 `as_mount()`，可能會有高度反復的大型資料處理程式執行速度較快。 您可以驗證此 experimentally。

### <a name="accessing-source-code-during-training"></a>在定型期間存取原始程式碼

Azure Blob 儲存體的輸送量速度高於 Azure 檔案共用，而且會調整為以平行方式啟動的大量作業。 基於這個理由，我們建議您將執行設定為使用 Blob 儲存體來傳送原始程式碼檔案。

下列程式碼範例會指定執行設定中的 blob 資料存放區，以用於來來源程式代碼傳輸：

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在評分期間存取資料

Azure Machine Learning 提供數種方式來使用您的模型進行評分。 其中一些方法不會提供資料存放區的存取權。 使用下表瞭解哪些方法可讓您在計分期間存取資料存放區：

| 方法 | 資料存放區存取 | 說明 |
| ----- | :-----: | ----- |
| [批次預測](how-to-run-batch-predictions.md) | ✔ | 以非同步方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 web 服務。 |
| [Azure IoT Edge 模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署到 IoT Edge 裝置。 |

針對 SDK 不提供資料存放區存取權的情況，您可以使用相關的 Azure SDK 來存取資料，以建立自訂程式碼。 例如，適用于[Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)是用戶端程式庫，可讓您用來存取儲存在 blob 或檔案中的資料。

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>將資料移至支援的 Azure 儲存體解決方案

Azure Machine Learning 支援從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫存取資料。 如果您使用不支援的儲存體，建議您使用 Azure Data Factory 將資料移至支援的 Azure 儲存體解決方案。 將資料移至支援的儲存體，可協助您節省機器學習實驗期間的資料輸出成本。 

Azure Data Factory 提供具有超過80個預先建立連接器的有效率且彈性的資料傳輸，且不需額外費用。 這些連接器包括 Azure 資料服務、內部部署資料來源、Amazon S3 和 Redshift，以及 Google BigQuery。 [遵循逐步指南，使用 Azure Data Factory 來移動資料](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

## <a name="next-steps"></a>後續步驟

* [將模型定型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
