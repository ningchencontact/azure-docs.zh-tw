---
title: 使用 Azure 儲存體儲存 Batch AI 作業輸入和輸出 | Microsoft Docs
description: 如何對於 Batch AI 使用 Azure 儲存體，以快速簡易的方式進行輸入和輸出檔案的雲端儲存
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1e9a4c6355c60b18bb78aae362c1e2f142e2d864
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407995"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>使用 Azure 儲存體儲存 Batch AI 作業輸入和輸出

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

本指南說明如何在執行作業時使用 Azure 儲存體來儲存輸入和輸出檔案。 Azure 儲存體是 Batch AI 支援的數個儲存體選項之一。 Batch AI 會與 Azure 儲存體整合，方法是將 Azure 儲存體系統裝載到 Batch AI 作業或叢集檔案系統，以便順利存取雲端中儲存的檔案。 

## <a name="introduction-to-azure-storage"></a>Azure 儲存體簡介

Azure 儲存體是 Microsoft 的雲端儲存體解決方案。 Batch AI 支援裝載 Batch AI 作業或叢集的 Azure Blob 容器和 Azure 檔案共用、以便能夠從工作存取檔案，就像是檔案在原生檔案系統中一樣。 Batch AI 會藉由 [blobfuse](https://github.com/Azure/azure-storage-fuse) 裝載 Azure Blob 容器，而且 Azure 檔案可透過 SMB 通訊協定共用。 如需有關 Azure 儲存體的詳細資訊，請參閱 [Azure 儲存體簡介](../storage/common/storage-introduction.md)。

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>在 Azure 儲存體中儲存資料集和輸入指令碼

您為 Batch AI 環境選擇 Azure 儲存體時，建議您在 Blob 容器中儲存輸入的檔案 (例如資料集)，這可達到更高的輸送量，而且您可以在檔案共用中儲存訓練輸出，這可支援串流 (允許在作業同時執行時讀取輸出記錄檔)。 

您使用 Azure 儲存體之前，您必須[建立 Azure 儲存體帳戶](../storage/common/storage-quickstart-create-account.md)。 Batch AI 支援從一般用途 v1 (GPv1) 和一般用途 v2 (GPv2) Azure 儲存體帳戶裝載磁碟。 Azure 儲存體帳戶可以存留多個 Blob 容器或檔案共用執行個體。 選擇要建立的儲存體帳戶類型時，請考慮您的成本和效能需求。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../storage/common/storage-account-overview.md)。 

若要建立 Blob 容器，並將資料集上傳至 Azure Blob 容器時，請選擇下列其中一種方法：
- [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md)可供透過網頁式 GUI 進行上傳。 若要上傳一小部分的檔案，Azure 入口網站可提供最簡單的操作方式。
- [Azure 儲存體 CLI](../storage/blobs/storage-quickstart-blobs-cli.md)可供透過命令列上傳 (支援目錄上傳)。 若要上傳檔案的目錄，請使用 `az storage blob upload-batch`。
- [其他技術](../storage/common/storage-moving-data.md)，包括使用應用程式 SDK。

