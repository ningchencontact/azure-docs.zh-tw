---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348544"
---
`inferenceconfig.json`檔中的專案會對應至[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別的參數。 下表描述 JSON 檔中的實體與方法的參數之間的對應:

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 本機檔案的路徑, 其中包含要為映射執行的程式碼。 |
| `runtime` | `runtime` | 要用於影像的執行時間。 目前支援的執行時間為 ' spark-.py ' 和 ' python '。 |
| `condaFile` | `conda_file` | 選擇性。 本機檔案的路徑, 其中包含要用於影像的 conda 環境定義。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 選擇性。 本機檔案的路徑, 其中包含設定映射時要執行的其他 Docker 步驟。 |
| `sourceDirectory` | `source_directory` | 選擇性。 資料夾的路徑, 其中包含要建立映射的所有檔案。 |
| `enableGpu` | `enable_gpu` | 選擇性。 是否要在映射中啟用 GPU 支援。 GPU 映射必須用於 Microsoft Azure 服務。 例如, Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 預設值為 False。 |
| `baseImage` | `base_image` | 選擇性。 要當做基底映射使用的自訂映射。 如果未指定基底映射, 則會根據指定的執行時間參數來使用基底映射。 |
| `baseImageRegistry` | `base_image_registry` | 選擇性。 包含基底映射的映射登錄。 |
| `cudaVersion` | `cuda_version` | 選擇性。 要為需要 GPU 支援的映射安裝的 CUDA 版本。 GPU 映射必須用於 Microsoft Azure 服務。 例如, Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 支援的版本為9.0、9.1 和10.0。 如果設定了 ' enable_gpu ', 則預設為 ' 9.1 '。 |
| `description` | `description` |  此影像的描述。 |

下列 JSON 是與 CLI 搭配使用的範例推斷設定:

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