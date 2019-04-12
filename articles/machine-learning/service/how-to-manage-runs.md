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
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494042"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>啟動、 監視及取消在 Python 中的定型執行

[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供各種方法來監視、 組織及管理您的定型和測試的執行。

此操作說明會顯示下列工作的範例：

* [執行效能監視器](#monitor)
* [取消或失敗執行](#cancel)
* [建立子執行](#children)
* [標記，並找出執行](#tag)

## <a name="prerequisites"></a>必要條件

您將需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* Azure Machine Learning 服務工作區。 請參閱[建立 Azure 機器學習服務工作區](setup-create-workspace.md)。

* Azure 機器學習服務 SDK for Python 安裝 (版本 1.0.21 或更新版本)。 若要安裝或更新至最新版的 sdk，請前往[安裝/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)頁面。

    若要檢查您的 Azure 機器學習服務 sdk 的版本，請使用下列程式碼。

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>啟動測試回合，並將它的狀態

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

取得與執行的狀態[ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--)。

```Python
print(notebook_run.get_status())
```

若要取得有關執行使用的其他詳細資料[ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--)。

```Python
notebook_run.get_details()
```

當您執行成功完成時，使用[ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)方法，以將它標示為已完成。

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

您也可以使用 Python 的`with...as`模式。 在此情況下，執行會自動將標示本身為已完成執行時超出範圍。 如此一來您不需要以手動方式將執行結果標示為已完成。

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>取消或失敗執行

 如果您注意到發生錯誤，或您的執行似乎需要一些時間來完成，請使用[ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法完成之前停止執行，並將其標示為已取消。

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

如果您執行完成，但包含類似的錯誤，使用不正確的定型指令碼，您可以使用[ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-)方法，以將它標示為失敗。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>建立子執行

建立子系將群組在一起相關的執行，例如針對不同的超參數微調反覆項目執行。

此程式碼範例使用 hello_with_children.py 指令碼來建立的五個子會從內送出執行使用批次[ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-)方法。

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
> 移出範圍時，執行自動完成子系。

您也可以啟動子執行，但每個建立產生的網路呼叫，因為它是比提交批次的執行效率。

使用[ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)查詢子系的方法會執行特定的父代。

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>標記，並找出執行

在 Azure Machine Learning 服務中，您可以使用屬性和標籤，來協助組織及查詢您執行的重要資訊。

### <a name="add-properties-and-tags"></a>新增屬性和標籤

使用[ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-)將可搜尋的中繼資料新增至您的執行。 比方說，下列程式碼會將"author"屬性加入至執行。

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

屬性是不可變的這是為永久記錄為稽核用途很有用。 下列程式碼範例會導致錯誤，因為我們已經在上述程式碼中新增 「 azureml 位使用者 」 與 「 作者 」 屬性。

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

標記，不過會變更。 使用[ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-)將可搜尋且有意義資訊的取用者的您的實驗。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

您也可以加入簡單的字串標記。 它會出現在值的標記字典`None`。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>查詢屬性和標籤

您可以查詢執行實驗，以符合特定的屬性和標籤。

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Notebook 範例

下列 Notebook 示範了此文章中說明的概念：

* 若要深入了解記錄 Api，請參閱[記錄 API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)。

* 如有 Azure 機器學習服務 SDK，執行管理的其他資訊，請參閱 <<c0> [ 管理執行 notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)。

## <a name="next-steps"></a>後續步驟

* 若要了解如何記錄計量，為您的實驗，請參閱[定型執行期間記錄計量](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment)文章。