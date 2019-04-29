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
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819211"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>啟動、 監視及取消在 Python 中的定型執行

[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供各種方法來監視、 組織及管理您的定型和測試的執行。

本文將說明下列工作的範例：

* 執行效能的監視。
* 取消或失敗執行。
* 建立子執行。
* 標記，並找出執行。

## <a name="prerequisites"></a>必要條件

您將需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure 機器學習服務工作區](setup-create-workspace.md)。

* Azure 機器學習服務 SDK for Python (版本 1.0.21 或更新版本)。 若要安裝或更新至最新版的 sdk，請參閱[安裝或更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

    若要檢查您的 Azure 機器學習服務 sdk 的版本，請使用下列程式碼：

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>啟動測試回合和其記錄處理程序

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

## <a name="monitor-the-status-of-a-run"></a>監視執行中的狀態

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

## <a name="cancel-or-fail-runs"></a>取消或失敗執行

 如果您注意到發生錯誤，或如果您的執行時間太長，無法完成，請使用[ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--)方法完成之前停止執行，並將其標示為已取消。

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

如果您執行完成時，但它包含錯誤 （例如，不正確的定型指令碼使用），您可以使用[ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-)方法，以將它標示為失敗。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>建立子執行

建立子系執行，以便分組相關的執行，例如不同超參數微調的反覆項目。

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

### <a name="query-properties-and-tags"></a>查詢屬性和標籤

您可以查詢執行實驗，以傳回符合特定的屬性和標籤的回合的清單內。

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Notebook 範例

下列 notebook 會示範如何在這篇文章中的概念：

* 若要深入了解記錄 Api，請參閱[記錄 API notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)。

* 如有 Azure 機器學習服務 SDK，執行管理的詳細資訊，請參閱 <<c0> [ 管理執行 notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)。

## <a name="next-steps"></a>後續步驟

* 若要了解如何記錄計量，為您的實驗，請參閱[定型執行期間記錄計量](how-to-track-experiments.md)。