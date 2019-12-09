---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926950"
---
`inferenceconfig.json` 檔中的專案會對應至[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別的參數。 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 本機檔案的路徑，其中包含要為映射執行的程式碼。 |
| `runtime` | `runtime` | 選用。 要用於影像的執行時間。 目前支援的執行時間為 `spark-py` 和 `python`。 如果已設定 `environment`，則會忽略這種情況。 |
| `condaFile` | `conda_file` | 選用。 本機檔案的路徑，其中包含要用於影像的 Conda 環境定義。  如果已設定 `environment`，則會忽略這種情況。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 選用。 本機檔案的路徑，其中包含設定映射時要執行的其他 Docker 步驟。  如果已設定 `environment`，則會忽略這種情況。|
| `sourceDirectory` | `source_directory` | 選用。 資料夾的路徑，其中包含要建立映射的所有檔案。 |
| `enableGpu` | `enable_gpu` | 選用。 是否要在映射中啟用 GPU 支援。 GPU 映射必須用於 Azure 服務，例如 Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 預設值為 False。 如果已設定 `environment`，則會忽略這種情況。|
| `baseImage` | `base_image` | 選用。 要當做基底映射使用的自訂映射。 如果未提供基底映射，則映射會以提供的執行時間參數為基礎。 如果已設定 `environment`，則會忽略這種情況。 |
| `baseImageRegistry` | `base_image_registry` | 選用。 包含基底映射的映射登錄。 如果已設定 `environment`，則會忽略這種情況。|
| `cudaVersion` | `cuda_version` | 選用。 要為需要 GPU 支援的映射安裝的 CUDA 版本。 GPU 映射必須用於 Azure 服務，例如 Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 支援的版本為9.0、9.1 和10.0。 如果已設定 `enable_gpu`，預設值為9.1。 如果已設定 `environment`，則會忽略這種情況。 |
| `description` | `description` | 影像的描述。 如果已設定 `environment`，則會忽略這種情況。  |
| `environment` | `environment` | 選用。  Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

下列 JSON 是與 CLI 搭配使用的範例推斷設定：

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

下列 JSON 是一個範例推斷設定，它會使用具有特定版本的現有 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)搭配 CLI 來使用：

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

下列 JSON 是一個範例推斷設定，它會使用具有最新版本的現有 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)來與 CLI 搭配使用：

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
