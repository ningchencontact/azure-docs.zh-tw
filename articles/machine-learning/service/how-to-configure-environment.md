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
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: c4bdeb4e00a59d6ba2b415801c0689d77ed9a825
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577555"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>設定 Azure Machine Learning 的開發環境

在本文中，您將了解如何設定開發環境以使用 Azure Machine Learning 服務。 Machine Learning 服務與平台無關。

唯一的需求，您的開發環境是 Python 3、 Anaconda （適用於隔離的環境） 和組態檔，其中包含您的 Azure Machine Learning 工作區資訊。

本文著重於下列環境和工具：

* Azure Notebooks：裝載於 Azure 雲端中的 Jupyter Notebook 服務。 這是最簡單的入門方式，因為已經安裝了 Azure Machine Learning SDK。

* [資料科學虛擬機器 (DSVM)](#dsvm)：Azure 雲端中預先設定的開發或實驗環境，此環境專為資料科學工作而設計，並可部署至純 CPU VM 執行個體或 GPU 型執行個體。 已安裝 Python 3、Conda、Jupyter Notebook 和 Azure Machine Learning SDK。 VM 隨附適用於開發機器學習解決方案的常用機器學習和深度學習架構、工具及編輯器。 針對 Azure 平台上的機器學習，它可能是最完整的開發環境。

* [Jupyter Notebook](#jupyter)：如果您已經在使用 Jupyter Notebook，則 SDK 有一些您應該安裝的附加功能。

* [Visual Studio Code](#vscode)：如果您使用 Visual Studio Code，則有一些您可以安裝的實用擴充功能。

* [Azure Databricks](#aml-databricks)：以 Apache Spark 為基礎的常用資料分析平台。 了解如何將 Azure Machine Learning SDK 安裝在您的叢集上，讓您可以部署模型。

如果您已經擁有 Python 3 環境，或者只是想要安裝 SDK 的基本步驟，請參閱[本機電腦](#local)一節。

## <a name="prerequisites"></a>必要條件

- Azure Machine Learning 服務工作區。 若要建立工作區，請參閱[建立 Azure 機器學習服務工作區](setup-create-workspace.md)。

- 任一[Anaconda](https://www.anaconda.com/download/)或是[Miniconda](https://conda.io/miniconda.html)封裝管理員。

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

若要開始使用 Azure Notebooks 進行開發，請參閱[開始使用 Azure Machine Learning 服務](quickstart-run-cloud-notebook.md)。

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

當您使用本機電腦 （這可能也是遠端的虛擬機器），建立的 Anaconda 環境，並安裝 SDK，請執行下列動作：

1. 下載並安裝[Anaconda](https://www.anaconda.com/distribution/#download-section) （Python 3.7 版） 如果您還沒有它。

1. 開啟 Anaconda 提示字元，並使用下列命令建立環境：

    執行下列命令來建立環境。

    ```shell
    conda create -n myenv python=3.6.5
    ```

    然後啟動環境。

    ```shell
    conda activate myenv
    ```

    這個範例會建立的環境中使用 python 3.6.5，但可以選擇任何特定 subversions。 SDK 相容性可能不保證特定主要版本中 （3.5 + 建議使用），並建議如果您遇到錯誤，請嘗試不同的版本/subversion Anaconda 環境中。 建立環境可能需要幾分鐘的時間，因為需要下載元件和套件。

1. 若要啟用特定環境的 ipython 核心新環境中執行下列命令。 這可確保預期的核心和封裝匯入行為 Anaconda 環境中使用 Jupyter Notebook 時：

    ```shell
    conda install notebook ipykernel
    ```

    然後執行下列命令來建立核心：

    ```shell
    ipython kernel install --user
    ```

1. 使用下列命令來安裝套件：

    此命令會使用 notebook 和 automl 額外項目安裝基底的 Azure 機器學習服務 SDK。 `automl`額外是大型的安裝，並可以從在方括號移除，如果您不想要執行自動化的機器學習服務實驗。 `automl`額外也做為相依性的預設包含 Azure Machine Learning 資料準備 SDK。

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    使用此命令來安裝 Azure Machine Learning 資料準備 SDK 本身：

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > 如果顯示訊息表示無法解除安裝 PyYAML ，請改用下列命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   它將會需要幾分鐘才能安裝 SDK。 請參閱[安裝指南](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)如需安裝選項的詳細資訊。

1. 安裝其他套件，針對您的機器學習實驗。

    使用下列命令，並取代*\<新的封裝 >* 與您想要安裝的封裝。 安裝套件透過`conda install`必要條件封裝是目前的通道 （Anaconda 定域機組中可以加入新的通道） 的一部分。

    ```shell
    conda install <new package>
    ```

    或者，您可以在其中安裝套件透過`pip`。

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 專案](https://jupyter.org/)的一部分。 它們提供互動式程式碼撰寫體驗，讓您用來建立混合即時程式碼與敘述文字和圖形的文件。 Jupyter Notebook 也是與其他人共用結果的好方法，因為您可以將程式碼區段的輸出儲存在文件中。 您可以在各種不同的平台上安裝 Jupyter Notebook。

中的程序[本機電腦](#local)區段會安裝必要元件，以在 Anaconda 環境中執行的 Jupyter Notebook。 若要在 Jupyter Notebook 環境中啟用這些元件，請執行下列操作：

1. 開啟 Anaconda 提示字元，並啟用您的環境。

    ```shell
    conda activate myenv
    ```

1. 啟動 Jupyter Notebook 伺服器，使用下列命令：

    ```shell
    jupyter notebook
    ```

1. 若要確認 Jupyter Notebook 可用的 SDK，建立**的新**筆記本中，選取**Python 3**以及您的核心，然後執行下列命令中的 notebook 資料格：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 如果您遇到匯入模組的問題，並收到`ModuleNotFoundError`，確定 Jupyter 核心時，已連線到您的環境的正確路徑上，執行下列程式碼中的 Notebook 資料格。

    ```python
    import sys
    sys.path
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
Azure Databricks 是 Azure 雲端中的 Apache Spark 為基礎的環境。 它提供共同作業基礎的 Notebook 環境與 CPU 或 GPU 型的計算叢集。

Azure Databricks 的運作方式與 Azure Machine Learning 服務：
+ 您可以使用 Spark mllib 建立定型模型，並將模型部署至 ACI/AKS 從 Azure Databricks 中。 
+ 您也可以使用[自動化機器學習服務](concept-automated-ml.md)特殊的 Azure ML SDK 與 Azure Databricks 中的功能。
+ 您可以使用 Azure Databricks 作為計算目標，從[Azure 機器學習服務管線](concept-ml-pipelines.md)。 

### <a name="set-up-your-databricks-cluster"></a>設定您的 Databricks 叢集

建立[Databricks 叢集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 只有當您安裝適用於自動化的機器學習服務在 Databricks 上的 SDK，適用於某些設定。
**需要幾分鐘的時間來建立叢集。**

使用這些設定：

| 設定 |適用於| 值 |
|----|---|---|
| 叢集名稱 |永遠| yourclustername |
| Databricks 執行階段 |永遠| 任何非 ML 執行階段 (非 ML 4.x、5.x) |
| Python 版本 |永遠| 3 |
| 背景工作角色 |永遠| 2 個以上 |
| 背景工作節點 VM 類型 <br>（可判斷並行的反覆項目的最大數目） |自動化 ML<br>只有| 建議使用已記憶體最佳化的 VM |
| 啟用自動調整 |自動化 ML<br>只有| 取消選取 |

請靜候至叢集運作，再繼續操作。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>將正確的 SDK 安裝到 Databricks 文件庫
叢集正在執行，一旦[建立程式庫](https://docs.databricks.com/user-guide/libraries.html#create-a-library)將適當的 Azure 機器學習服務 SDK 套件新增至您的叢集。 

1. 選擇**只有一個**（支援任何其他的 SDK 安裝） 的選項

   |SDK&nbsp;封裝&nbsp;額外項目|來源|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks| 上傳 Python Egg 或 PyPI | azureml-sdk[databricks]|
   |-加上-的 databricks<br> 自動化的 ML 功能| 上傳 Python Egg 或 PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > 您可以不安裝任何其他 SDK 額外項目。 選擇上述選項 [databricks] 或 [automl_databricks] 其中之一。

   * 請勿選取**會自動附加到所有叢集**。
   * 選取 **附加**旁您的叢集名稱。

1. 監視錯誤，直到狀態變成**Attached**，這可能需要幾分鐘的時間。  如果這個步驟失敗，請檢查下列各項： 

   請嘗試重新啟動您的叢集：
   1. 在左窗格中，選取 [叢集]。
   1. 請選取表格中您的叢集名稱。
   1. 在 [程式庫] 索引標籤上，選取 [重新啟動]。
      
   也請考慮：
   + 在 Automl 組態中，當使用 Azure Databricks 請新增下列參數：
    1. ```max_concurrent_iterations``` 根據您的叢集中的背景工作節點數目。 
    2. ```spark_context=sc``` #databricks/spark 預設 spark 內容。 
   + 或者，如果您有較舊的 SDK 版本時，取消選取它，從叢集的已安裝的程式庫，並移至垃圾桶。 安裝新版 SDK，並重新啟動叢集。 如果在此之後發生問題，請中斷連結再重新連結叢集。

如果安裝成功，匯入程式庫應該看起來像其中一個：
   
SDK databricks **_不含_** 自動化機器學習服務![Azure 機器學習服務 SDK 的 Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK databricks **WITH**自動化機器學習服務![SDK 會自動安裝在 Databricks 上的機器學習服務](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>開始探索

試試看：
+ 下載[notebook 封存檔](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc)適用於 Azure Databricks/Azure 機器學習服務 SDK 並[封存檔案匯入](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive)至 Databricks 叢集。  
  有許多範例筆記本可供使用，雖然**僅[這些 notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)搭配 Azure Databricks。**
  
+ 了解如何[建立管線，使用做為訓練計算 Databricks](how-to-create-your-first-pipeline.md)。

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

* **請依照下列中的步驟[建立 Azure 機器學習服務工作區](setup-create-workspace.md#sdk)**:*config.json* 檔案是在 Azure Notebook 程式庫中建立的。 此檔案包含您工作區的組態資訊。 您可以將此 *config.json* 下載或複製到其他開發環境。

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

- 在 Azure Machine Learning 使用 MNIST 資料集[定型模型](tutorial-train-models-with-aml.md) \(英文\)
- 檢視[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk) \(英文\) 參考
- 深入了解 [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk) \(英文\)
