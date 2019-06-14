---
title: 建立工作區
titleSuffix: Azure Machine Learning service
description: 使用 Azure 入口網站、 SDK、 範本或 CLI 來建立您的 Azure 機器學習服務工作區。 此工作區提供一個集中的位置，當您使用 Azure Machine Learning 服務，您建立的所有成品。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 36f3d421ee0b41a0ff71b549a4d4b5646188c3fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417346"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>建立 Azure 機器學習服務工作區

若要使用 Azure Machine Learning 服務，您需要[ **Azure Machine Learning 服務工作區**](concept-workspace.md)。  此工作區是服務的最上層資源，並提供您要使用您所建立的所有成品的集中位置。 

在本文中，您會學習如何建立工作區中使用任何一種方法： 
* [Azure 入口網站](#portal)介面
* [Azure Machine Learning 適用於 Python 的 SDK](#sdk)
* Azure Resource Manager 範本
* [Azure Machine Learning CLI](#cli)

您使用這裡中的步驟建立的工作區可用來當做其他教學課程和 how-to 文章的必要條件。

如果您想要使用指令碼設定自動化的機器學習服務中的本機 Python 環境，請參閱[Azure/MachineLearningNotebooks GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)如需相關指示。  

當您建立工作區自動 （如果它們也出現些許可用），會加入下列的 Azure 資源：
 
- [Azure 容器登錄](https://azure.microsoft.com/services/container-registry/)：若要降低成本，是 ACR**消極式載入**直到部署映像建立。
- [Azure 儲存體](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>如同其他 Azure 服務，Machine Learning 也有其相關的特定限制和配額。 [深入了解配額及如何要求更多配額。](how-to-manage-quotas.md)


## <a name="prerequisites"></a>必要條件
若要建立工作區，您將需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

## <a name="portal"></a> Azure 入口網站

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

無論其建立方式，您可以檢視您的工作區中[Azure 入口網站](https://portal.azure.com/)。  請參閱[檢視工作區](how-to-manage-workspace.md#view)如需詳細資訊。

## <a name="sdk"></a> Python SDK

建立您工作區中使用 Python SDK。 首先，您要安裝 SDK。

> [!IMPORTANT]
> 如果您使用 Azure Databricks 的 Azure 資料科學虛擬機器，請略過安裝 SDK。
> * 在 2018 年 9 月 27 日之後建立的 Azure 資料科學虛擬機器，會隨附預先安裝的 Python SDK。 略過安裝，而且開頭[sdk 建立的工作區](#sdk-create)。
> * 在 Azure Databricks 環境中，請改用 [Databricks 安裝步驟](how-to-configure-environment.md#azure-databricks)。

>[!NOTE]
> 使用下列指示來安裝和使用 SDK，透過您的本機電腦。 若要使用 Jupyter 的遠端虛擬機器上，設定遠端桌面或 X 終端機工作階段。

安裝 SDK 之前，我們建議您先建立獨立的 Python 環境。 雖然本文使用 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)，但您也可以使用已安裝的完整 [Anaconda](https://www.anaconda.com/) 或 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)。

這篇文章中的指示將會安裝執行快速入門和教學課程 runbook 所需的所有套件。  其他範例 Notebook 可能需要安裝其他元件。  如需這些元件的詳細資訊，請參閱 [安裝 適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

### <a name="install-miniconda"></a>安裝 Miniconda

[下載並安裝 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。 選取 Python 3.7 版進行安裝。 請勿選取 Python 2.x 版。  

### <a name="create-an-isolated-python-environment"></a>建立獨立的 Python 環境

1. 開啟 Anaconda 提示字元，然後建立名為新的 conda 環境*myenv*並安裝 Python 3.6.5。 Azure Machine Learning SDK 會使用 Python 3.5.2 或更新版本，但自動化機器學習元件在 Python 3.7 上無法完整運作。  建立環境可能需要幾分鐘的時間，因為需要下載元件和套件。 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. 啟用環境。

    ```shell
    conda activate myenv
    ```

1. 啟用環境特定的 iPython 核心：

    ```shell
    conda install notebook ipykernel
    ```

    然後建立核心：

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>安裝 SDK

1. 在已啟動的 conda 環境中，安裝 Machine Learning SDK 的核心元件和 Jupyter Notebook 功能。 完成安裝需要幾分鐘的時間，視您的電腦組態而定。

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. 若要在 Azure Machine Learning 教學課程中使用此環境，請安裝這些套件。

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. 若要在 Azure Machine Learning 教學課程中使用此環境，請安裝自動化機器學習元件。

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> 在某些命令列工具中，您可能需要加上引號，如下所示：
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> 使用 SDK 建立工作區

使用 Python SDK 在 Jupyter Notebook 中建立您的工作區。

1. 建立並/或切換至您要在快速入門和教學課程中使用的目錄。

1. 若要啟動 Jupyter Notebook，請輸入下列命令：

    ```shell
    jupyter notebook
    ```

1. 在瀏覽器視窗中，使用預設 `Python 3` 核心建立新的 Notebook。 

1. 若要顯示 SDK 版本，請在 Notebook 資料格中輸入並執行下列 Python 程式碼：

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. 在 [Azure 入口網站的訂用帳戶清單](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中尋找 `<azure-subscription-id>` 參數的值。 使用您在其中是擁有者或參與者角色的任何訂用帳戶。 如需有關角色的詳細資訊，請參閱 <<c0> [ 管理的存取權的 Azure Machine Learning 工作區](how-to-assign-roles.md)文章。

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


### <a name="write-a-configuration-file"></a>寫入組態檔

將組態檔中的工作區詳細資料儲存到目前的目錄。 這個檔案稱為 *.azureml/config.json*。  

此工作區組態檔可讓您稍後輕鬆地載入相同的工作區。 您可以將它與其他 notebook 和相同的目錄或子目錄，使用程式碼中的指令碼`ws=Workspace.from_config()`。 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

此 `write_config()` API 呼叫會在目前的目錄中建立組態檔。 *.Azureml/config.json*檔案包含下列：

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> 若要使用您的工作區中的 Python 指令碼或位於另一個目錄中的 Jupyter Notebook，請將這個檔案複製到該目錄。 檔案可以在相同的目錄中，命名的子目錄 *.azureml*，或父目錄中。

## <a name="resource-manager-template"></a>Resource manager 範本

若要使用範本建立工作區，請參閱[使用範本建立 Azure Machine Learning 服務工作區](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>命令列介面

若要使用 CLI 建立工作區，請參閱[使用 Azure Machine Learning 服務的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。

## <a name="clean-up-resources"></a>清除資源 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

* 無論其建立方式，您可以檢視您的工作區中[Azure 入口網站](https://portal.azure.com/)。  請參閱[檢視工作區](how-to-manage-workspace.md#view)如需詳細資訊。

* 試用您的工作區，透過這些快速入門和教學課程。

    * 快速入門：[在雲端中執行的 Jupyter notebook](quickstart-run-cloud-notebook.md)。
    * 快速入門：[在您自己的伺服器上執行的 Jupyter notebook](quickstart-run-local-notebook.md)。
    * 兩段式教學課程：[定型](tutorial-train-models-with-aml.md)並[部署](tutorial-deploy-models-with-aml.md)影像分類模式。
    * 兩段式教學課程：[準備資料](tutorial-data-prep.md)並[使用自動化的機器學習服務](tutorial-auto-train-models.md)建立迴歸模型。

* 深入了解如何[設定開發環境](how-to-configure-environment.md)。

* 深入了解[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)。
