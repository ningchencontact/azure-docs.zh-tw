---
title: Azure 快速入門 - 透過 Batch AI 執行 CNTK 訓練 - Python |Microsoft Docs
description: 快速了解如何使用 Python SDK 透過 Batch AI 執行 CNTK 訓練作業
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>使用 Azure Python SDK 執行 CNTK 訓練作業

本快速入門詳述如何使用 Python SDK 透過 Batch AI 服務執行 Microsoft Cognitive Toolkit (CNTK) 訓練作業。 

在此範例中中，您可使用手寫映像的 MNIST 資料庫，在單一節點 GPU 叢集上訓練卷積神經網路 (CNN)。 

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶 - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

* Azure Python SDK - 請參閱[安裝指示](/python/azure/python-sdk-azure-install)

* Azure 儲存體帳戶 - 請參閱[如何建立 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)

* Azure Active Directory 服務主體 - 請參閱[如何使用 CLI 建立服務主體](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* 使用 Azure Cloud Shell 或 Azure CLI 為您的訂用帳戶註冊 Batch AI 資源提供者一次。 提供者註冊可能需要 15 分鐘的時間。

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>設定認證
在 Python 指令碼中建立這些參數，以取代您自己的值：

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

最佳做法是將所有認證儲存在個別的組態檔中，該檔案不會顯示在此範例中。 請參閱[配方](https://github.com/Azure/BatchAI/tree/master/recipes)以實作組態檔。 

## <a name="authenticate-with-batch-ai"></a>使用 Batch AI 進行驗證

您需要使用 Azure Active Directory 進行驗證，才能夠存取 Azure Batch AI。 以下程式碼可使用您的服務主體認證和訂用帳戶 ID 向服務進行驗證 (建立 `BatchAIManagementClient` 物件)：

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>建立資源群組

Batch AI 叢集和作業都是 Azure 資源，必須放入 Azure 資源群組中。 下列程式碼片段會建立一個資源群組：

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>準備 Azure 檔案共用
為了方便說明，本快速入門會使用 Azure 檔案共用來裝載學習作業的訓練資料和指令碼。 

1. 建立名為 batchaiquickstart 的檔案共用。

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. 在名為 mnistcntksample 的共用中建立一個目錄 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. 下載[範例套件](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)並且解壓縮。 將內容上傳到您將在其中執行 Python 指令碼的目錄。

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>建立 GPU 叢集

建立 Batch AI 叢集。 在此範例中，叢集包含單一 STANDARD_NC6 VM 節點。 此 VM 大小有一個 NVIDIA K80 GPU。 在名為 azurefileshare 的資料夾掛接檔案共用。 GPU 計算節點上此資料夾的完整路徑為 $AZ_BATCHAI_MOUNT_ROOT/azurefileshare。

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>取得叢集狀態

使用下列命令監視叢集狀態： 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

上述程式碼會列印基本叢集配置資訊，如下所示：

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

此叢集會在節點都已配置並完成準備時就緒 (請參閱 `nodeStateCounts` 屬性)。 如果發生錯誤，`errors` 屬性會包含錯誤描述。

## <a name="create-training-job"></a>建立訓練作業

在叢集備妥之後，設定並提交學習作業。 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>監視作業
您可以使用下列命令檢查作業的狀態： 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

輸出如下：`Job state: running`。

`executionState` 包含作業的目前執行狀態：
* `queued`：作業正在等候叢集節點變成可用
* `running`：作業正在執行
* `succeeded` (或 `failed`)：作業已完成，且 `executionInfo` 包含結果的相關詳細資料
 
## <a name="list-stdout-and-stderr-output"></a>列出 stdout 和 stderr 輸出
使用下列命令列出 stdout 及 stderr 記錄檔的連結：

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>刪除資源

使用下列命令刪除作業：
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

使用下列命令刪除叢集：
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Azure Python SDK，在 Batch AI 叢集上執行 CNTK 訓練作業。 若要深入了解如何使用 Batch AI 搭配不同的工具組，請參閱[訓練配方](https://github.com/Azure/BatchAI)。
