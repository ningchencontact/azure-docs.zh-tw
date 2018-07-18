---
title: Azure 快速入門 - 透過 Batch AI 執行 CNTK 訓練 - Azure CLI |Microsoft Docs
description: 快速了解如何使用 Azure CLI 透過 Batch AI 執行 CNTK 訓練作業
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 06/14/2018
ms.author: Alexander.Yukhanov
ms.openlocfilehash: eb00c1d4ec74b5268a1497b11087030ab6a86e5a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294068"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>使用 Azure CLI 執行 CNTK 訓練作業

Azure CLI 2.0 可讓您建立及管理 Batch AI 資源 - 建立/刪除 Batch AI 檔案伺服器和叢集，以及提交/終止/刪除/監視訓練作業。

本快速入門說明如何使用 Microsoft Cognitive Toolkit (CNTK) 建立 GPU 叢集及執行訓練作業。

訓練指令碼 [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) 可在 Batch AI GitHub 頁面取得。 此指令碼可在手寫數字的 MNIST 資料庫上訓練卷積神經網路。

正式 CNTK 範例已修改成接受訓練資料集的位置以及透過命令列引數的輸出目錄位置。

## <a name="quickstart-overview"></a>快速入門概觀

* 使用 `nc6` 建立單一節點 GPU 叢集 (採用 `Standard_NC6` VM 大小)；
* 建立儲存體帳戶以儲存作業輸入和輸出；
* 建立包含 `logs` 和 `scripts` 兩個資料夾的 Azure 檔案共用，以儲存作業輸出和訓練指令碼；
* 建立 Azure Blob 容器 `data` 來儲存訓練資料；
* 將訓練指令碼和訓練資料部署到已建立的檔案共用和容器；
* 將作業設定為在叢集的節點上掛接 Azure 檔案共用和 Azure Blob 容器，讓它們可在 `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`、`$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` 和 `$AZ_BATCHAI_JOB_MOUNT_ROOT/data` 以一般檔案系統形式提供。
`AZ_BATCHAI_JOB_MOUNT_ROOT` 是由 Batch AI 針對作業設定的環境變數。
* 藉由串流作業的標準輸出來監視其執行情況；
* 作業完成之後，檢查其輸出和所產生的模型；
* 最後，刪除所有已配置的資源。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶 - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 存取具有 0.3 版或更高版本 Batchai 模組的 Azure CLI 2.0。 您可以使用 [Azure Cloud Shell](../cloud-shell/overview.md) 中提供的 Azure CLI 2.0，或遵循[這些指示](/cli/azure/install-azure-cli?view=azure-cli-latest)在本機進行安裝。

  如果您使用 Cloud Shell，請將工作目錄切換到 `/usr/$USER/clouddrive`，因為主目錄沒有空的空間：

  ```azurecli
  cd /usr/$USER/clouddrive
  ```

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是一個邏輯容器，可供部署與管理 Azure 資源。 下列命令會在美國東部位置建立新的資源群組 `batchai.quickstart`：

```azurecli
az group create -n batchai.quickstart -l eastus
```
## <a name="create-batch-ai-workspace"></a>建立 Batch AI 工作區

下列命令會在資源群組中建立 Batch AI 工作區。 Batch AI 工作區是所有 Batch AI 資源類型的最上層集合：

```azurecli
az batchai workspace create -g batchai.quickstart -n quickstart
```

## <a name="create-gpu-cluster"></a>建立 GPU 叢集

下列命令會使用 Ubuntu 資料科學虛擬機器 (DSVM) 作為作業系統映像，在工作區中建立單一節點 GPU 叢集 (VM 大小為 Standard_NC6)：

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -w quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM 可讓您在 Docker 容器中執行任何訓練作業，以及直接在 VM 上執行最廣受使用的深入學習架構。

`--generate-ssh-keys` 選項會指示 Azure CLI 產生私密和公用 ssh 金鑰 (如果您還沒有這些金鑰)。 您可以使用目前的使用者名稱和所產生的 ssh 金鑰來存取叢集節點。

如果您使用 Cloud Shell，建議您將 ~/.ssh 資料夾備份到某個永久儲存體。

範例輸出︰
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "myuser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

