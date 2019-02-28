---
title: Azure Batch AI 的現況為何？ | Microsoft Docs
description: 了解 Azure Batch AI 的現況及 Azure Machine Learning 服務計算選項。
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: fb1114e94c227ce6787532c6059186399d0f57f0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961344"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI 的現況為何？

**Azure Batch AI 服務將在 3 月淘汰。** Batch AI 的大規模定型與計分功能目前已在 [Azure Machine Learning 服務](../machine-learning/service/overview-what-is-azure-ml.md)中提供，其已於 2018 年 12 月 4 日正式推出。

除了許多其他機器學習功能，Azure Machine Learning 服務還包括雲端式受控計算目標來對機器學習服務模型進行定型、部署和評分。 此計算目標稱為 [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。 [立即開始移轉並使用它](#migrate)。 您可以透過其 [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、命令列介面及 [Azure 入口網站](../machine-learning/service/quickstart-get-started.md)來與 Azure Machine Learning 服務進行互動。

## <a name="support-timeline"></a>支援時間表

| 日期 | Batch AI 服務支援詳細資料 |
| ---- |-----------------|
| 2018 年&nbsp;12 月&nbsp;14 日| 您可以和以前一樣使用現有的 Azure Batch AI 訂用帳戶。 不過，您無法建立**新的訂用帳戶**，且我們不會進行任何新的投資。|
| 2019 年&nbsp;3 月&nbsp;31 日 | 在這個日期之後，現有的 Batch AI 訂用帳戶將無法運作。 |

## <a name="how-does-azure-machine-learning-service-compare"></a>Azure Machine Learning 服務有何差異？
這是用來訓練、部署、自動化及管理機器學習模型的雲端服務，而這一切都在雲端所提供的廣泛規模下進行。 透過[此概觀中的 Azure Machine Learning 服務](../machine-learning/service/overview-what-is-azure-ml.md)取得大致的了解。
 

一般的模型開發生命週期包括資料準備、訓練與測試和部署階段。 此端對端週期可使用機器學習管線來協調。

![流程圖](./media/overview-what-happened-batch-ai/lifecycle.png)


[深入了解服務的運作方式及其主要概念](../machine-learning/service/concept-azure-machine-learning-architecture.md)。 模型訓練工作流程中有許多概念都與 Batch AI 中現有的概念類似。 

具體而言，以下是兩者之間的對應關係：
 
|Batch AI 服務|  Azure Machine Learning 服務|
|:--:|:---:|
|工作區|工作區|
|叢集|   `AmlCompute` 類型的計算|
|檔案伺服器|資料存放區|
|實驗|實驗|
|工作|執行 (允許巢狀執行)|
 
以下是相同資料表的另一個檢視，可協助您進一步檢視這兩項服務：
 
**Batch AI 階層**
![流程圖](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Azure Machine Learning 服務階層**
![流程圖](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>平台功能
Azure Machine Learning 服務導入了一組絕佳的新功能，包括從訓練到部署的端對端堆疊，可讓您直接用於 AI 開發，而不需要管理任何 Azure 資源。 下表比較兩項服務之間的訓練功能支援。

|功能|BatchAI 服務|Azure Machine Learning 服務|
|-------|:-------:|:-------:|
|VM 大小選擇 |CPU/GPU    |CPU/GPU。 也支援用於推斷的 FPGA|
|AI Ready 叢集 (驅動程式、Docker 等等)|  yes |yes|
|節點準備| yes|    否|
|作業系統系列選擇   |部分    |否|
|專用和 LowPriority VM  |yes    |yes|
|自動調整   |yes    |是 (依照預設)|
|自動調整的等待時間  |否 |yes|
|SSH    |yes|   yes|
|叢集層級掛接 |是 (FileShare、Blob、NFS、自訂)   |是 (掛接或下載您的資料存放區)|
|分散式訓練|  yes |yes|
|作業執行模式|    VM 或容器|    容器|
|自訂容器映像|    yes |yes|
|任何工具組    |yes    |是 (執行 Python 指令碼)|
|JobPreparation|    yes |尚未提供|
|作業層級掛接 |是 (FileShare、Blob、NFS、自訂)   |是 (FileShare、Blob)|
|作業監視     |透過 GetJob|    透過執行歷程記錄 (更豐富的資訊、以自訂執行階段推送更多計量)|
|擷取作業記錄和檔案/模型 |   透過 ListFile 和儲存體 API  |透過成品服務|
 |Tensorboard 的支援   |否|    yes|
|VM 系列層級配額 |yes    |是 (您先前的容量可轉移使用)|
 
除了上表所列項目外，Azure Machine Learning 服務中還有有一些 BatchAI 過去未支援的功能。

|功能|BatchAI 服務|Azure Machine Learning 服務|
|-------|:-------:|:-------:|
|環境準備    |否 |是 (Conda 準備和上傳至 ACR)|
|超參數調整  |否|    yes|
|模型管理   |否 |yes|
|運算化/部署| 否  |透過 AKS 和 ACI|
|資料準備   |否 |yes|
|計算目標    |Azure VM  |本機、BatchAI (作為 AmlCompute)、DataBricks、HDInsight|
|自動化 Machine Learning |否|    yes|
|管線  |否 |yes|
|Batch 評分  |yes    |yes|
|入口網站/CLI 支援|    yes |yes|


### <a name="programming-interfaces"></a>程式設計介面

下表列出每項服務可用的各種程式設計介面。
    
|功能|BatchAI 服務|Azure Machine Learning 服務|
|-------|:-------:|:-------:|
|SDK    |Java、C#、Python、Nodejs   |Python (兩項服務針對一般架構均執行以設定為基礎和以估算器為基礎的 SDK)|
|CLI    |yes    |尚未提供|
|Azure 入口網站   |yes    |是 (作業提交除外)|
|REST API   |yes    |在，分散於微服務之間|




## <a name="why-migrate"></a>為何要移轉？

從預覽 BatchAI 服務升級至正式運作的 Azure Machine Learning 服務，可透過估算器和資料存放區等更容易使用的概念讓您獲得更好的體驗。 此外，也可保證 GA 層級的 Azure 服務 SLA 和客戶支援。

Azure Machine Learning 服務也導入了像是自動化 ML、超參數調整和 ML 管線等新功能，可運用在大部分的大規模 AI 工作負載中。 無須切換至不同服務即可實作定型模型的能力，有助於完成從資料準備 (使用資料準備 SDK) 到運算化和模型監視的資料科學迴圈。

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>我應如何移轉？

依照此移轉指南中的步驟對應這兩項服務之間的命令之前，建議您花點時間，透過 Azure Machine Learning 服務的[文件](../machine-learning/service/overview-what-is-azure-ml.md) (包括 [Python 教學課程](../machine-learning/service/tutorial-train-models-with-aml.md)) 來熟悉此服務。

若要避免中斷您的應用程式並受益於最新功能，請在 2019 年 3 月 31 日之前採取下列步驟：

1. 建立 Azure Machine Learning 服務工作區並開始使用：
    + [以 Python 為基礎的快速入門](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [以 Azure 入口網站為基礎的快速入門](../machine-learning/service/quickstart-get-started.md)

1. 設定用來為模型定型的 [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。

1. 更新您的指令碼以使用 Azure Machine Learning Compute。


### <a name="sdk-migration"></a>SDK 移轉

Azure Machine Learning 服務中目前的 SDK 支援來自於數個 Python SDK。 主要 SDK 大約每兩週會更新一次，可以使用下列命令從 PyPi 安裝：

```python
pip install --upgrade azureml-sdk[notebooks]
```

請使用這些[快速入門指示](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)設定您的環境並安裝 SDK

在您開啟將核心指向相關 conda 環境的 Jupyter Notebook 後，這兩項服務中的命令會有下列對應方式：


#### <a name="create-a-workspace"></a>建立工作區
在 BatchAI 中使用 configuration.json 初始化工作區的概念，類似於在 Azure ML 中使用組態檔的概念。

在 **Batch AI** 中，您採用下列方式：

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

此外，您也可以藉由指定如下的組態參數，直接建立工作區

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

請參閱 [SDK 參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)以深入了解 AML 工作區類別。


#### <a name="create-a-compute-cluster"></a>建立計算叢集
Azure Machine Learning 支援多個計算目標，其中有些由服務管理，有些則可連結至您的工作區，例如 HDInsight 叢集或遠端 VM。 深入了解各種[計算目標](../machine-learning/service/how-to-set-up-training-targets.md)。 建立 BatchAI 計算叢集的概念，與在 Azure ML 中建立 AmlCompute 叢集相對應。 建立 Amlcompute 時會採用計算組態，這類似於在 BatchAI 中傳遞參數。 要注意的一點是，自動調整在 AmlCompute 叢集上預設為開啟，而在 BatchAI 中則預設為關閉。

在 **Batch AI** 中，您採用下列方式：

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

請參閱 [SDK 參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)以深入了解 AMLCompute 類別。 請注意，在上述組態中，只有 vm_size 和 max_nodes 是必要屬性，其餘屬性 (如 VNets) 僅供進階叢集設定之用。


#### <a name="monitoring-status-of-your-cluster"></a>監視叢集的狀態
此作業在 Azure ML 中更為簡單直接，顯示如下。

在 **Batch AI** 中，您採用下列方式：

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>取得儲存體帳戶的參考
在 Azure ML 中，資料儲存體 (如 Blob) 的概念已透過 DataStore 物件而獲得簡化。 根據預設，Azure ML 工作區會建立儲存體帳戶，但您也可以在建立工作區的過程中連結自己的儲存體。 

在 **Batch AI** 中，您採用下列方式：

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

請參閱 [Azure ML 服務文件](../machine-learning/service/how-to-access-data.md#access)以深入了解如何註冊其他儲存體帳戶，或取得其他已註冊資料存放區的參考。


#### <a name="downloading-and-uploading-data"></a>下載和上傳資料 
使用任一項服務時，您都可以使用上述的資料存放區參考輕鬆地將資料上傳至儲存體帳戶。 在 BatchAI 中，我們也可將訓練指令碼部署為檔案共用的一部分，但在 Azure ML 中，您將可了解如何將其指定為工作組態的一部分。

在 **Batch AI** 中，您採用下列方式：

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


在 **Azure Machine Learning 服務**中，可以嘗試：

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>建立實驗
如前所述，Azure ML 在實驗方面的概念與 BatchAI 相似。 每個實驗可以有個別的執行，類似於我們在 BatchAI 中的作業。 Azure ML 也允許每個父執行之下有個別子執行的階層。

在 **Batch AI** 中，您採用下列方式：

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>提交作業
建立實驗之後，您可以透過幾種不同的方式提交執行。 在此範例中，我們將嘗試使用 TensorFlow 建立深度學習模型，並且使用 Azure ML 估算器來建立。 [估算器](../machine-learning/service/how-to-train-ml-models.md)就是基礎執行組態的簡單包裝函式，可方便您提交執行，目前僅支援 Pytorch 和 TensorFlow。 透過資料存放區的概念，您也將發現掛接路徑的指定會變得多麼容易 

在 **Batch AI** 中，您採用下列方式：

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

在 BatchAI 中，作業本身的提交是透過 create 函式執行的。

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

如需此訓練程式碼片段 (包括我們已上傳至前述檔案共用的 mnist_replica.py 檔案) 的完整資訊，請透過 [BatchAI 範例 Notebook Github 存放庫](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed)取得。

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

如需此訓練程式碼片段 (包括 tf_mnist_replica.py 檔案) 的完整資訊，請透過 [Azure ML 範例 Notebook Github 存放庫](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server)取得。 資料存放區本身可掛接在個別節點上，或者，可在節點本身下載訓練資料。 如需關於在估算器中參考資料存放區的詳細資訊，請參閱 [Azure ML 服務文件](../machine-learning/service/how-to-access-data.md#access)。 

在 Azure ML 中提交執行的作業可透過 submit 函式來完成。

```python
run = experiment.submit(estimator)
print(run)
```

您可以透過另一種方式為執行指定參數，也就是使用執行組態 – 特別適合用來定義自訂訓練環境。 您可以在[範例 AmlCompute Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb) 中找到更多詳細資料。 

#### <a name="monitor-your-run"></a>監視您的執行
提交執行後，您可以等候執行完成，或是在 Azure ML 中使用可直接從程式碼叫用的 Jupyter 小工具加以監視。 您也可以藉由重複存取工作區中的各種實驗和每個實驗內的個別執行，來提取任何先前執行的內容。

在 **Batch AI** 中，您採用下列方式：

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


在 **Azure Machine Learning 服務**中，可以嘗試：

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

以下快照集顯示小工具在您的 Notebook 中載入並即時查看記錄的情形：![小工具監視圖表](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="editing-a-cluster"></a>編輯叢集
刪除叢集相當簡單。 此外，如果您想要將叢集調整為更高數目的節點，或增加相應減少叢集之前的閒置等待時間，Azure ML 也可讓您從 Notebook 中更新叢集。 我們不允許變更叢集本身的 VM 大小，這需要在後端執行有效的新部署。

在 **Batch AI** 中，您採用下列方式：
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

在 **Azure Machine Learning 服務**中，可以嘗試：

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>支援

BatchAI 預定在 3 月 31 日淘汰，且目前已封鎖對服務註冊新的訂用帳戶，除非透過支援申請例外處理而列於允許清單中。  如有任何疑問，或是在移轉到 Azure Machine Learning 服務的過程中有任何意見，請透過 [Azure Batch AI 訓練預覽版](mailto:AzureBatchAITrainingPreview@service.microsoft.com)與我們連絡。

Azure Machine Learning 服務已是正式運作的服務。 這表示它附有經認可的 SLA 和各種支援方案可供選擇。

透過 BatchAI 服務或透過 Azure Machine Learning 服務使用 Azure 基礎結構的定價應該是相同的，因為在這兩種情況下，我們都只收取基礎計算的費用。 如需詳細資訊，請參閱[定價計算機](https://azure.microsoft.com/pricing/details/machine-learning-service/)。

請在 [Azure 入口網站](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all)上檢視這兩項服務的可用區域。


## <a name="next-steps"></a>後續步驟

+ 請參閱 [Azure Machine Learning 服務概觀](../machine-learning/service/overview-what-is-azure-ml.md)。

+ 使用 Azure Machine Learning 服務[為模型定型設定計算目標](../machine-learning/service/how-to-set-up-training-targets.md)。

+ 檢閱 [Azure 藍圖](https://azure.microsoft.com/updates/)以了解其他 Azure 服務更新。
