---
title: 啟動、 監視及取消在 Python 中的定型執行
titleSuffix: Azure Machine Learning service
description: 了解如何啟動、 設定狀態的標記，並組織您的機器學習服務實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: a67ac07c26063b380bda2b8cb2b6a02677e7f816
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656195"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>啟動、 監視及取消在 Python 中的定型執行

[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)並[Machine Learning CLI](reference-azure-machine-learning-cli.md)提供各種方法來監視、 組織及管理您的定型和測試的執行。

本文將說明下列工作的範例：

* 執行效能的監視。
* 取消或失敗執行。
* 建立子執行。
* 標記，並找出執行。

## <a name="prerequisites"></a>先決條件

您將需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure 機器學習服務工作區](setup-create-workspace.md)。

* Azure 機器學習服務 SDK for Python (版本 1.0.21 或更新版本)。 若要安裝或更新至最新版的 sdk，請參閱[安裝或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    若要檢查您的 Azure 機器學習服務 sdk 的版本，請使用下列程式碼：

    ```Python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)並[CLI 擴充功能，Azure Machine Learning 服務](reference-azure-machine-learning-cli.md)。

## <a name="start-a-run-and-its-logging-process"></a>啟動測試回合和其記錄處理程序

### <a name="using-the-sdk"></a>使用 SDK

藉由匯入設定您的實驗[工作區](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)，[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)，[執行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)，以及[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 類別[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)封裝。

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

開始執行並使用其記錄處理程序[ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-)方法。

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>使用 CLI

若要啟動的實驗執行，請使用下列步驟：

1. 從殼層或命令提示字元中，使用 Azure CLI 來驗證您的 Azure 訂用帳戶：

    ```azurecli-interactive
    az login
    ```

1. 將工作區設定連結至包含您的訓練指令碼的資料夾中。 取代`myworkspace`與您的 Azure 機器學習服務工作區。 取代`myresourcegroup`與 Azure 資源群組，其中包含您的工作區：

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令會建立`.azureml`子目錄，其中包含範例 runconfig 和 conda 環境檔案。 它也包含`config.json`用來與您的 Azure Machine Learning 工作區通訊的檔案。

    如需詳細資訊，請參閱 < [az ml 資料夾附加](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

2. 若要開始執行，請使用下列命令。 使用此命令時，指定的 runconfig 檔案名稱 (在之前的文字\*.runconfig，如果您正在查看您的檔案系統) 對-c 參數。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`命令建立`.azureml`子目錄，其中包含兩個範例 runconfig 檔案。 
    >
    > 如果您有以程式設計方式建立的回合的組態物件的 Python 指令碼時，您可以使用[RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將其儲存為 runconfig 檔案。
    >
    > 如以上範例的 runconfig 檔案，請參閱 < [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)。

    如需詳細資訊，請參閱 < [az ml 執行提交指令碼](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

## <a name="monitor-the-status-of-a-run"></a>監視執行中的狀態

### <a name="using-the-sdk"></a>使用 SDK

取得與執行中的狀態[ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)方法。

```Python
print(notebook_run.get_status())
```

若要取得有關執行的其他詳細資料，請使用[ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)方法。

```Python
notebook_run.get_details()
```

當您執行成功完成時，使用[ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)方法，以將它標示為已完成。

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

如果您使用 Python 的`with...as`模式中，執行會自動將標示為已完成執行時超出範圍本身。 您不需要以手動方式將執行結果標示為已完成。

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

1. 若要檢視您的實驗結果的清單，請使用下列命令。 取代`experiment`的實驗名稱：

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    此命令會傳回 JSON 文件，其中列出執行這項實驗的相關資訊。

    如需詳細資訊，請參閱 < [az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

2. 若要檢視特定執行的資訊，請使用下列命令。 取代`runid`的執行識別碼：

    ```azurecli-interactive
    az ml run show -r runid
    ```

    此命令會傳回 JSON 文件，其中列出執行的相關資訊。

    如需詳細資訊，請參閱 < [az ml 執行顯示](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)。

## <a name="cancel-or-fail-runs"></a>取消或失敗執行

如果您注意到發生錯誤，或您的執行時間太長，無法完成，您可以取消執行。

### <a name="using-the-sdk"></a>使用 SDK

若要取消執行，使用 SDK，請使用[ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法：

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

如果您執行完成時，但它包含錯誤 （例如，不正確的定型指令碼使用），您可以使用[ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-)方法，以將它標示為失敗。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>使用 CLI

若要取消執行，使用 CLI，請使用下列命令。 取代`runid`的執行識別碼

```azurecli-interactive
az ml run cancel -r runid
```

如需詳細資訊，請參閱 < [az ml 執行取消](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)。

## <a name="create-child-runs"></a>建立子執行

建立子系執行，以便分組相關的執行，例如不同超參數微調的反覆項目。

> [!NOTE]
> 子執行只能建立使用 SDK。

此程式碼範例會使用`hello_with_children.py`指令碼，以使用建立的五個子會從內送出執行批次[ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法：

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 移出範圍，子執行會自動標示為已完成。

您也可以啟動子執行，但每個建立產生的網路呼叫，因為它是比提交批次的執行效率。

若要查詢特定父系的子系執行，請使用[ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)方法。

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>標記，並找出執行

在 Azure Machine Learning 服務中，您可以使用屬性和標籤，來協助組織及查詢您執行的重要資訊。

### <a name="add-properties-and-tags"></a>新增屬性和標籤

#### <a name="using-the-sdk"></a>使用 SDK

若要加入您的執行中的可搜尋的中繼資料，請使用[ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)方法。 例如，下列程式碼加入`"author"`屬性來執行：

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

屬性不變，因此它們會建立稽核的永久記錄。 下列程式碼範例會產生錯誤，因為我們已新增`"azureml-user"`做為`"author"`前面的程式碼的屬性值：

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

與屬性不同，標記是可變更。 若要新增您的實驗的取用者可搜尋且有意義資訊，請使用[ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)方法。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

您也可以加入簡單的字串標記。 當這些標記會出現在標記字典中時，它們會有值為`None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>使用 CLI

> [!NOTE]
> 使用 CLI，您只能新增或更新標記。

若要新增或更新的標籤，請使用下列命令：

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

如需詳細資訊，請參閱 < [az ml 執行 update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)。

### <a name="query-properties-and-tags"></a>查詢屬性和標籤

您可以查詢執行實驗，以傳回符合特定的屬性和標籤的回合的清單內。

#### <a name="using-the-sdk"></a>使用 SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>使用 CLI

Azure CLI 支援[JMESPath](http://jmespath.org)可用來篩選執行屬性和標籤為基礎的查詢。 若要使用 Azure CLI 中使用 JMESPath 查詢，指定與`--query`參數。 下列範例顯示使用屬性和標籤的基本查詢：

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

如需有關如何查詢 Azure CLI 結果的詳細資訊，請參閱[查詢 Azure CLI 命令輸出](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。

## <a name="example-notebooks"></a>Notebook 範例

下列 notebook 會示範如何在這篇文章中的概念：

* 若要深入了解記錄 Api，請參閱[記錄 API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)。

* 如有 Azure 機器學習服務 SDK，執行管理的詳細資訊，請參閱 <<c0> [ 管理執行 notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)。

## <a name="next-steps"></a>後續步驟

* 若要了解如何記錄計量，為您的實驗，請參閱[定型執行期間記錄計量](how-to-track-experiments.md)。
