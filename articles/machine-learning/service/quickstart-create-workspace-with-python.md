---
title: 快速入門：在 Python 中開始使用
titleSuffix: Azure Machine Learning service
description: 在 Python 中開始使用 Azure Machine Learning 服務。 使用 Python SDK 建立工作區，此工作區是雲端中用來實驗、訓練及部署機器學習模型的基礎區塊。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: da84d6361d80db8aea797827ed3d7bc612e2eda3
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999045"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>快速入門：使用 Python SDK 開始使用 Azure Machine Learning

在本文中，您將使用適用於 Python 的 Azure Machine Learning SDK 來建立及使用 Azure Machine Learning 服務[工作區](concept-azure-machine-learning-architecture.md)。 此工作區是雲端中使用 Machine Learning 來實驗、訓練及部署機器學習模型的基礎。 

首先，您會設定自己的 Python 環境和 Jupyter Notebook 伺服器。 若要在未安裝的情況下執行，請參閱[快速入門：利用 Azure 入口網站開始使用 Azure Machine Learning](quickstart-get-started.md)。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

在本文章中，您將：
* 安裝 Python SDK。
* 在您的 Azure 訂用帳戶中建立工作區。
* 建立該工作區的組態檔，稍後在其他 Notebook 和指令碼中會用到。
* 撰寫程式碼來記錄工作區內的值。
* 在工作區中檢視記錄的值。

您會建立工作區和組態檔，作為其他 Machine Learning 教學課程和操作說明文章的必要條件。 如同其他 Azure 服務，Machine Learning 也有其相關的特定限制和配額。 [深入了解配額及如何要求更多配額。](how-to-manage-quotas.md)

下列 Azure 資源可在區域內取得時，就會自動新增至您的工作區：
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure 儲存體](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)

如果您沒有 Azure 訂用帳戶，請在開始前建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

## <a name="install-the-sdk"></a>安裝 SDK

> [!IMPORTANT]
> 如果您使用 2018 年 9 月 27 日之後建立的資料科學虛擬機器，請略過本節。
> 在此日期之後建立的資料科學虛擬機器，會隨附預先安裝的 Python SDK。

本文中的程式碼需要使用 Azure Machine Learning SDK 1.0.2 版或更新版本。

安裝 SDK 之前，我們建議您先建立獨立的 Python 環境。 雖然本文使用 [Miniconda](https://conda.io/docs/user-guide/install/index.html)，但您也可以使用已安裝的完整 [Anaconda](https://www.anaconda.com/) 或 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

### <a name="install-miniconda"></a>安裝 Miniconda

[下載並安裝 Miniconda](https://conda.io/miniconda.html)。 選取 Python 3.7 或更新版本。 請勿選取 Python 2.x。

### <a name="create-an-isolated-python-environment"></a>建立獨立的 Python 環境 

1. 開啟命令列視窗，然後使用 Python 3.6 建立名為 *myenv* 的新 Conda 環境。

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. 啟用環境。

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>安裝 SDK

在已啟用的 Conda 環境中安裝 SDK。 此程式碼會安裝 Machine Learning SDK 的核心元件。 它也會在 conda 環境中安裝 Jupyter Notebook 伺服器。 完成安裝需要幾分鐘的時間，視您的電腦組態而定。

```sh
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

您可以使用其他關鍵字來安裝 SDK 的其他元件：

```sh
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

在 Azure Databricks 環境中，請改用下列安裝命令：

```
# Install the base SDK and automl components in the Azure Databricks environment.
# For more information, see https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks.
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>建立工作區

1. 若要啟動 Jupyter Notebook，請輸入下列命令：

    ```shell
    jupyter notebook
    ```

1. 在瀏覽器視窗中，使用預設 `Python 3` 核心建立新的 Notebook。 

1. 若要顯示 SDK 版本，請在 Notebook 資料格中輸入並執行下列 Python 程式碼：

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. 在 [Azure 入口網站的訂用帳戶清單](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中尋找 `<azure-subscription-id>` 參數的值。 使用您在其中是擁有者或參與者角色的任何訂用帳戶。

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   當您執行程式碼時，系統可能會提示您登入 Azure 帳戶。 登入之後，會在本機快取驗證權杖。

1. 若要檢視工作區詳細資料，例如相關聯的儲存體、容器登錄和金鑰保存庫，請輸入下列程式碼：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>寫入組態檔

將組態檔中的工作區詳細資料儲存到目前的目錄。 此檔案稱為 *aml_config\config.json*。  

此工作區組態檔可讓您稍後輕鬆地載入相同的工作區。 您可以使用相同目錄或子目錄中的其他 Notebook 和指令碼來載入它。 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

`write_config()` API 呼叫會在目前的目錄中建立組態檔。 *config.json* 檔案包含下列指令碼：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>使用工作區

撰寫一些使用基本 SDK API 的程式碼來追蹤實驗執行。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>檢視記錄的值
執行完成時，您可以在 Azure 入口網站中檢視實驗執行。 若要列印會瀏覽至前次執行結果的 URL，請使用下列程式碼：

```python
print(run.get_portal_url())
```

使用此連結，即可透過瀏覽器在 Azure 入口網站中檢視記錄的值。

![Azure 入口網站中已記錄的值](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>清除資源 
>[!IMPORTANT]
>您可以使用在此處建立的資源，作為其他 Machine Learning 教學課程和操作說明文章的必要條件。

如果您不打算繼續使用您在本文中建立的資源，請加以刪除，以避免產生費用。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>後續步驟

在本文中，您已建立進行實驗和部署模型所需的資源。 您在 Notebook 中執行了程式碼，並且在雲端的工作區中探索了該程式碼的執行歷程記錄。

若要將程式碼用於 Machine Learning 教學課程，您的環境中還需要若干套件。

1. 在瀏覽器中關閉 Notebook。
1. 在命令列視窗中選取 Ctrl+C，以停止 Jupyter Notebook 伺服器。
1. 安裝其他套件。

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

安裝這些套件後，請繼續進行教學課程以定型和部署模型。 

> [!div class="nextstepaction"]
> [教學課程：將影像分類模型定型](tutorial-train-models-with-aml.md)

您也可以探索 [GitHub 上更進階的範例](https://aka.ms/aml-notebooks)。
