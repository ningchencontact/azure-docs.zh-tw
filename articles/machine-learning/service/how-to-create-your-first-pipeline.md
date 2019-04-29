---
title: 建立、 執行及追蹤 ML 管線
titleSuffix: Azure Machine Learning service
description: 使用適用於 Python 的 Azure Machine Learning SDK 來建立及執行機器學習管線。 您可以使用管線建立和管理結合多個機器學習 (ML) 階段的工作流程。 這些階段包括資料準備、模型訓練、模型部署和推斷等階段。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2e6bc0fd9de4fdba1188b40c49ebf9459d684d38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819919"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>使用 Azure Machine Learning SDK 建立及執行機器學習管線

在本文中，您將了解如何使用 [Azure Machine Learning SDK](https://aka.ms/aml-sdk) 來建立、發佈、執行及追蹤[機器學習管線](concept-ml-pipelines.md)。  這些管線可協助您建立及管理將各種機器學習階段拼接在一起的工作流程。 管線的每個階段 (例如資料準備和模型訓練) 皆可包含一或多個步驟。

您建立的管線將可供您 Azure Machine Learning 服務[工作區](how-to-manage-workspace.md)的成員檢視。 

管線使用遠端計算目標，來計算和儲存與該管線相關的中繼資料和最終資料。 管線可以對支援的 [Azure 儲存體](https://docs.microsoft.com/azure/storage/)位置讀取和寫入資料。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必要條件

* [設定開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning SDK。

* 建立 [Azure Machine Learning 工作區](how-to-configure-environment.md#workspace)以保存您的所有管線資源。 

  ```python
  from azureml.core import Workspace
  
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>設定機器學習資源

建立執行管線所需的資源：

* 設定用來存取管線步驟中所需資料的資料存放區。

* 設定 `DataReference` 物件，以指向位於資料存放區中或可在資料存放區中存取的資料。

* 設定將作為您管線步驟執行位置的[計算目標](concept-azure-machine-learning-architecture.md#compute-target)。

### <a name="set-up-a-datastore"></a>設定資料存放區
資料存放區會儲存可供管線存取的資料。 每個工作區都有一個預設的資料存放區。 您可以註冊額外的資料存放區。 

在您建立工作區時，依預設會將 [Azure 檔案](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)連結至該工作區。 檔案儲存體是工作區的預設資料存放區，但您也可以使用 Blob 儲存體作為資料存放區。 若要深入了解，請參閱[決定何時使用 Azure 檔案、Azure Blob 或 Azure 磁碟](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

請將資料檔案或目錄上傳至資料存放區，以便能夠從您的管線存取這些資料檔案或目錄。 以下範例使用資料存放區的 Blob 儲存體版本：

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

管線會由一或多個步驟所組成。 步驟是在計算目標上執行的單位。 步驟可能會取用資料來源並產生「中繼」資料。 步驟可以建立資料 (例如模型)、含有模型和相依檔案的目錄，或是暫存資料。 此資料接著便可供管線中稍後的其他步驟使用。

### <a name="configure-data-reference"></a>設定資料參考

您剛建立一個可在管線中當作某個步驟的輸出來參考的資料來源。 管線中的資料來源會由 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 物件代表。 `DataReference` 物件會指向位於資料存放區中或可從資料存放區存取的資料。

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

中繼資料 (或步驟的輸出) 會由 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 物件代表。 `output_data1` 會產生為步驟的輸出，並用來作為一或多個未來步驟的輸入。 `PipelineData` 會在步驟之間導入資料相依性，並在管線中建立隱含的執行順序。

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>設定計算目標

在 Azure Machine Learning 中，__計算__一詞 (或__計算目標__) 係指會在您機器學習管線中執行計算步驟的機器或叢集。   如需完整的計算目標清單，以及了解如何建立這些目標並將其連結至您的工作區，請參閱[用於模型定型的計算目標](how-to-set-up-training-targets.md)。  不論您是要將模型定型還是執行管線步驟，建立和/或連結計算目標的程序都相同。 在您建立並連結計算目標之後，請在您的[管線步驟](#steps)中使用 `ComputeTarget` 物件。

> [!IMPORTANT]
> 不支援從遠端作業內部對計算目標執行管理作業。 由於機器學習管線會作為遠端作業提交，因此請勿從管線內對計算目標使用管理作業。

以下是為下列項目建立並連結計算目標的範例：

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute

您可以建立用來執行步驟的 Azure Machine Learning Compute。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 雲端中的 Apache Spark 型環境。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

使用 Azure Databricks 之前，請先建立其工作區。 若要建立這些資源，請參閱[在 Azure Databricks 執行 Spark 作業](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)文件。

若要連結 Azure Databricks 作為計算目標，請提供下列資訊：

* __Databricks 計算名稱__：您想要指派給這個計算資源的名稱。
* __Databricks 工作區名稱__：Azure Databricks 工作區的名稱。
* __Databricks 存取權杖__：用來向 Azure Databricks 進行驗證的存取權杖。 若要產生存取權杖，請參閱[驗證](https://docs.azuredatabricks.net/api/latest/authentication.html)文件。

下列程式碼示範如何搭配 Azure Machine Learning SDK 來連結 Azure Databricks 作為計算目標：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 雲端中的巨量資料分析平台。 它可與 Azure Machine Learning 管線搭配使用作為計算目標。

在使用 Azure Data Lake Analytics 之前，請先建立其帳戶。 若要建立此資源，請參閱[開始使用 Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 文件。

若要連結 Data Lake Analytics 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：您想要指派給這個計算資源的名稱。
* __資源群組__：包含 Data Lake Analytics 帳戶的資源群組。
* __帳戶名稱__：Data Lake Analytics 帳戶名稱。

下列程式碼示範如何連結 Data Lake Analytics 來作為計算目標：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning 管線只能使用 Data Lake Analytics 帳戶的預設資料存放區中所儲存的資料來運作。 如果您運作所需的資料位於非預設的存放區，則可以先使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 複製資料再進行訓練。

## <a id="steps"></a>建構您的管線步驟

建立計算目標並將其連結至您的工作區之後，您便已做好定義管線步驟的準備。 透過 Azure Machine Learning SDK，有許多內建的步驟可供使用。 是最基本的這些步驟[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)，在指定的計算目標中執行 Python 指令碼：

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

定義步驟之後，您必須使用這些步驟中的部分或全部步驟來建置管線。

> [!NOTE]
> 當您定義步驟或建置管線時，並不會將任何檔案或資料上傳到 Azure Machine Learning 服務。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

下列範例會使用稍早建立的 Azure Databricks 計算目標： 

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

如需詳細資訊，請參閱 <<c0> [ 步驟管線 azure 執行套件](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)並[管線類別](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)參考。

## <a name="submit-the-pipeline"></a>提交管線

當您提交管線時，Azure Machine Learning 服務會檢查每個步驟的相依性，並上傳您所指定之來源目錄的快照集。 如果未指定來源目錄，則會上傳目前的本機目錄。 快照集也會儲存為工作區中實驗的一部分。

> [!IMPORTANT]
> 若要防止檔案包含快照中，建立[.gitignore](https://git-scm.com/docs/gitignore)或`.amlignore`檔案的目錄中，並將檔案新增至它。 `.amlignore`檔案使用相同的語法，並做為模式[.gitignore](https://git-scm.com/docs/gitignore)檔案。 如果這兩個檔案存在，`.amlignore`檔有優先順序。
>
> 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshot)。

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

當您第一次執行管線時，Azure Machine Learning 會：

* 從與工作區相關的 Blob 儲存體，將專案快照集下載到計算目標。
* 建置與管線中的每個步驟相對應的 Docker 映像。
* 從容器登錄將每個步驟的 Docker 映像下載到計算目標。
* 如果步驟中指定了 `DataReference` 物件，就會掛接資料存放區。 如果不支援掛接，則會改為將資料複製到計算目標。
* 在步驟定義中指定的計算目標內執行步驟。 
* 建立步驟所指定的成品，例如記錄、stdout 和 stderr、計量及輸出。 這些成品會接著上傳到使用者的預設資料存放區，並保存在該處。

![以管線的形式執行實驗的圖表](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

如需詳細資訊，請參閱 <<c0> [ 實驗類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)參考。

## <a name="publish-a-pipeline"></a>發佈管線

您可以發佈管線以稍後使用不同的輸入來執行它。 若要讓已發佈之管線的 REST 端點接受參數，您必須在發佈管線前將其參數化。 

1. 若要建立管線參數，請使用 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 物件搭配預設值。

   ```python
   pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
   ```

2. 將這個 `PipelineParameter` 物件以參數形式新增至管線中的任何步驟，如下所示：

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
   ```

3. 發佈這個將在被叫用時接受參數的管線。

   ```python
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>執行已發佈的管線

所有已發佈的管線都有 REST 端點。 此端點可從外部系統 (例如非 Python 用戶端) 叫用管線執行。 此端點可在批次評分和重新訓練案例中，提供「受控的可重複性」。

若要叫用先前管線的執行，您需要 Azure Active Directory 驗證標頭權杖，如 [AzureCliAuthentication 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) \(英文\) 中所說明，或是於 [Azure Machine Learning 中的驗證](https://aka.ms/pl-restep-auth) \(英文\) 筆記本中取得詳細資料。

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>檢視結果

查看您所有管線的清單及其執行詳細資料：
1. 登入 [Azure 入口網站](https://portal.azure.com/)。  

1. [檢視您的工作區](how-to-manage-workspace.md#view)以尋找管線清單。
 ![機器學習管線清單](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 選取特定管線以查看執行結果。

## <a name="next-steps"></a>後續步驟
- 使用 [GitHub 上的這些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 來進一步探索機器學習管線。
- 閱讀 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 套件和 [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 套件的 SDK 參考說明。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
