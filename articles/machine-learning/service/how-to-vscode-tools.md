---
title: 搭配使用 Visual Studio Code
titleSuffix: Azure Machine Learning service
description: 了解如何安裝適用於 Visual Studio Code 的 Azure Machine Learning，並在 Azure Machine Learning 中建立簡單實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995246"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>開始使用適用於 Visual Studio Code 的 Azure Machine Learning

在本文中，您將了解如何在 Visual Studio Code (VS Code) 中搭配 Azure Machine Learning 服務使用**適用於 Visual Studio Code 的 Azure Machine Learning**延伸模組，來訓練及部署機器學習和深度學習模型。

Azure Machine Learning 服務支援在本機和遠端計算目標上執行實驗。 對於每個實驗，您可以持續追蹤多個回合，因為您通常需要反覆嘗試不同的技術、超參數和更多功能。 您可以使用 Azure Machine Learning 來追蹤自訂計量和實驗回合，從而能夠重現與稽核資料科學。

此外，您可以基於測試和生產環境需求來部署這些模型。

## <a name="prerequisites"></a>必要條件

+ 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

+ 必須安裝 Visual Studio Code。 VS Code 是一個功能強大的輕量型原始程式碼編輯器，其會在您的桌面上執行。 它隨附 Python 和其他更多產品的內建支援。  [了解如何安裝 VS Code](https://code.visualstudio.com/docs/setup/setup-overview) \(英文\)。

+ [安裝 Python 3.5 或更新版本](https://www.anaconda.com/download/)。


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>安裝適用於 VS Code 的 Azure Machine Learning 擴充功能

當您安裝 **Azure Machine Learning** 擴充功能時，會自動安裝兩個以上的擴充功能 (前提是您具備網際網路存取權)。 它們是 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) \(英文\) 擴充功能和 [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) \(英文\) 擴充功能。

為了與 Azure Machine Learning 搭配運作，我們需要將 VS Code 轉換成 Python IDE。 與 [Visual Studio Code 中的 Python](https://code.visualstudio.com/docs/languages/python) \(英文\) 搭配運作，需要使用 Azure Machine Learning 自動安裝的 Microsoft Python 擴充功能。 此擴充功能會使 VS Code 成為絕佳的 IDE，並搭配各種不同的 Python 解譯器在任何作業系統上運作。 它會利用所有 VS Code 的能力來提供自動完成和 IntelliSense、使用 lint、進行偵錯及單元測試，而且還能輕鬆地在 Python 環境 (包括虛擬和 Conda 環境) 之間進行切換。 如需查看這個編輯、執行和偵錯 Python 程式碼的逐步解說，請參閱 [Python Hello World 教學課程](https://code.visualstudio.com/docs/python/python-tutorial)。

**安裝 Azure Machine Learning 擴充功能：**

1. 啟動 VS Code。

1. 在瀏覽器中，瀏覽：[適用於 Visual Studio Code 的 Azure Machine Learning (預覽)](https://aka.ms/vscodetoolsforai) \(英文\) 擴充功能。

1. 在該網頁上，按一下 [安裝]。 

1. 在擴充功能索引標籤中，按一下 [安裝]。

1. 歡迎使用 索引標籤在 VS Code 中開啟擴充功能和 Azure 的符號 （在下圖中紅色方塊中所述） 會新增至活動列。

   ![Visual Studio Code 活動列中的 Azure 圖示](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在對話方塊中，按一下 [登入]，然後依照螢幕上的提示來向 Azure 進行驗證。 
   
   已與適用於 VS Code 的 Azure Machine Learning 擴充功能一起安裝的 Azure 帳戶擴充功能，可協助使用您的 Azure 帳戶進行驗證。 請參閱 [Azure 帳戶擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) \(英文\) 頁面中的命令清單。

> [!Tip] 
> 請查看[適用於 VS Code 的 IntelliCode 擴充功能 (預覽)](https://go.microsoft.com/fwlink/?linkid=2006060) \(英文\)。 IntelliCode 會在 Python 中提供一組適用於 IntelliSense 的 AI 輔助功能，例如，根據目前的程式碼內容來推斷最相關的自動完成。

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK 安裝

1. 確定已安裝 Python 3.5 或更新版本，並透過 VS Code 來辨識。 如果您立即安裝它，接著重新啟動 VS Code，然後使用 https://code.visualstudio.com/docs/python/python-tutorial 上的指示來選取 Python 解譯器。

1. 在整合式終端機視窗中，指定要使用的 Python 解譯器，或者您可以按 **Enter** 來使用預設的 Python 解譯器。

   ![選擇解譯器](./media/vscode-tools-for-ai/python.png)

1. 視窗的右下角會出現通知，指出正在自動安裝 Azure ML SDK。    會建立本機的私用 Python 環境，具有使用 Azure Machine Learning 服務的 Visual Studio Code 必要條件。

   ![安裝適用於 Python 的 Azure Machine Learning SDK](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>開始使用 Azure Machine Learning

使用 VS Code 開始定型和部署機器學習模型之前，您需要先在雲端建立 [Azure Machine Learning 服務工作區](concept-azure-machine-learning-architecture.md#workspace)來包含您的模型和資源。 了解如何建立一個，以及在該工作區中建立您的第一個實驗。

1. 在 Visual Studio Code 活動列中，按一下 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

   [![安裝](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. 以滑鼠右鍵按一下您的 Azure 訂用帳戶，然後選取 [建立工作區]。 清單隨即出現。 在動畫影像中，訂用帳戶名稱是 'Free Trial'，而工作區是 'TeamWorkspace'。 

1. 從清單中選取現有的資源群組，或在命令選擇區中使用精靈來建立新的資源群組。

1. 在欄位中，為您的新工作區輸入唯一且清楚的名稱。 在螢幕擷取畫面中，工作區名稱為 'TeamWorkspace'。

1. 按 Enter，即會建立新的工作區。 它會出現在訂用帳戶名稱下方的樹狀目錄中。

1. 以滑鼠右鍵按一下實驗節點，然後從捷徑功能表中選擇 [建立實驗]。  實驗會使用 Azure Machine Learning 持續追蹤您的回合。

1. 在欄位中，輸入您的實驗名稱。 在螢幕擷取畫面中，實驗名稱為 'MNIST'。
 
1. 按 Enter，即會建立新的實驗。 它會出現在工作區名稱下方的樹狀目錄中。

1. 您可以滑鼠右鍵按一下工作區中的實驗，然後選取 [設定為使用中實驗]。 [使用中] 實驗是您目前使用的實驗，而您在 VS Code 中開啟的資料夾會連結到雲端中的這項實驗。 此資料夾應該包含您的本機 Python 指令碼。

   現在，您的每個實驗都會透過您的實驗來執行，因此，您的所有關鍵計量都將儲存於實驗歷程記錄中，而您定型的模型將自動上傳至 Azure Machine Learning，並與您的實驗計量和記錄一起儲存。

   [![在 VS Code 中附加資料夾](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


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

7. 在 JSON 組態檔中指定任何進階屬性，此檔案會在新的索引標籤中開啟。您可以指定屬性，例如節點計數上限。

8. 當您設定完計算目標時，按一下畫面右下方的 [提交]。

以下是建立 Azure Machine Learning Compute (AMLCompute) 的範例：[![在 VS Code 中建立 AML Compute](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>「回合組態」檔

VS Code 擴充功能會在您的本機電腦上，自動為您的**本機** 和 **docker** 環境建立本機計算目標和回合組態。 回合的組態檔位於相關聯的計算目標節點下。 

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

根據預設，會為您建立新的 Conda 環境，以及管理您的安裝相依性。 不過，您必須在 `aml_config/conda_dependencies.yml` 檔案中指定相依性和其版本。 

這是來自預設 'aml_config/conda_dependencies.yml' 的程式碼片段。 例如，您可以指定 'tensorflow=1.12.0'，如下所示。 如果您未指定相依性版本，則會使用最新版本。  
您可以在組態檔中新增其他相依性。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

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

### <a name="use-keyboard-shortcuts"></a>使用鍵盤快速鍵

如同大部分的 VS Code，VS Code 中的 Azure Machine Learning 功能均可從鍵盤存取。 需知道的最重要按鍵組合是 Ctrl+Shift+P，其會顯示命令選擇區。 從這裡開始，您可以存取 VS Code 的所有功能，包括最常見作業的鍵盤快速鍵。

[![適用於 VS Code 的 Azure Machine Learning 的鍵盤快速鍵](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>後續步驟

如需有關在 VS Code 外使用 Machine Learning 來進行訓練的逐步解說，請參閱[教學課程：使用 Azure Machine Learning 將模型定型](tutorial-train-models-with-aml.md)。

如需在本機編輯、執行和偵錯程式碼的逐步解說，請參閱 [Python Hello World 教學課程](https://code.visualstudio.com/docs/python/python-tutorial)
