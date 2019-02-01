---
title: 從 VS Code 訓練及部署模型
titleSuffix: Azure Machine Learning service
description: 了解適用於 Visual Studio Code 的 Azure Machine Learning，以及如何使用 Visual Studio Code 在 Azure Machine Learning 中開始訓練及部署機器學習和深度學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 409d1bb30dc956bb19e9a37212d93990d1401e7b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240109"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>使用 Visual Studio Code 來訓練及部署機器學習模型

在本文中，您將了解如何在 Visual Studio Code (VS Code) 中搭配 Azure Machine Learning 服務使用**適用於 Visual Studio Code 的 Azure Machine Learning**延伸模組，來訓練及部署機器學習和深度學習模型。

Azure Machine Learning 支援在本機和遠端計算目標上執行實驗。 對於每個實驗，您可以持續追蹤多個回合，因為您通常需要反覆嘗試不同的技術、超參數和更多功能。 您可以使用 Azure Machine Learning 來追蹤自訂計量和實驗回合，從而能夠重現與稽核資料科學。

此外，您可以基於測試和生產環境需求來部署這些模型。

## <a name="prerequisites"></a>必要條件

+ 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

+ 安裝[適用於 VS Code 的 Azure Machine Learning](how-to-vscode-tools.md) 延伸模組。

+ 已使用 VS Code [安裝適用於 Python 的 Azure Machine Learning SDK](how-to-vscode-tools.md)。

## <a name="create-and-manage-compute-targets"></a>建立和管理計算目標

藉由適用於 VS Code 的 Azure Machine Learning，您便可以準備資料、訓練模型，然後將它們部署在本機和遠端計算目標上。

此擴充功能支援適用於 Azure Machine Learning 的數個不同遠端計算目標。 請參閱適用於 Azure Machine Learning 之[支援計算目標的完整清單](how-to-set-up-training-targets.md)。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>在 VS Code 中建立適用於 Azure Machine Learning 的計算目標

**建立計算目標：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

2. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 在動畫影像中，訂用帳戶名稱是 'Free Trial'，而工作區則是 'TeamWorkspace'。

3. 在工作區節點下方，以滑鼠右鍵按一下 [計算] 節點，然後選擇 [建立計算]。

4. 從清單中選擇計算目標類型。

5. 在命令選擇區中，選取虛擬機器大小。

6. 在命令選擇區中，於欄位輸入計算目標的名稱。

7. 在 JSON 組態檔中指定任何進階屬性，此檔案會在新的索引標籤中開啟。您可以指定屬性，例如最大節點計數。

8. 當您設定完計算目標時，按一下畫面右下方的 [提交]。

