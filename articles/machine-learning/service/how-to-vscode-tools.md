---
title: 使用適用于機器學習服務的 Visual Studio Code
titleSuffix: Azure Machine Learning service
description: 了解如何安裝適用於 Visual Studio Code 的 Azure Machine Learning，並在 Azure Machine Learning 中建立簡單實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: b1d18006e6ec3ff1659f8fb80f8cf6766797d76b
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114423"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>開始使用適用於 Visual Studio Code 的 Azure Machine Learning

在本文中, 您將瞭解如何使用適用于 Visual Studio Code 的 Azure Machine Learning 延伸模組來定型及部署機器學習和深度學習模型。

[Azure Machine Learning 服務](overview-what-is-azure-ml.md)提供您在本機和遠端計算目標上執行的實驗支援。 對於每個實驗，您可以持續追蹤多個回合，因為您通常需要反覆嘗試不同的技術、超參數和更多功能。 您可以使用 Azure Machine Learning 來追蹤自訂計量和實驗回合，從而能夠重現與稽核資料科學。

您也可以為測試和生產需求部署這些模型。

## <a name="prerequisites"></a>先決條件

+ 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

+ 必須安裝 Visual Studio Code。 Visual Studio Code 是輕量但功能強大的原始程式碼編輯器, 可在您的桌面上執行。 它隨附內建的 Python 和其他程式設計語言支援。 如果您還未安裝 Visual Studio Code, [請瞭解做法](https://code.visualstudio.com/docs/setup/setup-overview)。

+ [安裝 Python 3.5 或更新版本](https://www.anaconda.com/download/)。


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>為 Visual Studio Code 安裝 Azure Machine Learning 的延伸模組

當您安裝 Azure Machine Learning 擴充功能時, 會自動安裝兩個擴充功能 (如果您有網際網路存取)。 它們是[Azure 帳戶延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)模組和[Microsoft Python 擴充](https://marketplace.visualstudio.com/items?itemName=ms-python.python)功能。

若要使用 Azure Machine Learning, 您需要將 Visual Studio Code 轉換成 Python 整合式開發環境 (IDE)。 您需要 Microsoft Python 延伸模組, 才能[在 Visual Studio Code 中使用 Python](https://code.visualstudio.com/docs/languages/python)。 此延伸模組會隨 Azure Machine Learning 擴充功能自動安裝。 延伸模組 Visual Studio Code 絕佳的 IDE, 而且可以在任何具有各種 Python 解譯器的作業系統上運作。 Microsoft Python 延伸模組會使用 Visual Studio Code 的所有功能, 以提供自動完成、IntelliSense、linting、調試和單元測試。 延伸模組也可讓您輕鬆地在 Python 環境之間切換, 包括虛擬和 conda 環境。 如需有關編輯、執行及檢查 Python 程式碼的詳細資訊, 請參閱[python hello world 教學](https://code.visualstudio.com/docs/python/python-tutorial)課程。

若要安裝 Azure Machine Learning 延伸模組:

1. 開啟 Visual Studio Code。

1. 在網頁瀏覽器中, 移至[Visual Studio Code 延伸模組的 Azure Machine Learning (預覽)](https://aka.ms/vscodetoolsforai)。

1. 在該網頁上, 選取 [**安裝**]。 

1. 在 [擴充功能] 索引標籤上, 選取 [**安裝**]。

1. 延伸模組的 [歡迎使用] 索引標籤會在 Visual Studio Code 中開啟, 而 Azure 符號 (在下列螢幕擷取畫面中以紅色標示) 會新增至活動列。

   ![Visual Studio Code 活動列上的 Azure 圖示](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在對話方塊中, 選取 [登**入**], 並遵循提示向 Azure 進行驗證。 
   
   與 Visual Studio Code 延伸模組的 Azure Machine Learning 一起安裝的 Azure 帳戶擴充功能, 可協助您使用 Azure 帳戶進行驗證。 如需命令清單, 請參閱[Azure 帳戶延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)模組的頁面。

> [!Tip] 
> 查看 Visual Studio Code 的[IntelliCode 延伸模組 (預覽)](https://go.microsoft.com/fwlink/?linkid=2006060)。 IntelliCode 會針對 Python 中的 IntelliSense 提供一組 AI 輔助的功能, 例如根據目前的程式碼內容推斷最相關的 autocompletions。

## <a name="install-the-azure-machine-learning-sdk"></a>安裝 Azure Machine Learning SDK

1. 請確定已安裝 Python 3.5 或更新版本, 且 Visual Studio Code 加以辨識。 如果您現在安裝它, 請重新開機 Visual Studio Code 並[選取 Python 解釋](https://code.visualstudio.com/docs/python/python-tutorial)器。

1. 在整合式終端機視窗中, 指定要使用的 Python 解譯器。 或者, 選取 Enter 來使用預設的 Python 解譯器。

   ![選擇解譯器](./media/vscode-tools-for-ai/python.png)

1. 視窗的右下角會顯示通知, 指出正在自動安裝[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 。 新建立的 Python 環境是本機和私人, 而且具有使用 Azure Machine Learning 服務的 Visual Studio Code 必要條件。

   ![安裝適用於 Python 的 Azure Machine Learning SDK](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>開始使用 Azure Machine Learning

在 Visual Studio Code 中開始訓練和部署機器學習模型之前, 您需要在雲端中建立[Azure Machine Learning 服務工作區](concept-workspace.md)。 此工作區將包含您的模型和資源。 

若要建立工作區並新增您的第一個實驗:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

   [![建立工作區](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. 以滑鼠右鍵按一下您的 Azure 訂用帳戶，然後選取 [建立工作區]。 清單隨即出現。 在範例動畫影像中, 訂用帳戶名稱為**免費試用版**, 而且工作區為**TeamWorkspace**。 

1. 從清單中選取資源群組, 或使用 [命令選擇區] 中的 [建立新的]。

1. 在欄位中，為您的新工作區輸入唯一且清楚的名稱。 在範例影像中, 工作區的名稱為**TeamWorkspace**。

1. 選取 Enter 以建立新的工作區。 它會出現在樹狀目錄中的訂用帳戶名稱底下。

1. 以滑鼠右鍵按一下 [**實驗**] 節點, 然後從內容功能表中選擇 [**建立實驗**]。  實驗會使用 Azure Machine Learning 持續追蹤您的回合。

1. 在欄位中, 輸入您的實驗名稱。 在範例螢幕擷取畫面中, 實驗的名稱是**MNIST**。
 
1. 選取 Enter 以建立新的實驗。 實驗會出現在樹狀結構的工作區名稱下方。

1. 在工作區中, 您可以用滑鼠右鍵按一下實驗, 將其設定為作用中的實驗。 作用中的實驗是您目前的實驗。 您在 Visual Studio Code 中開啟的資料夾將會連結到雲端中的這項實驗。 此資料夾應該包含您的本機 Python 指令碼。

現在, 您的金鑰計量會儲存在實驗歷程記錄中。 同樣地, 您定型的模型將自動上傳至 Azure Machine Learning, 並與您的實驗計量和記錄一起儲存。 

[![在 Visual Studio Code 中附加資料夾](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>建立和管理計算目標

使用 Visual Studio Code 的 Azure Machine Learning, 您可以準備資料、將模型定型, 並將它們部署在本機和遠端計算目標上。

延伸模組支援 Azure Machine Learning 的數個遠端計算目標。 如需詳細資訊, 請參閱 Azure Machine Learning 支援的[計算目標](how-to-set-up-training-targets.md)完整清單。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>在 Visual Studio Code 中建立 Azure Machine Learning 的計算目標

若要建立計算目標:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

2. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 在下列範例影像中, 訂用帳戶名稱為**免費試用版**, 而且工作區為**TeamWorkspace**。 

3. 在工作區節點下方，以滑鼠右鍵按一下 [計算] 節點，然後選擇 [建立計算]。

4. 從清單中選擇計算目標類型。 

5. 在 [命令選擇區] 中, 選取虛擬機器大小。

6. 在 [命令選擇區] 的欄位中, 輸入計算目標的名稱。 

7. 在新索引標籤上開啟的 JSON 設定檔中, 指定任何 [advanced] 屬性。 您可以指定屬性, 例如節點計數上限。

8. 當您完成計算目標的設定時, 請在視窗的右下角選取 [**提交**]。

以下是如何建立 Azure Machine Learning 計算 (AMLCompute) 的範例:

[![在 Visual Studio Code 中建立 AML 計算](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>執行設定檔

Visual Studio Code 延伸模組會自動建立本機計算目標, 並在您的本機電腦上執行本機和 docker 環境的設定。 您可以在相關聯的計算目標節點底下找到執行設定檔。 

## <a name="train-and-tune-models"></a>定型及調整模型

使用適用于 Visual Studio Code 的 Azure Machine Learning (預覽), 快速逐一查看程式碼、逐步執行和 debug, 並使用您的方案進行原始程式碼控制。 

若要使用 Azure Machine Learning 在本機執行您的實驗:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 

1. 在 [工作區] 節點底下, 展開 [**計算**] 節點, 然後以滑鼠右鍵按一下您想要使用之計算的 [**執行**設定]。 

1. 選取 [執行實驗]。

1. 從 [檔案瀏覽器] 中, 選取您要執行的腳本。 

1. 選取 [ **View 實驗執行**] 以查看整合式 Azure Machine Learning 入口網站來監視您的執行, 並查看您的定型模型。

以下是如何在本機執行實驗的範例:

[![在本機執行實驗](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>在 Visual Studio Code 中使用實驗的遠端計算

若要使用遠端計算目標進行定型, 您需要建立執行設定檔。 此檔案不只會告訴 Azure Machine Learning 要在哪裡執行您的實驗，而且還會告知如何準備環境。

#### <a name="the-conda-dependencies-file"></a>Conda 相依性檔案

根據預設, 系統會為您建立新的 conda 環境, 並管理您的安裝相依性。 不過, 您必須在*aml_config/conda_dependencies. yml*檔案中指定相依性及其版本。 

下列來自預設*aml_config/conda_dependencies*的程式碼片段。 yml 指定`tensorflow=1.12.0`。 如果您未指定相依性的版本, 則會使用最新版本。 您可以在組態檔中新增其他相依性。

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

若要在遠端計算目標上使用 Azure Machine Learning 執行實驗:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 

1. 在 [編輯器] 視窗中, 以滑鼠右鍵按一下您的 Python **腳本, 然後選取 [AML:** 在 Azure 中當作實驗執行]。 

1. 在 [命令選擇區] 中, 選取計算目標。 

1. 在 [命令選擇區] 的欄位中, 輸入回合設定名稱。 

1. 編輯*conda_dependencies yml*檔案, 以指定實驗的執行時間相依性。 然後在視窗的右下角, 選取 [**提交**]。 

1. 選取 [ **View 實驗執行**] 以查看整合式 Azure Machine Learning 入口網站來監視您的執行, 並查看您的定型模型。

以下是如何在遠端計算目標上執行實驗的範例:

[![在遠端目標上執行實驗](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>部署和管理模型
在 Azure Machine Learning 中, 您可以在雲端和邊緣部署和管理您的機器學習模型。 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>註冊您的模型以從 Visual Studio Code Azure Machine Learning

既然您已訓練模型, 您可以在工作區中註冊它。 您可以追蹤和部署已註冊的模型。

若要註冊您的模型:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。

1. 在工作區節點下方，以滑鼠右鍵按一下 [模型]，然後選擇 [註冊模型]。

1. 在 [命令選擇區] 的欄位中, 輸入模型名稱。 

1. 從清單中, 選擇是否要上傳**模型**檔案 (適用于單一模型) 或**模型資料夾**(適用于具有多個檔案的模型, 例如 TensorFlow)。 

1. 選取您的資料夾或檔案。

1. 當您完成模型屬性的設定時, 請在視窗的右下角選取 [**提交**]。 

以下是如何註冊模型以 Azure Machine Learning 的範例:

[![向 AML 註冊模型](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>從 Visual Studio Code 部署您的服務

在 Visual Studio Code 中, 您可以將 web 服務部署至:
+ 用於測試的 Azure 容器實例 (ACI)。
+ 適用于生產環境的 Azure Kubernetes Service (AKS)。

您不需要事先建立 ACI 容器來進行測試, 因為 ACI 容器是即時建立的。 不過, 您必須事先設定 AKS 叢集。 如需詳細資訊, 請參閱[使用 Azure Machine Learning 服務部署模型](how-to-deploy-and-where.md)。

若要部署 web 服務:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和您的 Azure Machine Learning services 工作區。

1. 在工作區節點下方，展開 [模型] 節點。

1. 以滑鼠右鍵按一下您要部署的模型, 然後從內容功能表選擇 [**從已註冊的模型部署服務**]。

1. 在 [命令選擇區] 中, 選擇您想要部署到的計算目標。 

1. 在 [命令選擇區] 的欄位中, 輸入此服務的名稱。  

1. 在 [命令選擇區] 中, 選取鍵盤上的 Enter 鍵以流覽並選取腳本檔案。

1. 在 [命令選擇區] 中, 選取鍵盤上的 Enter 鍵以流覽並選取 conda 相依性檔案。

1. 當您完成設定服務屬性時, 請在視窗的右下角選取 [**提交**] 以進行部署。 在服務屬性檔案中, 您可以指定本機 docker 檔案或架構. json 檔案。

現在已部署 Web 服務。

以下是如何部署 web 服務的範例:

[![部署 web 服務](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>使用鍵盤快速鍵

您可以使用鍵盤來存取 Visual Studio Code 中的 Azure Machine Learning 功能。 要知道的最重要鍵盤快速鍵是 Ctrl + Shift + P, 這會顯示命令選擇區。 從命令選擇區中, 您可以存取 Visual Studio Code 的所有功能, 包括最常見作業的鍵盤快速鍵。

[![Visual Studio Code Azure Machine Learning 的鍵盤快速鍵](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>後續步驟

* 如需如何使用 Visual Studio Code 外的 Azure Machine Learning 進行定型的逐步解說, [請參閱教學課程:使用 Azure Machine Learning 將模型定型](tutorial-train-models-with-aml.md)。
* 如需如何在本機編輯、執行及偵錯工具代碼的逐步解說, 請參閱[Python hello world 教學](https://code.visualstudio.com/docs/python/python-tutorial)課程。
