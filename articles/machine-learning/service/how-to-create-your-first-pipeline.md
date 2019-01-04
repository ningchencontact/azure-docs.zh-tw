---
title: 建立、 執行及追蹤 ML 管線
titleSuffix: Azure Machine Learning service
description: 使用適用於 Python 的 Azure Machine Learning SDK 來建立及執行機器學習管線。  管線可用來建立及管理將機器學習 (ML) 階段 (例如資料準備、模型訓練、模型部署及推斷) 拼接在一起的工作流程。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 8478b6760921f4641cd214b1ff19cae9757b6d7e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269034"
---
# <a name="create-and-run-a-machine-learning-pipeline-using-azure-machine-learning-sdk"></a>使用 Azure Machine Learning SDK 來建立及執行機器學習管線

在本文中，您將了解如何使用 [Azure Machine Learning SDK](https://aka.ms/aml-sdk)來建立、發佈、執行及追蹤[機器學習管線](concept-ml-pipelines.md)。  這些管線可協助您建立及管理將各種機器學習階段拼接在一起的工作流程。 管線的每個階段 (例如資料準備和模型訓練) 皆可包含一或多個步驟。

您建立的管線將可供您 Azure Machine Learning 服務[工作區](how-to-manage-workspace.md)的成員檢視。 

管線使用遠端計算目標，來計算和儲存與該管線相關的中繼資料和最終資料。  管線可以對支援的 [Azure 儲存體](https://docs.microsoft.com/azure/storage/)位置讀取和寫入資料。

>[!Note]
>如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必要條件

* [設定開發環境](how-to-configure-environment.md)以安裝 Azure Machine Learning SDK。

* 建立 [Azure Machine Learning 工作區](how-to-configure-environment.md#workspace)以保存您的所有管線資源。 

 ```python
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
資料存放區會儲存可供管線存取的資料。  每個工作區都有一個預設的資料存放區。 您可以註冊額外的資料存放區。 

當您建立工作區時，預設會有一個 [Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和一個 [Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)附加至該工作區。  Azure 檔案儲存體是工作區的「預設資料存放區」，但您也可以使用 Blob 儲存體作為資料存放區。  深入了解 [Azure 儲存體選項](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

請將資料檔案或目錄上傳至資料存放區，以便能夠從您的管線存取這些資料檔案或目錄。  以下範例使用資料存放區的 Blob 儲存體版本：

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

管線會由一或多個步驟所組成。  步驟是在計算目標上執行的單位。  步驟可能會取用資料來源並產生「中繼」資料。 步驟可以建立資料 (例如模型)、含有模型和相依檔案的目錄，或是暫存資料。  此資料接著便可供管線中稍後的其他步驟使用。

### <a name="configure-data-reference"></a>設定資料參考

您剛建立一個可在管線中當作某個步驟的輸出來參考的資料來源。 管線中的資料來源會由 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 物件代表。 `DataReference` 物件會指向位於資料存放區中或可從資料存放區存取的資料。

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

中繼資料 (或步驟的輸出) 會由 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 物件代表。 `output_data1` 會產生為步驟的輸出，並用來作為一或多個未來步驟的輸入。  `PipelineData` 會在步驟之間導入資料相依性，並在管線中建立隱含的執行順序。

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>設定計算

在 Azure Machine Learning 中，計算 (或計算目標) 係指將在您機器學習管線中執行計算步驟的機器或叢集。 例如，您可以建立用來執行步驟的 Azure Machine Learning Compute。

```python
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

## <a name="construct-your-pipeline-steps"></a>建構您的管線步驟

現在您已做好定義管線步驟的準備。 透過 Azure Machine Learning SDK，有許多內建的步驟可供使用。 這些步驟中最基本的就是 `PythonScriptStep`，此步驟會在指定的計算目標中執行 Python 指令碼。

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

定義步驟之後，您需使用這些步驟中的部分或全部步驟來建置管線。

>[!NOTE]
>當您定義步驟或建置管線時，並不會將任何檔案或資料上傳到 Azure Machine Learning 服務。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>提交管線

當您提交管線時，系統會檢查每個步驟的相依性，然後將指定為來源目錄的資料夾快照集上傳到 Azure Machine Learning 服務。  如果未指定來源目錄，則會上傳目前的本機目錄。

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

當您第一次執行管線時：

* 會從與工作區相關的 Blob 儲存體，將專案快照集下載到計算目標。
* 針對管線中的每個步驟，都會建置一個對應的 Docker 映像。
* 會從容器登錄將每個步驟的 Docker 映像下載到計算目標。
* 如果步驟中已指定 `DataReference` 物件，就會掛接資料存放區。 如果不支援掛接，則會改為將資料複製到計算目標。
* 步驟會在步驟定義中所指定的計算目標內執行。 
* 會建立步驟所指定的成品，例如記錄、stdout 和 stderr、計量及輸出。 這些成品會接著上傳到使用者的預設資料存放區並保存在該處。

![以管線的形式執行實驗](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>發佈管線

您可以發佈管線以稍後使用不同的輸入來執行它。 若要讓已發佈之管線的 REST 端點接受參數，必須在發佈管線前先將管線參數化。 

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

所有已發佈的管線都有 REST 端點，可從外部系統 (例如非 Python 用戶端) 叫用管線執行。 此端點可在批次評分和重新訓練案例中，提供一個「受控的可重複性」方式。

若要叫用先前管線執行，您需要一個 Azure Active Directory 驗證標頭權杖，如 [AzureCliAuthentication 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)中所述

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>檢視結果

查看您所有管線的清單及其執行詳細資料：
1. 登入 [Azure 入口網站](https://portal.azure.com/)。  

1. [檢視您的工作區](how-to-manage-workspace.md#view-a-workspace)以尋找管線清單。
 ![機器學習管線清單](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 選取特定管線以查看執行結果。

## <a name="next-steps"></a>後續步驟
- 使用 [GitHub 上的這些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 來進一步探索機器學習管線。
- 閱讀 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 套件和 [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 套件的 SDK 參考說明。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
