---
title: Azure Machine Learning 的 Git 整合
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 如何與本機 Git 存放庫整合。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377508"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning 的 Git 整合

[Git](https://git-scm.com/)是受歡迎的版本控制系統，可讓您共用和共同作業您的專案。 提交定型作業以 Azure Machine Learning 時，如果定型檔案儲存在本機 git 儲存機制中，則會在定型過程中追蹤存放庫的相關資訊。

由於 Azure Machine Learning 會從本機 git 存放庫追蹤資訊，因此它不會系結至任何特定的中央儲存機制。 您的存放庫可以從 GitHub、GitLab、Bitbucket、Azure DevOps 或任何其他與 git 相容的服務複製。

## <a name="how-does-git-integration-work"></a>Git 整合如何運作？

當您從 Python SDK 或 Machine Learning CLI 提交定型回合時，定型模型所需的檔案會上傳至您的工作區。 如果您的開發環境可以使用 `git` 命令，則上傳程式會使用它來檢查檔案是否儲存在 git 存放庫中。 若是如此，則您 git 存放庫中的資訊也會上傳做為定型執行的一部分。 這項資訊會儲存在定型回合的下列屬性中：

| 屬性 | 描述 |
| ----- | ----- |
| `azureml.git.repository_uri` | 您的存放庫複製來源的 URI。 |
| `mlflow.source.git.repoURL` | 您的存放庫複製來源的 URI。 |
| `azureml.git.branch` | 提交執行時的作用中分支。 |
| `mlflow.source.git.branch` | 提交執行時的作用中分支。 |
| `azureml.git.commit` | 針對執行所提交之程式碼的認可雜湊。 |
| `mlflow.source.git.commit` | 針對執行所提交之程式碼的認可雜湊。 |
| `azureml.git.dirty` | `True`，如果認可已變更，則為，否則，`false`。 |

此資訊會針對使用估計工具、機器學習管線或腳本執行的執行進行傳送。

如果您的定型檔案不在開發環境的 git 存放庫中，或 `git` 命令無法使用，則不會追蹤與 git 相關的資訊。

## <a name="view-the-logged-information"></a>查看記錄的資訊

Git 資訊儲存在定型執行的屬性中。 您可以使用 Azure 入口網站、Python SDK 和 CLI 來查看此資訊。 

### <a name="azure-portal"></a>Azure Portal

1. 從  [Azure 入口網站](https://portal.azure.com)中，選取您的工作區。
1. 選取 [__實驗__]，然後選取其中一個實驗。
1. 從 [__執行編號__] 資料行中選取其中一個執行。
1. 選取 [__記錄__]，然後展開 [__記錄__] 和 [ __azureml__ ] 專案。 選取以 __### @ no__t-2azure__開頭的連結。

記錄的資訊包含類似下列 JSON 的文字：

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

提交定型回合之後，會傳回[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)物件。 此物件的 `properties` 屬性包含已記錄的 git 資訊。 例如，下列程式碼會抓取認可雜湊：

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

您可以使用 `az ml run` CLI 命令，從執行中取出屬性。 例如，下列命令會在名為 `train-on-amlcompute` 的實驗中，傳回最後一次執行的屬性：

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

如需詳細資訊，請參閱[az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) reference 檔。

## <a name="next-steps"></a>後續步驟

* 如需如何在 Visual Studio Code 中使用 Azure Machine Learning 進行定型的逐步解說，請參閱[教學課程：使用 Azure Machine Learning 來定型模型](tutorial-train-models-with-aml.md)。
* 如需如何在本機編輯、執行及偵錯工具代碼的逐步解說，請參閱[Python hello world 教學](https://code.visualstudio.com/docs/Python/Python-tutorial)課程。