以下是建立 Azure Machine Learning Compute (AMLCompute) 的範例：[![在 VS Code 中建立 AML Compute](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>「回合組態」檔

VS Code 擴充功能會在您的本機電腦上，自動為您的**本機** 和 **docker** 環境建立本機計算目標和回合組態。 回合組態檔位於相關聯的計算目標之下。

這是來自預設本機回合組態檔的程式碼片段。 根據預設為 `userManagedDependencies: True`，所以您必須自行安裝所有的程式庫/相依性，而本機實驗回合接著將會使用您的預設 Python 環境，如 VS Code Python 擴充功能所指定。

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that Azure Machine Learning service will manage the user environment.
    userManagedDependencies: True
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

## <a name="train-and-tune-models"></a>定型及調整模型

使用適用於 VS Code 的 Azure Machine Learning (預覽) 來快速逐一查看您的程式碼、逐步執行並偵錯，以及使用您所選擇的原始程式碼控制解決方案。

**使用 Azure Machine Learning 在本機執行您的實驗：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。

1. 在工作區節點下方，展開 [計算] 節點，然後以滑鼠右鍵按一下您想要使用之計算的 [回合組態]。

1. 選取 [執行實驗]。

1. 從 [檔案總管] 中選取要執行的指令碼。

1. 按一下 [檢視實驗回合] 來查看整合的 Azure Machine Learning 入口網站，以監視您的回合並查看您的定型模型。

以下是在本機執行實驗的範例：[![在本機執行實驗](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>在 VS Code 中使用遠端計算進行實驗

若要在定型時使用遠端計算目標，您需要建立回合組態檔。 此檔案不只會告訴 Azure Machine Learning 要在哪裡執行您的實驗，而且還會告知如何準備環境。

#### <a name="the-conda-dependencies-file"></a>Conda 相依性檔案

根據預設，會為您建立新的 Conda 環境，以及管理您的安裝相依性。 不過，您必須在 `aml_config/conda_dependencies.yml` 檔案中指定相依性。

這是來自預設 'aml_config/conda_dependencies.yml' 的程式碼片段。
您可以在組態檔中新增其他相依性。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure Machine Learning service only supports 3.5.2 and later.

- python=3.6.2
- tensorflow

- pip:
    # Required packages for Azure Machine Learning service execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**若要在遠端計算目標上使用 Azure Machine Learning 執行您的實驗：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。

1. 在碼編輯器視窗中以滑鼠右鍵按一下您的 python 指令，然後選取 [AML：在 Azure 中當作實驗執行]。

1. 在命令選擇區中，選取計算目標。

1. 在命令選擇區中，於欄位輸入回合組態名稱。

1. 編輯 conda_dependencies.yml 檔案，以指定實驗的執行階段相依性，然後按一下畫面右下方的 [提交]。

1. 按一下 [檢視實驗回合] 來查看整合的 Azure Machine Learning 入口網站，以監視您的回合並查看您的定型模型。

以下是在遠端計算目標上執行實驗的範例：[![在遠端目標上執行實驗](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>部署和管理模型
Azure Machine Learning 能夠在雲端中和 Edge 上部署和管理您的機器學習模型。

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>從 VS Code 向 Azure Machine Learning 註冊您的模型

既然已將模型定型，您就能在工作區中註冊該模型。
您可以追蹤和部署已註冊的模型。

**註冊您的模型：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。

1. 在工作區節點下方，以滑鼠右鍵按一下 [模型]，然後選擇 [註冊模型]。

1. 在命令選擇區中，於欄位輸入模型名稱。

1. 從清單中，選擇您是否要上傳**模型檔案** (適用於單一模型) 或**模型資料夾** (適用於具有多個檔案的模型，例如 Tensorflow)。

1. 選取您的資料夾或檔案。

1. 當您設定完模型屬性時，按一下畫面右下方的 [提交]。

以下是向 AML 註冊模型的範例：[![向 AML 註冊模型](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>從 VS Code 部署您的服務

您可以使用 VS Code，將 Web 服務部署到：
+ Azure 容器執行個體 (ACI)：用於測試
+ Azure Kubernetes Service (AKS)：用於生產環境

您不需預先建立要測試的 ACI 容器，因為它們會即時建立。 不過，AKS 叢集需要預先設定。

深入了解通常如何[使用 Azure Machine Learning 進行部署](how-to-deploy-and-where.md)。

**部署 Web 服務：**

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和您的 Azure Machine Learning services 工作區。

1. 在工作區節點下方，展開 [模型] 節點。

1. 以滑鼠右鍵按一下您想要部署的模型，然後從內容功能表中選擇 [從已註冊的模型部署服務] 命令。

1. 在「命令選擇區」中，從清單中選擇要作為部署目的地的計算目標。

1. 在命令選擇區中，於欄位輸入此服務的名稱。

1. 在「命令選擇區」中，按您鍵盤上的 Enter 鍵以瀏覽並選取指令檔。

1. 在「命令選擇區」中，按您鍵盤上的 Enter 鍵以瀏覽並選取 Conda 相依性檔案。

1. 當您設定完服務屬性時，按一下畫面右下方的 [提交] 進行部署。 在這個服務屬性檔案中，您可以指定可能想要使用的本機 Docker 檔案或 schema.json 檔案。

現在已部署 Web 服務。

以下是部署 Web 服務的範例：[![部署 Web 服務](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

## <a name="next-steps"></a>後續步驟

如需有關在 VS Code 外使用 Machine Learning 來進行訓練的逐步解說，請參閱[教學課程：使用 Azure Machine Learning 將模型定型](tutorial-train-models-with-aml.md)。

如需在本機編輯、執行和偵錯程式碼的逐步解說，請參閱 [Python Hello World 教學課程](https://code.visualstudio.com/docs/python/python-tutorial)