同樣地，若要建立 Azure 檔案共用，請選擇下列其中一種方法：
- [Azure 入口網站](../storage/files/storage-how-to-use-files-portal.md)
- [Azure 儲存體 CLI](../storage/files/storage-how-to-use-files-cli.md)
- [其他技術](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>使用 Batch AI 自動儲存

或者，您也可以使用 `--use-auto-storage` 參數和 `az batchai cluster create`，建立包含 Azure 檔案共用和 Blob 容器的 Azure 儲存體帳戶 (並自動將這些磁碟區裝載到 Batch AI 叢集)。 如需詳細資訊，請參閱[這裡](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account)。

## <a name="mount-azure-storage"></a>裝載 Azure 儲存體 

### <a name="mount-volumes-to-a-job"></a>將磁碟區裝載到作業

裝載 Azure 儲存體磁碟區，即可透過對於每個作業建立的檔案系統進行存取。 因此，作業可以順利讀取雲端儲存體中的檔案，並且將檔案順利寫入到雲端儲存體，就像是檔案是本機檔案一樣。

若要將 Azure 儲存體磁碟區裝載到使用 Azure CLI 建立的作業，請在執行 `az batchai job create` 時使用 `job.json` 檔案中的 `mountVolumes` 屬性。 如需範例，請參閱 [Tensorflow GPU 分散式配方](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json)。 `mountVolumes` 的結構描述是：
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` 和 `azureBlobFileSystems` 是物件的陣列，這些物件代表要裝載的磁碟區。 預留位置的說明：

- <RELATIVE_MOUNT_PATH> - 磁碟區會裝載於此路徑。 例如，如果 `relativeMountPath` 是 `jobs`，磁碟區將位於 `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- <STORAGE_ACCOUNT_NAME> - 留存檔案共用或 Blob 容器的 Azure 儲存體帳戶名稱
- <FILE_SHARE_NAME> -檔案共用的名稱
- <BLOB_CONTAINER_NAME> -Blob 容器的名稱

若要使用 Azure Batch AI SDK 裝載 Azure 儲存體磁碟區，請在 `JobCreateParameters` 上設定 `mount_volumes` (Python) 或 `MountVolumes` (C#、Java) 屬性。 使用 Azure Batch AI SDK 裝載磁碟區時，您必須提供儲存體帳戶的認證。 檢視使用 [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python)、[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet) 和 [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable) 裝載磁碟區的結構描述。

### <a name="mount-volumes-to-a-cluster"></a>將磁碟區裝載到叢集

Batch AI 也支援將 Azure 儲存體磁碟區裝載到 Batch AI 叢集。 磁碟區裝載到叢集時，該叢集上執行的所有工作都可以使用裝載到該叢集的磁碟區。 雖然作業層級裝載提供最大的彈性 (允許每個作業有不同的裝載磁碟區)，但是簡單的情況只需要使用叢集層級裝載即可。

若要將 Azure 儲存體磁碟區裝載到使用 Azure CLI 建立的叢集，請在執行 `az batchai cluster create` 時使用 `cluster.json` 檔案中的 `mountVolumes` 屬性。 裝載叢集時的 `mountVolumes` 結構描述相同與裝載到作業時相同。 

同樣地，使用 Azure Batch AI SDK 裝載時，您可以使用 `ClusterCreateParameters` 上的 `mount_volumes` (Python) 或 `MountVolumes` (C#、Java) 屬性。 

## <a name="access-mounted-filesystem-in-a-job"></a>存取作業中裝載的檔案系統

### <a name="azbatchaijobmountroot-environment-variable"></a>$AZ_BATCHAI_JOB_MOUNT_ROOT 環境變數

在作業的執行環境中，可以使用 `$AZ_BATCHAI_JOB_MOUNT_ROOT` 環境變數 (如果您使用作業層級裝載) 存取已裝載的儲存體系統包含在其中的目錄。 如果您使用叢集層級裝載時，這個環境變數是 `$AZ_BATCHAI_MOUNT_ROOT`。 下列範例假設您使用作業層級裝載。

若要提供已裝載的磁碟區之中的資料路徑，請使用環境變數 `$AZ_BATCHAI_JOB_MOUNT_ROOT` 和裝載路徑。 例如，如果定型指令碼 `train.py` 已上傳到在相對裝載路徑 `scripts` 裝載的 Azure 檔案共用，檔案會位在 `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`。

如果您的定型指令碼需要知道路徑，您應該傳遞該路徑作為命令列引數。 例如，如果您將資料儲存在名為 `train_data` 的資料夾，而該資料夾位在裝載於路徑 `data` 的 Azure Blob 容器中，您可以向指令碼傳遞 `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` 作為命令列引數。 因此，您必須修改您的指令碼，以接受命令列引數。

### <a name="abbreviate-input-paths"></a>縮寫輸入路徑

若要將輸入的路徑縮寫為環境變數，請使用 `job.json` 檔案的 `inputDirectories` 屬性 (如果使用 Batch AI SDK，則使用 `models.JobCreateParameters.input_directories`)。 `inputDirectories` 的結構描述是：

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

指定的每個路徑都會放在名為 `$AZ_BATCHAI_INPUT_<ID>` 的環境變數中。 使用這個方法可以簡化輸入檔案或目錄的路徑。 例如，若要縮寫訓練指令碼的路徑：如果 `"id"` 是`"SCRIPT"`，而且 `"path"` 是 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`，則該路徑位於作業的執行環境中的 `$AZ_BATCHAI_INPUT_SCRIPT`。

如需詳細資訊，請參閱[這裡](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories)。

### <a name="abbreviate-output-paths"></a>縮寫輸出路徑

若要將輸出的路徑縮寫為環境變數，請使用 `job.json` 檔案的 `outputDirectories` 屬性 (如果使用 Batch AI SDK，則使用 `models.JobCreateParameters.output_directories`)。 使用這個方法可以簡化輸出檔案的路徑。 `outputDirectories` 的結構描述是：

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

指定的每個路徑都會放在名為 `$AZ_BATCHAI_OUTPUT_<ID>` 的環境變數中。 `pathPrefix` 會決定裝載的磁碟區來儲存輸出 (例如 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`)。 `pathSuffix` 會決定輸出的資料夾名稱 (例如 `"logs"`、`"checkpoints"`)。 輸出的實際路徑是 `pathPrefix`、`jobOutputDirectoryPathSegment` (對於每個工作自動產生) 和 `pathSuffix` 的串連。

如需詳細資訊，請參閱[這裡](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories)。

## <a name="retrieve-job-output-from-azure-storage"></a>從 Azure 儲存體擷取工作輸出

### <a name="use-azure-portal"></a>使用 Azure 入口網站

Azure 入口網站是使用 GUI 檔案總管來檢視工作輸出的便利方式。 不過，如果您想要檢視 Stdout 和 Stderr 中或 `outputDirectories` 的路徑中的輸出，檔案會放在 Azure 儲存體磁碟區中自動產生的路徑。 如需詳細資訊，請參閱下方。

### <a name="access-stdout-and-stderr-output"></a>存取 stdout 和 stderr 輸出

使用 `job.json` 的 `stdOutErrPathPrefix` 屬性會告知作業放置作業的執行記錄，以及 Stdout 和 Stderr 輸出。 例如，如果您將檔案共用裝載在相對裝載路徑 `outputs`，而且您指定 `stdOutErrPathPrefix` 成為 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`，則 Stdout 和 Stderr 作業輸出會位於該裝載磁碟區中的 `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr`。 這個自動產生的路徑可用來防止相同名稱的作業之間的輸出衝突。

如需詳細資訊，請參閱[這裡](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output)。

### <a name="list-the-output-files"></a>列出輸出檔案

您可以使用 Azure CLI 來透過 `az batchai job file list` 命令列出作業的可用輸出檔案。 例如，若要列出作業的標準輸出目錄中的檔案，請使用 `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`。

如需詳細資訊和範例，請參閱[這裡](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)。

### <a name="stream-output-files"></a>串流輸出檔案

您可以使用 Azure CLI 來透過 `az batchai job file stream` 命令串流檔案。 例如，若要檢視：
- Stdout：`az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr：`az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

如需詳細資訊和範例，請參閱[這裡](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)。

## <a name="next-steps"></a>後續步驟
- 若要深入了解與 Azure 儲存體互動的 CLI 命令，請參閱 [Azure Batch AI CLI 文件](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md)。
- 若要找出更多關於 Batch AI 的使用範例，包括裝載儲存體，以及讀取輸出檔案，請參閱 [Batch AI 的 Jupyter Notebook 配方](https://github.com/Azure/BatchAI)。
- 瀏覽裝載儲存體的其他選項，包括[裝載 NFS 伺服器](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)和[裝載您自己的 NFS、cifs 或 GlusterFS 叢集](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)