---
title: 快速入門：使用 Python SDK 來建立機器學習服務工作區 - Azure Machine Learning
description: 開始使用 Azure Machine Learning。  安裝 Python SDK，並使用它來建立工作區。 此工作區是雲端中使用 Azure Machine Learning services，來實驗、定型及部署機器學習服務模型的基礎區塊。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067751"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>快速入門：使用 Python 來開始使用 Azure Machine Learning

在此快速入門中，您將使用適用於 Python 的 Azure Machine Learning 來建立及使用 Machine Learning 服務[工作區](concept-azure-machine-learning-architecture.md)。 此工作區是使用 Azure Machine Learning 服務在雲端中實驗、定型及部署機器學習服務模型的基礎區塊。

在本教學課程中，您將安裝 Python SDK，以及：
* 在您的 Azure 訂用帳戶中建立工作區
* 建立該工作區的組態檔，稍後在其他 Notebook 和指令碼中會用到
* 撰寫程式碼來記錄工作區內的值
* 在工作區中檢視記錄的值

您在此快速入門中建立的工作區和其組態檔，可用來作為其他 Azure Machine Learning 教學課程和操作說明文章的必要條件。 如同使用其他 Azure 服務，Azure Machine Learning 服務也有一些相關限制和配額。 [深入了解配額及如何要求更多配額。](how-to-manage-quotas.md)

為了方便起見，當下列的 Azure 資源可在區域內取得時，會自動加入至您的工作區：[容器登錄](https://azure.microsoft.com/services/container-registry/)、[儲存體](https://azure.microsoft.com/services/storage/)、[Application Insights](https://azure.microsoft.com/services/application-insights/) 和 [Key Vault](https://azure.microsoft.com/services/key-vault/)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


##  <a name="install-the-sdk"></a>安裝 SDK

如果您使用 2018 年 9 月 27 日之後建立的資料科學虛擬機器 (DSVM)，**請略過本節**，因為這些 DSVM 會隨附預先安裝的 Python SDK。

安裝 SDK 之前，我們建議您先建立獨立的 Python 環境。 雖然本快速入門使用 [Miniconda](https://conda.io/docs/user-guide/install/index.html)，但您也可以使用已安裝的完整 [Anaconda](https://www.anaconda.com/) 或 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

### <a name="install-miniconda"></a>安裝 Miniconda


[下載](https://conda.io/miniconda.html)並安裝 Miniconda。 選擇 Python 3.7 版或更新版本。 請勿選擇 Python 2.x 版。

### <a name="create-an-isolated-python-environment"></a>建立獨立的 Python 環境 

啟動命令列視窗，並使用 Python 3.6 建立名為 `myenv` 的新 Conda 環境。

```sh
conda create -n myenv -y Python=3.6
```

啟用環境。

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>安裝 SDK

在已啟用的 Conda 環境中安裝 SDK。 此程式碼會在 `myenv` Conda 環境中安裝 Azure Machine Learning SDK 和 Jupyter Notebook 伺服器的核心元件。  完成此安裝程序需要**大約 4 分鐘**。

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>建立工作區

輸入下列命令來啟動 Jupyter Notebook。
```sh
jupyter notebook
```

在瀏覽器視窗中，使用預設 `Python 3` 核心建立新的 Notebook。 

在 Notebook 資料格中輸入下列 Python 程式碼並執行，即可顯示 SDK 版本。

```python
import azureml.core
print(azureml.core.VERSION)
```

建立新的 Azure 資源群組和新的工作區。

在 [Azure 入口網站的訂用帳戶清單](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中尋找 `<azure-subscription-id>` 的值。 使用您在其中是擁有者或參與者角色的任何訂用帳戶。

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region
                     )
```

執行上述程式碼可能會觸發新的瀏覽器視窗，讓您登入 Azure 帳戶。 一旦登入之後，本機上將會執行驗證權杖快取。

若要查看工作區的詳細資料，包括相關聯的儲存體、容器登錄和金鑰保存庫，請輸入：

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>寫入組態檔

將組態檔中的工作區詳細資料儲存到目前的目錄。 此檔案稱為 'aml_config\config.json'。  

當您稍後要使用相同目錄或子目錄中的其他 Notebook 和指令碼，來載入這同一個工作區時，此工作區組態檔有助於簡化作業程序。 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

`write_config()` API 呼叫會在目前的目錄中建立組態檔。 `config.json` 檔案包含下列內容：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>使用工作區

撰寫一些使用基本 SDK API 的程式碼來追蹤實驗執行。

```python
from azureml.core import Experiment

# create a new experiemnt
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>檢視記錄的值
執行完成時，您可以在 Azure 入口網站中檢視實驗執行。 使用下列程式碼可列印上一個執行結果的 URL。

```python
print(run.get_portal_url())
```

使用此連結，即可透過瀏覽器在 Azure 入口網站中檢視記錄的值。

![入口網站中的已記錄值](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>清除資源 
>[!IMPORTANT]
>您所建立的資源可用來作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

如果您不再使用在此處建立的資源，請刪除您為本快速入門建立的資源，以免產生任何費用。

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>後續步驟

您現在已經建立了開始實驗和部署模型所需的資源。 您也在 Notebook 中執行了程式碼，並在雲端的工作區中探索了該程式碼的執行歷程記錄。

您的環境中需要多一點套件，才能搭配 Azure Machine Learning 教學課程使用該環境：

1. 在瀏覽器中關閉 Notebook。
1. 在命令列視窗中，使用 `Ctrl`+`C` 停止 Notebook 伺服器。
1. 安裝其他套件。

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

安裝這些套件後，請遵循教學課程來定型和部署模型。  

> [!div class="nextstepaction"]
> [教學課程：將影像分類模型定型](tutorial-train-models-with-aml.md)

您也可以探索 [GitHub 上更進階的範例](https://aka.ms/aml-notebooks)。
