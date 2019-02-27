---
title: 設定 Python 開發環境
titleSuffix: Azure Machine Learning service
description: 了解如何在使用 Azure Machine Learning 服務時設定開發環境。 在本文中，您將了解如何使用 Conda 環境、建立組態檔，以及設定 Jupyter Notebook、Azure Notebooks、Azure Databricks、IDE、程式碼編輯器和資料科學虛擬機器。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 136a83c586b2f797269beff3cdd0afb9973cb7c8
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340513"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>設定 Azure Machine Learning 的開發環境

在本文中，您將了解如何設定開發環境以使用 Azure Machine Learning 服務。 Machine Learning 服務與平台無關。 

開發環境的唯一要求是 Python 3、Conda (適用於隔離環境)，以及包含 Azure Machine Learning 工作區資訊的組態檔。

本文著重於下列環境和工具：

* Azure Notebooks：裝載於 Azure 雲端中的 Jupyter Notebook 服務。 這是最簡單的入門方式，因為已經安裝了 Azure Machine Learning SDK。

* [資料科學虛擬機器 (DSVM)](#dsvm)：Azure 雲端中預先設定的開發或實驗環境，此環境專為資料科學工作而設計，並可部署至純 CPU VM 執行個體或 GPU 型執行個體。 已安裝 Python 3、Conda、Jupyter Notebook 和 Azure Machine Learning SDK。 VM 隨附適用於開發機器學習解決方案的常用機器學習和深度學習架構、工具及編輯器。 針對 Azure 平台上的機器學習，它可能是最完整的開發環境。

* [Jupyter Notebook](#jupyter)：如果您已經在使用 Jupyter Notebook，則 SDK 有一些您應該安裝的附加功能。

* [Visual Studio Code](#vscode)：如果您使用 Visual Studio Code，則有一些您可以安裝的實用擴充功能。

* [Azure Databricks](#aml-databricks)：以 Apache Spark 為基礎的常用資料分析平台。 了解如何將 Azure Machine Learning SDK 安裝在您的叢集上，讓您可以部署模型。

如果您已經擁有 Python 3 環境，或者只是想要安裝 SDK 的基本步驟，請參閱[本機電腦](#local)一節。

## <a name="prerequisites"></a>必要條件

- Azure Machine Learning 服務工作區。 若要建立工作區，請參閱[開始使用 Azure Machine Learning 服務](quickstart-get-started.md)。

- [Continuum Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 套件管理員。

    > [!IMPORTANT]
    > 使用 Azure Notebooks 時不需要 Anaconda 和 Miniconda。

- 在 Linux 或 macOS 上，您需要 bash 殼層。

    > [!TIP]
    > 如果您使用的是 Linux 或 macOS 並使用 bash 以外的殼層 (例如，zsh)，則在執行一些命令時可能會收到錯誤。 若要解決此問題，請使用 `bash` 命令啟動新的 bash 殼層，並在其中執行命令。

- 在 Windows 上，您需要命令提示字元或 Anaconda 提示字元 (由 Anaconda 和 Miniconda 安裝)。

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (預覽) 是 Azure 雲端中的互動式開發環境。 這是開始使用 Azure Machine Learning 環境最簡單的方式。

* Azure Machine Learning SDK 已安裝。
* 在 Azure 入口網站中建立 Azure Machine Learning 服務工作區後，您可以按一下按鈕來自動設定您的 Azure Notebook 環境，以使用工作區。

若要開始使用 Azure Notebooks 進行開發，請參閱[開始使用 Azure Machine Learning 服務](quickstart-get-started.md)。

根據預設，Azure Notebooks 使用免費服務層，其限制為 4 GB 記憶體和 1GB 資料。 不過，您可以將資料科學虛擬機器執行個體附加到 Azure Notebooks 專案來移除這些限制。 如需詳細資訊，請參閱[管理和設定 Azure Notebooks 專案 - 計算層](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)。

## <a id="dsvm"></a>資料科學虛擬機器

DSVM 是自訂的虛擬機器 (VM) 映像。 它是針對使用下列項目預先設定的資料科學工作而設計：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 及 Azure Machine Learning SDK 等套件
  - Spark 獨立版和 Drill 等常用的資料科學工具
  - Azure CLI、AzCopy 及儲存體總管等 Azure 工具
  - Visual Studio Code 和 PyCharm 等整合式開發環境 (IDE)
  - Jupyter Notebook 伺服器

Azure Machine Learning SDK 適用於 Ubuntu 或 Windows版本的 DSVM。 但如果您也打算使用 DSVM 作為計算目標，則僅支援 Ubuntu。

若要使用 DSVM 作為開發環境，請執行下列操作：

1. 在下列其中一個環境中建立 DSVM：

    * Azure 入口網站：

        * [建立 Ubuntu 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [建立 Windows 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI：

        > [!IMPORTANT]
        > * 使用 Azure CLI 時，您必須先使用 `az login` 命令來登入您的 Azure 訂用帳戶。
        >
        > * 使用此步驟中的命令時，您必須提供資源群組名稱、VM 名稱、使用者名稱及密碼。

        * 若要建立「Ubuntu 資料科學虛擬機器」，請使用下列命令：

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * 若要建立「Windows 資料科學虛擬機器」，請使用下列命令：

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Azure Machine Learning SDK 已安裝在 DSVM 上。 若要使用包含該 SDK 的 Conda 環境，請使用下列命令之一：

    * Ubuntu DSVM：

        ```shell
        conda activate py36
        ```

    * Windows DSVM：

        ```shell
        conda activate AzureML
        ```

1. 若要確認您是否可以存取 SDK 並檢查版本，請使用下列 Python 程式碼：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要設定 DSVM 使用 Azure Machine Learning 服務工作區，請參閱[建立工作區組態檔](#workspace)一節。

如需詳細資訊，請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a id="local"></a>本機電腦

使用本機電腦 (也可能是遠端虛擬機器) 時，請執行下列操作來建立 Conda 環境並安裝 SDK：

1. 開啟命令提示字元或殼層。

1. 使用下列命令建立 Conda 環境：

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    建立環境可能需要幾分鐘的時間，因為或許需要下載 Python 3.6 和其他元件。

1. 使用下列命令安裝 Azure Machine Learning SDK 與 Notebook 的額外項目，以及資料準備 SDK：

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > 如果顯示訊息表示無法解除安裝 PyYAML ，請改用下列命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   安裝 SDK 可能需要幾分鐘的時間。

1. 安裝您的機器學習實驗套件。 使用下列命令，將 *\<new package>* 取代為想要安裝的套件：

    ```shell
    conda install <new package>
    ```

1. 若要確認是否已安裝 SDK，請使用下列 Python 程式碼：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 專案](https://jupyter.org/)的一部分。 它們提供互動式程式碼撰寫體驗，讓您用來建立混合即時程式碼與敘述文字和圖形的文件。 Jupyter Notebook 也是與其他人共用結果的好方法，因為您可以將程式碼區段的輸出儲存在文件中。 您可以在各種不同的平台上安裝 Jupyter Notebook。

[本機電腦](#local) 區段中的程序會為 Jupyter Notebook 安裝選用元件。 若要在 Jupyter Notebook 環境中啟用這些元件，請執行下列操作：

1. 開啟命令提示字元或殼層。

1. 若要安裝可感知 Conda 的 Jupyter Notebook 伺服器，請使用下列命令：

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. 使用下列命令開啟 Jupyter Notebook：

    ```shell
    jupyter notebook
    ```

1. 若要確認 Jupyter Notebook 是否可以使用 SDK，請開啟新的 Notebook 並選取 **myenv** 作為您的核心，然後在 Notebook 資料格中執行下列命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要設定 Jupyter Notebook 使用 Azure Machine Learning 服務工作區，請前往[建立工作區組態檔](#workspace)一節。

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code 是跨平台的程式碼編輯器。 它依賴於 Python 支援的本機 Python 3 和 Conda 安裝，但它提供額外的工具來使用 AI。 它還支援從程式碼編輯器中選取 Conda 環境。

若要使用 Visual Studio Code 進行開發，請執行下列操作：

1. 若要了解如何使用 Visual Studio Code 進行 Python 開發，請參閱[在 VSCode 中開始使用 Python](https://code.visualstudio.com/docs/python/python-tutorial)。

1. 若要選取 Conda 環境，請開啟 VS Code，然後選取 Ctrl+Shift+P (Linux 和 Windows) 或 Command+Shift+P (Mac)。  
    [命令棧板] 隨即開啟。 

1. 輸入 __Python:Select Interpreter__，然後選取 Conda 環境。

1. 若要驗證您是否可以使用 SDK，請建立並執行包含下列程式碼的新 Python 檔案 (.py)：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要安裝適用於 Visual Studio Code 的 Azure Machine Learning 擴充功能，請參閱[適用於 AI 的工具](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

    如需詳細資訊，請參閱[使用適用於 Visual Studio Code 的 Azure Machine Learning](how-to-vscode-tools.md)。

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

您可以使用自訂版適用於 Azure Databricks 的 Azure Machine Learning SDK，來進行端對端自訂機器學習。 或者，您可以在 Databricks 內為您的模型定型，然後使用 [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) 部署該模型。

準備您的 Databricks 叢集並取得範例 Notebook：

1. 使用下列設定建立 [Databricks 叢集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)：

    | 設定 | 值 |
    |----|---|
    | 叢集名稱 | yourclustername |
    | Databricks 執行階段 | 任何非 ML 執行階段 (非 ML 4.x、5.x) |
    | Python 版本 | 3 |
    | 背景工作角色 | 2 個以上 |

    只有當您要在 Databricks 上使用自動化機器學習時，才使用這些設定：
    
    |   設定 | 值 |
    |----|---|
    | 背景工作節點 VM 類型 | 建議使用已記憶體最佳化的 VM |
    | 啟用自動調整 | 取消選取 |
    
    Databricks 叢集中的背景工作節點數目決定自動化 ML 設定中並行反覆項目的上限數目。  

    建立叢集將需要幾分鐘的時間。 請靜候至叢集運作，再繼續操作。

1. 安裝 Azure Machine Learning SDK 套件並連結您的叢集。  

    * 使用下列其中一個設定建立[程式庫](https://docs.databricks.com/user-guide/libraries.html#create-a-library) (只能選擇其中一個選項)：
    
        * 若要安裝不含自動化機器學習功能的 Azure Machine Learning SDK：
            | 設定 | 值 |
            |----|---|
            |來源 | 上傳 Python Egg 或 PyPI
            |PyPi 名稱 | azureml-sdk[databricks]
    
        * 若要安裝含有自動化機器學習功能的 Azure Machine Learning SDK：
            | 設定 | 值 |
            |----|---|
            |來源 | 上傳 Python Egg 或 PyPI
            |PyPi 名稱 | azureml-sdk[automl_databricks]
    
    * 請勿選取 [自動連結至所有叢集]

    * 選取叢集名稱旁的 [連結]

    * 確定狀態直到變更為 [已連結] 之前，沒有發生任何錯誤。 這需要幾分鐘的時間。

    如果您有舊版 SDK，請從叢集的已安裝程式庫將它取消選取，並移至垃圾桶。 安裝新版 SDK，並重新啟動叢集。 如果在此之後發生問題，請中斷連結再重新連結叢集。

    當您完成時，便會連結程式庫，如下圖所示。 請注意這些[常見的 Databricks 問題](resource-known-issues.md#databricks)。

    * 如果您已安裝不含自動化機器學習的 Azure Machine Learning SDK ![Databricks 上已安裝不含自動化機器學習的 SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * 如果您已安裝含有自動化機器學習的 Azure Machine Learning SDK ![Databricks 上已安裝含有自動化機器學習的 SDK](./media/how-to-configure-environment/automlonadb.jpg)

   如果此步驟失敗，請執行下列操作，重新啟動您的叢集：

   a. 在左窗格中，選取 [叢集]。 
   
   b. 請選取表格中您的叢集名稱。 

   c. 在 [程式庫] 索引標籤上，選取 [重新啟動]。

1. 下載 [Azure Databricks/Azure Machine Learning SDK Notebook 封存檔案](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc)。

   >[!Warning]
   > 有許多範例 Notebook 可與 Azure Machine Learning 服務搭配使用。 只有[這些範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) 可以搭配 Azure Databricks 使用。

1.  [將封存檔案匯入](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive)到您的 Databricks 叢集並開始探索，如 [Machine Learning Notebooks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) \(英文\) 頁面所述。


## <a id="workspace"></a>建立工作區組態檔

工作區組態檔是一個 JSON 檔案，可告知 SDK 如何與您的 Azure Machine Learning 服務工作區進行通訊。 檔案名稱為 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

這個 JSON 檔案必須位於包含您的 Python 指令碼或 Jupyter Notebook 的目錄結構中。 它可以位於相同的目錄，名為 *aml_config* 的子目錄，也可以位於父目錄中。

要使用程式碼中的此檔案，請使用 `ws=Workspace.from_config()`。 此程式碼會從檔案載入資訊，並連接到您的工作區。

您可以透過三種方式建立組態檔：

* **依照 [Azure Machine Learning 快速入門](quickstart-get-started.md)**：*config.json* 檔案是在 Azure Notebook 程式庫中建立的。 此檔案包含您工作區的組態資訊。 您可以將此 *config.json* 下載或複製到其他開發環境。

* **手動建立檔案**：使用此方法時，您可以使用文字編輯器。 您可以透過瀏覽 [Azure 入口網站](https://portal.azure.com)中的工作區，來尋找組態檔中的值。 複製工作區名稱、資源群組和訂用帳戶識別碼值，並在組態檔中使用它們。

     ![Azure 入口網站](./media/how-to-configure-environment/configure.png)

* **以程式設計方式建立檔案**：在下列程式碼片段中，您可以提供訂用帳戶識別碼、資源群組和工作區名稱來連線到工作區。 接著，它會將工作區組態儲存至檔案：

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    此程式碼會將組態檔寫入 *aml_config/config.json* 檔案。

## <a name="next-steps"></a>後續步驟

- 在 Azure Machine Learning 使用 MNIST 資料集[定型模型](tutorial-train-models-with-aml.md)] \(英文\)
- 檢視[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk) \(英文\) 參考
- 深入了解 [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk) \(英文\)
