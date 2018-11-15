---
title: 設定 Azure Machine Learning 的開發環境 | Microsoft Docs
description: 了解如何在使用 Azure Machine Learning 服務時設定開發環境。 在本文件中，您將了解如何使用 Conda 環境、建立組態檔，以及設定 Jupyter Notebook、Azure Notebooks、IDE 和資料科學虛擬機器。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: 8ce411e424d538a4a1f94300bfe5510658017f56
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238311"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>設定 Azure Machine Learning 的開發環境

本文說明如何設定開發環境以使用 Azure Machine Learning services，包括：

- 如何建立組態檔，以讓您的環境與 Azure Machine Learning services 工作區產生關聯。
- 如何設定下列開發環境：
  - 您自己電腦上的 Jupyter Notebook
  - Visual Studio Code
  - 自訂程式碼編輯器
- 如何設定 [conda 虛擬環境](https://conda.io/docs/user-guide/tasks/manage-environments.html)並用於 Azure Machine Learning。 我們建議使用 Continuum Anaconda 來隔離工作環境，以避免在各個封裝之間產生相依性衝突。

## <a name="prerequisites"></a>必要條件

- 設定 Azure Machine Learning services 工作區。 遵循[開始使用 Azure Machine Learning services](quickstart-get-started.md) 中說明的步驟操作。
- 安裝 [Continuum Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 套件管理員。
- 如果您使用 Visual Studio Code，請取得 [Python 擴充功能](https://code.visualstudio.com/docs/python/python-tutorial)。

> [!NOTE]
> 您可使用 Bash (針對 Linux 與 Mac 作業系統) 或命令提示字元 (針對 Windows)，測試本文中顯示的殼層命令。

## <a name="create-a-workspace-configuration-file"></a>建立工作區組態檔

Azure Machine Learning SDK 會使用工作區組態檔來和您的 Azure Machine Learning services 工作區進行通訊。

- 若要建立組態檔，請完成 [Azure Machine Learning 快速入門](quickstart-get-started.md)。
  - 快速入門程序會在 Azure Notebooks 中建立 `config.json` 檔案。 此檔案包含您工作區的組態資訊。
  - 將 `config.json` 下載或複製至參考此工作區的指令碼或 Notebook 所在目錄。

- 或者，您可遵循下列步驟透過手動方式建置檔案：

    1. 在 [Azure 入口網站](https://portal.azure.com)中開啟工作區。 複製__工作區名稱__、__資源群組__和__訂用帳戶識別碼__。 這些值會用來建立組態檔。
        ![Azure 入口網站](./media/how-to-configure-environment/configure.png)

    1. 請使用下列 Python 程式碼建立檔案，並務必在參考此工作區的指令碼或 Notebook 所在目錄中執行此程式碼：

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        此程式碼會寫入下列 `aml_config/config.json` 檔案：

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        您可以將 `aml_config` 目錄或 `config.json` 複製到任何其他參考工作區的目錄中。

       > [!NOTE]
       > 相同目錄或下方目錄中的其他指令碼或 Notebook，會以 `ws=Workspace.from_config()` 來載入工作區。

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks 與資料科學虛擬機器

Azure Notebooks 和 Azure 資料科學虛擬機器 (DSVM) 已設定為可搭配使用 Azure Machine Learning services。 這些環境已包含所有必要的元件，例如 Azure Machine Learning SDK。

### <a name="azure-notebooks"></a>Azure Notebooks

- Azure Notebooks 是 Azure 雲端中的 Jupyter Notebook 服務。
- 但您仍然需要工作區組態檔來使用這些環境。

如需搭配使用 Azure Notebooks 和 Azure Machine Learning 服務的範例，請參閱[開始使用 Azure Machine Learning services](quickstart-get-started.md)。

### <a name="data-science-virtual-machines"></a>資料科學虛擬機器

- 資料科學虛擬機器是專為資料科學工作設計的自訂虛擬機器 (VM) 映像。 其中包括：
  - 常用的資料科學工具
  - 整合式開發環境 (IDE)，例如 PyCharm 和 RStudio
  - 套件，例如 Jupyter Notebooks 和 Tensorflow

DSVM 隨附多個已經安裝的 Anaconda 環境。 若要在不安裝任何套件的情況下使用 Azure Machine Learning Python SDK，請開啟命令提示字元/殼層，並使用下列其中一個命令啟用環境：

* 在 __Ubuntu__ DSVM 上，使用下列命令：

    ```shell
    conda activate py36
    ```

* 在 __Windows__ DSVM 上，使用下列命令：

    ```shell
    conda activate AzureML
    ```

一旦處於此環境之後，您可以立即在所選擇的建置工具中匯入 Azure Machine Learning SDK，而不需要安裝套件。

```python
import azureml.core
print(azureml.core.VERSION)
```

如需有關資料科學虛擬機器的詳細資訊，請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="configure-jupyter-notebooks-on-your-computer"></a>在您自己的電腦上設定 Jupyter Notebook

1. 開啟命令提示字元或殼層。

1. 使用下列命令建立 Conda 環境：

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    建立環境可能需要幾分鐘的時間，因為或許需要下載 Python 3.6 和其他元件。

1. 使用下列命令安裝 Azure Machine Learning SDK 與 Notebook 的額外項目，以及資料準備 SDK：

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   您可以參閱 Python 參考文件，以了解下列 SDK 中的類別和方法：
   + [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk)
   + [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > 如果顯示訊息表示無法解除安裝 `PyYAML`，請改用下列命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   安裝 SDK 可能需要幾分鐘的時間。

1. 安裝您的機器學習實驗套件。 使用下列命令，將 `<new package>` 取代為想要安裝的套件：

    ```shell
    conda install <new package>
    ```

1. 安裝可感知 Conda 的 Jupyter Notebook 伺服器，並啟用實驗 widget (用於檢視執行資訊)。 使用下列命令：

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. 使用下列命令開啟 Jupyter Notebook：

    ```shell
    jupyter notebook
    ```

1. 開啟新的 Notebook，選取「myenv」做為核心，然後驗證您已安裝 Azure Machine Learning SDK。 在 Notebook 資料格中執行下列命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>設定 Visual Studio Code

1. 開啟命令提示字元或殼層。

1. 使用下列命令建立 Conda 環境：

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. 使用下列命令安裝 Azure Machine Learning SDK 與資料準備 SDK：

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. 安裝適用於 AI 擴充功能的 Visual Studio Code 工具。 參閱[適用於 AI 的工具](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

1. 安裝您的機器學習實驗套件。 使用下列命令，將 `<new package>` 取代為想要安裝的套件：

    ```shell
    conda install <new package>
    ```

1. 開啟 Visual Studio Code，然後再使用 **CTRL-SHIFT-P** (針對 Windows) 或 **COMMAND-SHIFT-P** (針對 Mac 作業系統) 取得**命令選擇區**。 輸入「Python：選取解譯器」，然後選取您所建立的 Conda 環境。

   > [!NOTE]
   > Visual Studio Code 會自動感知您電腦上的 Conda 環境。 如需詳細資訊，請參閱 [Visual Studio 程式碼文件](https://code.visualstudio.com/docs/python/environments#_conda-environments)。

1. 使用 Visual Studio Code 驗證設定，以搭配下列程式碼來建立新的 Python 指令碼檔案，然後再執行它：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>設定自訂程式碼編輯器

您可搭配 Azure Machine Learning SDK 使用您所選擇的程式碼編輯器。

1. 依照上文的[設定 Visual Studio Code](#configure-visual-studio-code) 步驟 2 中所述，建立您的 Conda 環境。
1. 按照每個編輯器的說明指示，使用 Conda 環境。 例如可依照 [PyCharm 指示](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html)進行。

## <a name="next-steps"></a>後續步驟

- [使用 MNIST 資料集訓練 Azure Machine Learning 上的模型](tutorial-train-models-with-aml.md)
