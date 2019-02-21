---
title: 快速入門：在 Python 中開始使用
titleSuffix: Azure Machine Learning service
description: 在 Python 中開始使用 Azure Machine Learning 服務。 使用 Python SDK 建立工作區，此工作區是雲端中用來實驗、訓練及部署機器學習模型的基礎區塊。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/22/2019
ms.custom: seodec18
ms.openlocfilehash: 1962cef85c5e663de640f296a6e8e9efd5a1f4d6
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310350"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>快速入門：使用 Python SDK 開始使用 Azure Machine Learning

在本文中，您將使用適用於 Python 3 的 Azure Machine Learning SDK 來建立及使用 Azure Machine Learning 服務[工作區](concept-azure-machine-learning-architecture.md)。 此工作區是雲端中使用 Machine Learning 來實驗、訓練及部署機器學習模型的基礎。

首先，您會設定自己的 Python 環境和 Jupyter Notebook 伺服器。 若要在未安裝的情況下執行，請參閱[快速入門：利用 Azure 入口網站開始使用 Azure Machine Learning](quickstart-get-started.md)。 

檢視本快速入門的影片版本：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

在本快速入門中，您將：

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

>[!NOTE]
> 本文中的程式碼需要使用 Azure Machine Learning SDK 1.0.2 版，並且已進行過 1.0.8 版的測試。


如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

## <a name="install-the-sdk"></a>安裝 SDK

> [!IMPORTANT]
> 如果您使用 Azure 資料科學虛擬機器或 Azure Databricks，請略過本節。
> * 在 2018 年 9 月 27 日之後建立的 Azure 資料科學虛擬機器，會隨附預先安裝的 Python SDK。
> * 在 Azure Databricks 環境中，請改用 [Databricks 安裝步驟](how-to-configure-environment.md#azure-databricks)。

安裝 SDK 之前，我們建議您先建立獨立的 Python 環境。 雖然本文使用 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)，但您也可以使用已安裝的完整 [Anaconda](https://www.anaconda.com/) 或 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

### <a name="install-miniconda"></a>安裝 Miniconda

[下載並安裝 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。 選取 Python 3.7 或更新版本以進行安裝。 請勿選取 Python 2.x 版。  

### <a name="create-an-isolated-python-environment"></a>建立獨立的 Python 環境

1. 開啟命令列視窗，然後建立名為 *myenv* 的新 Conda 環境，並安裝 Python 3.6。 Azure Machine Learning SDK 會使用 Python 3.5.2 或更新版本，但自動化機器學習元件在 Python 3.7 上無法完整運作。

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. 啟用環境。

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>安裝 SDK

1. 在已啟動的 conda 環境中，安裝 Machine Learning SDK 的核心元件和 Jupyter Notebook 功能。  完成安裝需要幾分鐘的時間，視您的電腦組態而定。

  ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. 在 Conda 環境中安裝 Jupyter Notebook 伺服器。

  ```shell
    conda install -y nb_conda
    ```

1. 若要在 Azure Machine Learning 教學課程中使用此環境，請安裝這些套件。

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. 若要在 Azure Machine Learning 教學課程中使用此環境，請安裝自動化機器學習元件。

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

## <a name="create-a-workspace"></a>建立工作區

使用 Python SDK 在 Jupyter Notebook 中建立您的工作區。

1. 建立並/或切換至您要在快速入門和教學課程中使用的目錄。

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
                         location='eastus2' 
                        )
   ```

   當您執行程式碼時，系統可能會提示您登入 Azure 帳戶。 登入之後，會在本機快取驗證權杖。

1. 若要檢視工作區詳細資料，例如相關聯的儲存體、容器登錄和金鑰保存庫，請輸入下列程式碼：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>寫入組態檔

將組態檔中的工作區詳細資料儲存到目前的目錄。 此檔案稱為 *aml_config\config.json*。  

此工作區組態檔可讓您稍後輕鬆地載入相同的工作區。 您可以使用相同目錄或子目錄中的其他 Notebook 和指令碼來載入它。  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

此 `write_config()` API 呼叫會在目前的目錄中建立組態檔。 *config.json* 檔案包含下列項目：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>使用工作區

執行一些使用基本 SDK API 的程式碼以追蹤實驗執行：

1. 在工作區中建立實驗。
1. 將單一值記錄到實驗中。
1. 將值清單記錄到實驗中。

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

> [!div class="nextstepaction"]
> [教學課程：將影像分類模型定型](tutorial-train-models-with-aml.md)

您也可以探索 [GitHub 上更進階的範例](https://aka.ms/aml-notebooks)。
