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
ms.openlocfilehash: fa70e0dfa1f131e38e43faa3d80497d50a52e135
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275209"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>設定 Azure Machine Learning 的開發環境

在本文件中，了解如何設定開發環境以使用 Azure Machine Learning 服務。 Azure Machine Learning 服務與平台無關。 開發環境的唯一要求是 __Python 3__、__Conda__ (適用於隔離環境)，以及包含 Azure Machine Learning 工作區資訊的組態檔。

本文件著重於下列特定的環境和工具：

* [Azure Notebooks](#aznotebooks)：裝載於 Azure 雲端中的 Jupyter Notebook 服務。 這是__最簡單的__入門方式，因為已經安裝了 Azure Machine Learning SDK。
* [資料科學虛擬機器](#dsvm)：__專為資料科學工作設計__的虛擬機器。 已安裝 Python 3、Conda、Jupyter Notebook 和 Azure Machine Learning SDK。 VM 隨附用於開發 ML 方案的熱門 ML 架構、工具和編輯器。 針對 Azure 平台上的 ML，它可能是__最完整的__開發環境。
* [Jupyter Notebook](#jupyter)：如果您已經在使用 Jupyter Notebook，那麼 SDK 有一些您應該安裝的附加功能。
* [Visual Studio Code](#vscode)：如果您使用 Visual Studio Code，則可以安裝一些有用的擴充功能。

如果您已經擁有 Python 3 環境，或者只是想要安裝 SDK 的基本步驟，請參閱[本機電腦](#local)一節。

## <a name="prerequisites"></a>必要條件

- Azure Machine Learning 服務工作區。 遵循[開始使用 Azure Machine Learning 服務](quickstart-get-started.md)中說明的步驟來建立一個。

- [Continuum Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 套件管理員。

    > [!IMPORTANT]
    > 使用 Azure Notebooks 時不需要 Anaconda 和 Miniconda。

- 在 Linux 或 Mac OS 上，您需要 bash 殼層。

    > [!TIP]
    > 如果您使用的是 Linux 或 Mac OS 並使用 bash 以外的殼層 (例如，zsh)，則在執行一些命令時可能會收到錯誤。 若要解決此問題，請使用 `bash` 命令啟動新的 bash 殼層，並在其中執行命令。

- 在 Windows 上，您需要命令提示字元或 Anaconda 提示字元 (由 Anaconda 和 Miniconda 安裝)。

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (預覽) 是 Azure 雲端中的互動式開發環境。 這是開始使用 Azure Machine Learning 環境__最簡單的__方式。

* Azure Machine Learning SDK __已安裝__。
* 在 Azure 入口網站中建立 Azure Machine Learning 服務工作區後，您可以按一下按鈕來自動設定您的 Azure Notebook 環境，以使用工作區。

若要開始使用 Azure Notebooks 進行開發，請遵循[開始使用 Azure Machine Learning 服務](quickstart-get-started.md)文件。

## <a id="dsvm"></a>資料科學虛擬機器

資料科學虛擬機器 (DSVM) 是**專為資料科學工作設計**的自訂虛擬機器 (VM) 映像。 其中包括：

  - 常用的資料科學工具
  - 整合式開發環境 (IDE)，例如 PyCharm 和 RStudio
  - 套件，例如 Jupyter Notebooks 和 Tensorflow

Azure Machine Learning SDK 適用於 Ubuntu 或 Windows版本的 DSVM。 若要使用 DSVM 作為開發環境，請遵循下列步驟：

1. 若要建立資料科學虛擬機器，請依照下列其中一個文件中的步驟操作：

    * [建立 Ubuntu 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [建立 Windows 資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. Azure Machine Learning SDK **已安裝**在 DSVM 上。 若要使用包含該 SDK 的 Conda 環境，請使用下列命令之一：

    * 在 __Ubuntu__ DSVM 上，使用此命令：

        ```shell
        conda activate py36
        ```

    * 在 __Windows__ DSVM 上，使用此命令：

        ```shell
        conda activate AzureML
        ```

1. 若要確認您是否可以存取 SDK 並檢查版本，請使用下列 Python 程式碼：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要設定 DSVM 以使用 Azure Machine Learning 服務工作區，請參閱[設定工作區](#workspace)一節。

如需有關資料科學虛擬機器的詳細資訊，請參閱[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a id="local"></a>本機電腦

使用本機電腦 (也可能是遠端虛擬機器) 時，請使用下列步驟來建立 conda 環境並安裝 SDK：

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

   > [!NOTE]
   > 如果顯示訊息表示無法解除安裝 `PyYAML`，請改用下列命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   安裝 SDK 可能需要幾分鐘的時間。

1. 安裝您的機器學習實驗套件。 使用下列命令，將 `<new package>` 取代為想要安裝的套件：

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

[本機電腦](#local)區段中的步驟為 Jupyter Notebook 安裝選用元件。 若要在 Jupyter Notebook 環境中啟用這些元件，請使用下列步驟：

1. 開啟命令提示字元或殼層。

1. 若要安裝可感知 Conda 的 Jupyter Notebook 伺服器，並啟用實驗小工具，請使用下列命令：

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.widgets
    ```

1. 使用下列命令開啟 Jupyter Notebook：

    ```shell
    jupyter notebook
    ```

1. 若要確認 Jupyter Notebook 是否可以使用 SDK，請開啟新的 Notebook 並選取 "myenv" 作為您的核心。 然後，在 Notebook 資料格中執行下列命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要設定 Jupyter Notebook 以使用 Azure Machine Learning 服務工作區，請參閱[設定工作區](#workspace)一節。

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code 是跨平台的程式碼編輯器。 它依賴於 Python 支援的本機 Python 3 和 Conda 安裝，但它提供額外的工具來使用 AI。 它還支援從程式碼編輯器中選取 Conda 環境。

若要使用 Visual Studio Code 進行開發，請使用下列步驟：

1. 若要了解如何使用 Visual Studio Code 進行 Python 開發，請參閱[在 VSCode 中開始使用 Python](https://code.visualstudio.com/docs/python/python-tutorial) 文件。

1. 若要選取 Conda 環境，請開啟 VS Code，然後使用 __Ctrl-Shift-P__ (Linux 和 Windows) 或 __Command-Shift-P__ (Mac) 取得__命令棧板__。 輸入「Python：選取解譯器」，然後選取 Conda 環境。

1. 若要驗證您是否可以使用 SDK，請建立包含下列程式碼的新的 Python 檔案 (.py)。 然後執行檔案：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要安裝適用於 AI 擴充功能的 Visual Studio Code 工具，請參閱[適用於 AI 的工具](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)頁面。

    如需詳細資訊，請參閱[使用適用於 AI 的 VS Code 工具與 Azure Machine Learning](how-to-vscode-tools.md) 文件。

## <a id="workspace"></a>建立工作區組態檔

工作區組態檔是 JSON 文件，可告知 SDK 如何與您的 Azure Machine Learning 服務工作區進行通訊。 檔案名稱為 `config.json`，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

此文件必須位於包含您的 Python 指令碼或 Jupyter Notebook 的目錄結構中。 它可以位於相同的目錄，名為 `aml_config` 的子目錄中，也可以位於父目錄中。

要使用程式碼中的此檔案，請使用 `ws=Workspace.from_config()`。 此程式碼會從檔案載入資訊，並連接到您的工作區。

有三種方式可建立組態檔：

* 如果您遵循 [Azure Machine Learning 快速入門](quickstart-get-started.md)，則會在您的 Azure Notebook 文件庫中建立 `config.json` 檔案。 此檔案包含您工作區的組態資訊。 您可以將此 `config.json` 下載或複製到其他開發環境。

* 您可以使用文字編輯器**手動建立檔案**。 您可以透過瀏覽 [Azure 入口網站](https://portal.azure.com)中的工作區，來查找組態檔中的值。 複製__工作區名稱__、__資源組__和__訂用帳戶識別碼__值，並在組態檔中使用它們。
        ![Azure 入口網站](./media/how-to-configure-environment/configure.png)

* 您可以**以程式設計方式**建立檔案。 下列程式碼片段示範如何藉由提供提供訂用帳戶識別碼、資源群組和工作區名稱來連接到工作區。 然後它會將工作區設定儲存至檔案：

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

    此程式碼會將組態檔寫入 `aml_config/config.json` 檔案。

## <a name="next-steps"></a>後續步驟

- [使用 MNIST 資料集訓練 Azure Machine Learning 上的模型](tutorial-train-models-with-aml.md)
- [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk)
- [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)