下列命令會在用來建立 Batch AI 叢集的相同資源群組中建立儲存體帳戶。 您可以使用儲存體帳戶來儲存作業輸入和輸出。 使用唯一的儲存體帳戶名稱更新此命令。

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```


## <a name="deploy-data"></a>部署資料

### <a name="download-the-training-script-and-training-data"></a>下載訓練指令碼和訓練資料

* 從[這個位置](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D)，將前置處理過的 MNIST 資料庫下載並解壓縮到目前的資料夾中。

對於 GNU/Linux 或 Cloud Shell：

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

請注意，如果 GNU/Linux 發行版本都沒有 `unzip`，您可能需要加以安裝。

* 將 [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) 範例指令碼下載到目前的資料夾中：

對於 GNU/Linux 或 Cloud Shell：

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

### <a name="create-azure-file-share-and-deploy-the-training-script"></a>建立 Azure 檔案共用和部署訓練指令碼

下列命令會建立 Azure 檔案共用 `scripts` 和 `logs`，並將訓練指令碼複製到 `scripts` 共用內的 `cntk` 資料夾中：

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

### <a name="create-a-blob-container-and-deploy-training-data"></a>建立 Blob 容器和部署訓練資料

下列命令會建立名為 `data` 的 Azure Blob 容器，並將訓練資料複製到 `mnist_cntk` 資料夾中：

```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

## <a name="submit-training-job"></a>提交訓練作業

### <a name="create-a-batch-ai-experiment"></a>建立 Batch AI 實驗

實驗是相關 Batch AI 作業的邏輯容器。 請使用下列命令在您的工作區中建立實驗：

```azurecli
az batchai experiment create -g batchai.quickstart -w quickstart -n quickstart
```

### <a name="prepare-job-configuration-file"></a>準備作業組態檔

建立建立含有下列內容的訓練作業組態檔 `job.json`。 請以您的儲存體帳戶名稱進行更新。

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<YOUR_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

此組態檔指定：

* `nodeCount` - 作業所需的節點數目 (本快速入門中為 1)
* `cntkSettings` - 指定訓練指令碼和命令列引數的路徑。 命令列引數包含訓練資料的路徑，以及用來儲存所產生模型的目的地路徑。 `AZ_BATCHAI_OUTPUT_MODEL` 是 Batch AI 根據輸出目錄組態所設定的環境變數 (請參閱下文)
* `stdOutErrPathPrefix` - Batch AI 建立目錄用以存放作業輸出和記錄的路徑
* `outputDirectories` - Batch AI 所要建立之輸出目錄的集合。 針對每個目錄，Batch AI 會建立名稱為 `AZ_BATCHAI_OUTPUT_<id>` 的環境變數，其中 `<id>` 是目錄識別碼
* `mountVolumes` - 在作業執行期間要掛接的檔案系統清單。 檔案系統已掛接在 `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>` 之下。 `AZ_BATCHAI_JOB_MOUNT_ROOT` 是由 Batch AI 設定的環境變數
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` - 在作業提交期間要透過電腦上的 `--storage-account-name parameter` 或 `AZURE_BATCHAI_STORAGE_ACCOUNT` 環境變數來指定的儲存體帳戶名稱。

### <a name="submit-the-job"></a>提交作業

```azurecli
az batchai job create -n cntk_python_1 -c nc6 -g batchai.quickstart -w quickstart -e quickstart  -f job.json --storage-account-name <storage account name>
```

範例輸出︰
```
{
  "caffe2Settings": null,
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-06-14T22:22:57.543000+00:00",
  "customMpiSettings": null,
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "startTime": "2018-06-14T22:22:59.838000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2018-06-14T22:22:59.838000+00:00",
  "horovodSettings": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/f2d6ff09-7549-4e1a-8cd8-ec839f042a61",
  "jobPreparation": null,
  "mountVolumes": {
    "azureBlobFileSystems": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null
    }
  ],
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-06-14T22:22:58.625000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "danlep0614b",
  "schedulingPriority": "normal",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/workspaces/experiments/jobs"
}

```

## <a name="monitor-job-execution"></a>監視作業執行

訓練指令碼會在標準輸出目錄的 `stderr.txt` 檔案中報告訓練進度。 請使用下列命令來監視進度：

```azurecli
az batchai job file stream -j cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart -f stderr.txt
```

範例輸出︰
```
File found with URL "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.62% * 10000
```

串流會在作業完成 (成功或失敗) 時停止。

## <a name="inspect-generated-model-files"></a>檢查所產生的模型檔案

作業會將產生的模型檔案儲存在 `id` 屬性等於 `MODEL` 的輸出目錄中。 請使用下列命令列出模型檔案並取得下載 URL：

```azurecli
az batchai job file list -j cntk_python_1 -w quickstart -e quickstart -g batchai.quickstart -d MODEL
```

範例輸出︰
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

或者，請使用 Azure 入口網站或 Azure 儲存體總管來檢查產生的檔案。 為了區分來自不同作業的輸出，Batch AI 會為每個作業建立唯一的資料夾結構。 請使用所提交作業的 `jobOutputDirectoryPathSegment` 屬性，尋找輸出所在資料夾的路徑：

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart --query jobOutputDirectoryPathSegment
```

範例輸出︰
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，請使用下列命令來刪除資源群組和所有已配置的資源：

```azurecli
az group delete -n batchai.quickstart -y
```
