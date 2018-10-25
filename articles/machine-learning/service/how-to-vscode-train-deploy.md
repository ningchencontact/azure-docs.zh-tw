---
title: 搭配使用 Visual Studio Code Tools for AI 擴充功能與 Azure Machine Learning
description: 了解 Visual Studio Code Tools for AI，以及如何使用 VS Code 中的 Azure Machine Learning services，開始定型和部署機器學習和深度學習模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 3a5ee8795dabcbf9e35e16a8ba0f9c0eb5490ead
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240684"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>VS Code Tools for AI：從 VS Code 定型和部署 ML 模型
在此文章中，您將了解如何使用 **VS Code Tools for AI** 擴充功能，透過 VS Code 中的 Azure Machine Learning services 來定型和部署機器學習和深度學習模型。

Azure Machine Learning 支援在本機和遠端計算目標上執行實驗。 對於每個實驗，您可以持續追蹤多個回合，因為您通常需要反覆嘗試不同的技術、超參數和更多功能。 您可以使用 Azure Machine Learning 來追蹤自訂計量和實驗回合，從而能夠重現與稽核資料科學。

此外，您可以基於測試和生產環境需求來部署這些模型。

## <a name="prerequisites"></a>先決條件

+ 已針對 Azure Machine Learning [設定 VS Code Tools for AI](how-to-vscode-tools.md)。

+ 已使用 VS Code [安裝適用於 Python 的 Azure Machine Learning SDK](how-to-vscode-tools.md)。

+ 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-and-manage-compute-targets"></a>建立和管理計算目標

使用 Visual Studio Code Tools for AI，您可以準備資料、將模型定型，並將它們部署在本機和遠端計算目標上。

此擴充功能支援適用於 Azure Machine Learning 的數個不同遠端計算目標。 請參閱適用於 Azure Machine Learning 之[支援計算目標的完整清單](how-to-set-up-training-targets.md)。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>在 VS Code 中建立適用於 Azure Machine Learning 的計算目標

**建立計算目標：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 [Azure: Machine Learning] 提要欄位隨即出現。

2. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 在動畫影像中，訂用帳戶名稱是 'OpenMind Studio'，而工作區是 'MyWorkspace'。 

3. 在工作區節點下方，以滑鼠右鍵按一下 [計算] 節點，然後選擇 [建立計算]。

4. 從清單中選擇計算目標類型。 

5. 在欄位中，輸入此計算目標的唯一名稱，並指定虛擬機器的大小。

6. 在 JSON 組態檔中指定任何進階屬性，此檔案會在新的索引標籤中開啟。 

7. 當您完成設定計算目標之後，按一下右下角的 [完成]。

Azure Batch AI 範例如下：[![在 VS Code 中建立 Azure Batch AI 計算](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>在 VS Code 中使用遠端計算進行實驗

若要在定型時使用遠端計算目標，您需要建立回合組態檔。 此檔案不只會告訴 Azure Machine Learning 要在哪裡執行您的實驗，而且還會告知如何準備環境。

#### <a name="the-run-configuration-file"></a>「回合組態」檔

VS Code 擴充功能將在您的本機電腦上，自動為您的**本機** 和 **docker** 環境建立回合組態。

這是來自預設回合組態檔的程式碼片段。

如果您想要自行安裝所有程式庫/相依性，請設定 `userManagedDependencies: True`，本機實驗回合接著將會使用您的預設 Python 環境，如 VS Code Python 擴充功能所指定。

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

#### <a name="the-conda-dependencies-file"></a>Conda 相依性檔案

根據預設，會為您建立新的 Conda 環境，以及管理您的安裝相依性。 不過，您必須在 `aml_config/conda_dependencies.yml` 檔案中指定相依性。

這是來自預設 'aml_config/conda_dependencies.yml' 的程式碼片段。
您可以在組態檔中新增其他相依性。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>定型及調整模型

從 VS Code 使用 Azure Machine Learning，快速逐一查看您的程式碼、逐步執行並偵錯，以及使用您所選擇的原始程式碼控制解決方案。 

**使用 Azure Machine Learning 執行您的實驗：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 [Azure: Machine Learning] 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 在動畫影像中，訂用帳戶名稱是 'OpenMind Studio'，而工作區是 'MyWorkspace'。 

1. 在工作區節點下方，展開 [計算] 節點，然後以滑鼠右鍵按一下您想要使用之計算的 [回合組態]。 

1. 選取 [執行實驗]。

1. 按一下 [檢視實驗回合] 來查看整合的 Azure Machine Learning 入口網站，以監視您的回合並查看您的定型模型。

   [![從 VS Code 執行機器學習實驗](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>部署和管理模型
Azure Machine Learning 能夠在雲端中和 Edge 上部署和管理您的機器學習模型。 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>從 VS Code 向 Azure Machine Learning 註冊您的模型

既然已將模型定型，您就能在工作區中註冊該模型。
您可以追蹤和部署已註冊的模型。

**註冊您的模型：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 [Azure: Machine Learning] 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。

1. 在工作區節點下方，以滑鼠右鍵按一下 [模型]，然後選擇 [註冊模型]。

1. 從清單中，選擇您是否要上傳**模型檔案** (適用於單一模型) 或**模型資料夾** (適用於具有多個檔案的模型，例如 Tensorflow)。 

1. 使用檔案選擇器對話方塊來選取檔案或資料夾。

   [![計算](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> 現在，請從產生的 JSON 檔案中移除標記。

### <a name="deploy-your-service-from-vs-code"></a>從 VS Code 部署您的服務

您可以使用 VS Code，將 Web 服務部署到：
+ Azure 容器執行個體 (ACI)：用於測試
+ Azure Kubernetes Service (AKS)：用於生產環境 

您不需預先建立要測試的 ACI 容器，因為它們會即時建立。 不過，AKS 叢集需要預先設定。 

深入了解通常如何[使用 Azure Machine Learning 進行部署](how-to-deploy-and-where.md)。

**部署 Web 服務：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 [Azure: Machine Learning] 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和您的 Azure Machine Learning services 工作區。

1. 在工作區節點下方，展開 [模型] 節點。

1. 以滑鼠右鍵按一下您想要部署的模型，然後從內容功能表中選擇 [從已註冊的模型部署服務] 命令。

1. 在 VS Code 命令選擇區中，從清單中選擇要部署的計算目標。 

1. 在欄位中，輸入此服務的名稱。 

1. 在對話方塊中，按一下右下角的 [瀏覽]，然後選取您的評分指令碼。 對話方塊即會關閉。

1. 如果您具有本機 Docker 檔案，在第二個顯示的對話方塊中按一下 [瀏覽]。 

   如果您取消該對話方塊且未指定本機 Docker 檔案，則預設會使用 ' Azure Machine Learning' 檔案。

1. 在第三個顯示的對話方塊中，按一下 [瀏覽]，然後選取本機 Conda 檔案路徑，或稍後在 json 編輯器中輸入檔案路徑。

1. 如果您具有想要使用的 schema.json 檔案，請在第四個顯示的對話方塊中按一下 [瀏覽]，然後選取檔案。

現在已部署 Web 服務。

以下是 Azure 容器執行個體的範例：[![來自 VS Code 的 Azure 容器執行個體](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>後續步驟

如需在 VS Code 外部使用 Machine Learning 來定型的逐步解說，請參閱[教學課程：使用 Azure Machine Learning 將模型定型](tutorial-train-models-with-aml.md)。

如需在本機編輯、執行和偵錯程式碼的逐步解說，請參閱 [Python Hello World 教學課程](https://code.visualstudio.com/docs/languages/python/docs/python/python-tutorial)
