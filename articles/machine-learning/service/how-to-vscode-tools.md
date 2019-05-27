---
title: 適用於 machine learning 中使用 Visual Studio Code
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
ms.openlocfilehash: 70f9c34957b977aff9fc6211bf79415ed9abe255
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016516"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>開始使用適用於 Visual Studio Code 的 Azure Machine Learning

在本文中，您將了解如何使用適用於 Visual Studio Code 的 Azure Machine learning 的延伸模組來定型和部署機器學習和深度學習模型。

[Azure Machine Learning 服務](overview-what-is-azure-ml.md)提供支援，針對您在本機和遠端執行的實驗的計算目標。 對於每個實驗，您可以持續追蹤多個回合，因為您通常需要反覆嘗試不同的技術、超參數和更多功能。 您可以使用 Azure Machine Learning 來追蹤自訂計量和實驗回合，從而能夠重現與稽核資料科學。

您也可以部署這些模型，針對您測試和生產環境的需求。

## <a name="prerequisites"></a>必要條件

+ 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 請嘗試[在 Azure 機器學習服務免費或付費版](https://aka.ms/AMLFree)。

+ 必須安裝 Visual Studio Code。 Visual Studio Code 是輕巧但功能強大的原始程式碼編輯器，在您的桌面上執行。 它是 Python 和其他程式設計語言內建支援。 如果您尚未安裝 Visual Studio Code[了解如何](https://code.visualstudio.com/docs/setup/setup-overview)。

+ [安裝 Python 3.5 或更新版本](https://www.anaconda.com/download/)。


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>安裝 Azure Machine Learning for Visual Studio Code 的延伸模組

當您安裝 Azure Machine Learning 延伸模組時，兩個的多個延伸模組會自動安裝 （如果您有網際網路存取）。 它們[Azure 帳戶延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)並[Microsoft Python 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

若要使用 Azure Machine Learning，您需要將 Visual Studio Code 轉換成 Python 整合式開發環境 (IDE)。 您需要使用 Microsoft Python 延伸模組[在 Visual Studio Code 的 Python](https://code.visualstudio.com/docs/languages/python)。 此延伸模組會自動安裝 Azure Machine Learning 副檔名。 擴充功能可讓 Visual Studio Code 的極佳的 IDE，並在各種不同的 Python 解譯器使用任何作業系統上運作。 使用 Microsoft 的 Python 擴充功能會使用所有的 Visual Studio Code 強大提供自動完成、 IntelliSense、 linting、 偵錯和單元測試。 擴充功能也可讓您輕鬆地切換 Python 環境，包括虛擬和 conda 環境。 如需有關編輯、 執行和偵錯的 Python 程式碼，請參閱[Python hello world 教學課程](https://code.visualstudio.com/docs/python/python-tutorial)。

若要安裝 Azure Machine Learning 延伸模組：

1. 開啟 Visual Studio Code。

1. 在 web 瀏覽器中，移至[Visual Studio Code 擴充功能 （預覽） 的 Azure Machine Learning](https://aka.ms/vscodetoolsforai)。

1. 在該網頁中，選取**安裝**。 

1. 在 [延伸模組] 索引標籤中，選取**安裝**。

1. 延伸模組的 [歡迎使用] 索引標籤開啟在 Visual Studio Code 和 Azure （下列螢幕擷取畫面中以紅色所述） 的符號加入至活動列。

   ![在 Visual Studio Code 活動列上的 azure 圖示](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在對話方塊中，選取**登入**並遵循提示以使用 Azure 進行驗證。 
   
   Azure 帳戶延伸模組，以及 Azure Machine Learning 的 Visual Studio Code 擴充功能安裝，可協助您使用您的 Azure 帳戶進行驗證。 如需命令的清單，請參閱頁面[Azure 帳戶延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。

> [!Tip] 
> 請參閱[IntelliCode 延伸模組，適用於 Visual Studio Code （預覽）](https://go.microsoft.com/fwlink/?linkid=2006060)。 IntelliCode 適用於在 Python 中，IntelliSense 提供一組的 AI 輔助功能，例如推斷最相關的 autocompletions 根據目前的程式碼內容。

## <a name="install-the-azure-machine-learning-sdk"></a>安裝 Azure Machine Learning SDK

1. 請確定已安裝 Python 3.5 或更新版本，且能辨識的 Visual Studio Code。 如果您立即安裝它，請重新啟動 Visual Studio Code 並[選取 Python 解譯器](https://code.visualstudio.com/docs/python/python-tutorial)。

1. 在整合式終端機視窗中，指定要使用的 Python 解譯器。 或選取 enter 鍵以使用預設的 Python 解譯器。

   ![選擇解譯器](./media/vscode-tools-for-ai/python.png)

1. 在視窗的右下角，通知會出現，表示[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)自動安裝。 新建立的 Python 環境為本機和私用，而且其使用 Azure Machine Learning 服務的 Visual Studio Code 必要條件。

   ![安裝適用於 Python 的 Azure Machine Learning SDK](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>開始使用 Azure Machine Learning

開始訓練和部署機器學習服務模型，在 Visual Studio Code 中的，您需要建立之前[Azure 機器學習服務工作區](concept-workspace.md)在雲端中。 此工作區會包含您的模型和資源。 

若要建立工作區，並新增您的第一個實驗：

1. 在 Visual Studio Code 活動列中，選取 [Azure] 圖示。 Azure Machine Learning 提要欄位隨即出現。

   [![建立工作區](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. 以滑鼠右鍵按一下您的 Azure 訂用帳戶，然後選取 [建立工作區]。 清單隨即出現。 在範例動畫影像中，訂用帳戶名稱是**免費試用**，工作區，而且**TeamWorkspace**。 

1. 從清單中選取資源群組，或建立一個新的使用命令選擇區中的精靈。

1. 在欄位中，為您的新工作區輸入唯一且清楚的名稱。 在範例影像中，名為工作區**TeamWorkspace**。

1. 選取 Enter，以建立新的工作區。 它會出現在樹狀目錄中，訂用帳戶名稱下面。

1. 以滑鼠右鍵按一下**實驗**節點，然後選擇**建立實驗**從內容功能表。  實驗會使用 Azure Machine Learning 持續追蹤您的回合。

1. 在欄位中，輸入您的實驗的名稱。 在範例螢幕擷取畫面中，名為實驗**MNIST**。
 
1. 選取 Enter，以建立新的實驗。 實驗會出現在樹狀目錄中，下列工作區名稱。

1. 在工作區中，您可以滑鼠右鍵按一下實驗，以將它設為**Active**實驗。 **Active**實驗是您目前的實驗。 您在 Visual Studio Code 中開啟的資料夾將會連結到雲端中的這項實驗中。 此資料夾應該包含您的本機 Python 指令碼。

現在您金鑰的度量資訊會儲存實驗歷程記錄中。 同樣地，您定型模型會自動上傳至 Azure Machine Learning 及儲存與實驗計量和記錄檔。 

[![附加 Visual Studio Code 中的資料夾](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>建立和管理計算目標

使用適用於 Visual Studio Code 的 Azure Machine Learning，您可以準備您的資料、 定型模型，並將它們部署在本機和遠端計算目標上。

延伸模組適用於 Azure Machine Learning 支援數個遠端計算目標。 如需詳細資訊，請參閱完整的清單支援[適用於 Azure Machine Learning 計算目標](how-to-set-up-training-targets.md)。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>在 Visual Studio Code 的 Azure Machine learning 中建立計算目標

若要建立計算目標：

1. 在 Visual Studio Code 活動列中，選取 [Azure] 圖示。 Azure Machine Learning 提要欄位隨即出現。

2. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 在下列的範例圖中，訂用帳戶名稱是**免費試用**，工作區，而且**TeamWorkspace**。 

3. 在工作區節點下方，以滑鼠右鍵按一下 [計算] 節點，然後選擇 [建立計算]。

4. 從清單中選擇計算目標類型。 

5. 在命令選擇區中，選取虛擬機器大小。

6. 在命令選擇區中，在欄位中，輸入用於計算目標的名稱。 

7. 在 JSON 組態檔中，開啟新索引標籤上，指定任何進階的屬性。 您可以指定屬性，例如節點計數上限。

8. 當您完成設定您的計算目標，在視窗右下角中時，選取**送出**。

如何建立 Azure Machine Learning 計算 (AMLCompute) 的範例如下：

[![在 Visual Studio Code 建立 AML 計算](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>回合的組態檔

Visual Studio Code 延伸模組會自動建立本機的計算目標和您的本機和 docker 環境的回合的組態，在您的本機電腦上。 您可以找到相關聯的計算目標節點下的回合的組態檔。 

## <a name="train-and-tune-models"></a>定型及調整模型

使用適用於 Visual Studio Code （預覽） 的 Azure Machine Learning，來快速逐一查看您的程式碼、 逐步執行和偵錯及原始程式碼控制的使用您的解決方案。 

若要使用 Azure Machine Learning 在本機執行您的實驗：

1. 在 Visual Studio Code 活動列中，選取 [Azure] 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 

1. 在 [工作區] 節點中，依序展開**計算**節點，然後以滑鼠右鍵按一下**執行組態**您想要使用的計算。 

1. 選取 [執行實驗]。

1. 從 [檔案總管] 中，選取您想要執行的指令碼。 

1. 選取 **檢視的實驗執行**查看整合的 Azure Machine Learning 入口網站中，以監視您的執行，並查看您定型的模型。

如何在本機執行實驗的範例如下：

[![在本機執行實驗](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>為 Visual Studio Code 中的實驗中使用遠端計算

若要使用遠端計算目標進行訓練，您需要建立執行的設定檔。 此檔案不只會告訴 Azure Machine Learning 要在哪裡執行您的實驗，而且還會告知如何準備環境。

#### <a name="the-conda-dependencies-file"></a>Conda 相依性檔案

根據預設，新的 conda 環境會建立，並管理您的安裝相依性。 不過，您必須指定您的相依性和其版本*aml_config/conda_dependencies.yml*檔案。 

預設值的下列程式碼片段*aml_config/conda_dependencies.yml*指定`tensorflow=1.12.0`。 如果您未指定的相依性版本，就會使用最新版本。 您可以在組態檔中新增其他相依性。

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

若要執行的遠端使用 Azure Machine Learning 實驗的計算目標：

1. 在 Visual Studio Code 活動列中，選取 [Azure] 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。 

1. 在 編輯器 視窗中，您的 Python 指令碼，以滑鼠右鍵按一下，然後選取**AML:** 在 Azure 中當作實驗執行。 

1. 在命令選擇區中，選取的計算目標。 

1. 在命令選擇區中，在欄位中，輸入執行的組態名稱。 

1. 編輯*conda_dependencies.yml*檔案來指定實驗的執行階段相依性。 然後在視窗的右下角，選取**送出**。 

1. 選取 **檢視的實驗執行**查看整合的 Azure Machine Learning 入口網站中，以監視您的執行，並查看您定型的模型。

如何在遠端計算目標上執行實驗的範例如下：

[![遠端目標上執行實驗](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>部署和管理模型
在 Azure Machine Learning，您可以部署及管理您的機器學習模型，在雲端和邊緣。 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>向 Azure Machine Learning 中的模型，從 Visual Studio Code

既然您已在定型模型，您可以在您的工作區來進行註冊。 您可以追蹤，並將已註冊的模型部署。

若要註冊您的模型：

1. 在 Visual Studio Code 活動列中，選取 [Azure] 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning services 工作區。

1. 在工作區節點下方，以滑鼠右鍵按一下 [模型]，然後選擇 [註冊模型]。

1. 在命令選擇區中，在欄位中，輸入模型名稱。 

1. 從清單中，選擇是否要上傳**模型檔**（適用於單一模型） 或**模型資料夾**（適用於具有多個檔案，例如 TensorFlow 模型）。 

1. 選取您的資料夾或檔案。

1. 當您完成設定您的模型屬性 視窗右下角時，選取**送出**。 

如何註冊您的 Azure Machine Learning 模型的範例如下：

[![向 AML 註冊模型](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>部署您的服務，從 Visual Studio Code

在 Visual Studio Code 中，您可以部署您的 web 服務：
+ Azure Container Instances (ACI) 進行測試。
+ Azure Kubernetes Service (AKS) 用於生產環境。

您不需要建立 ACI 容器事先測試，因為即時建立 ACI 容器。 不過，您需要事先設定 AKS 叢集。 如需詳細資訊，請參閱 <<c0> [ 部署與 Azure Machine Learning 服務的模型](how-to-deploy-and-where.md)。

若要部署 web 服務：

1. 在 Visual Studio Code 活動列中，選取 [Azure] 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和您的 Azure Machine Learning services 工作區。

1. 在工作區節點下方，展開 [模型] 節點。

1. 以滑鼠右鍵按一下您想要部署，然後選擇的模型**部署的服務，從已註冊的模型**從內容功能表。

1. 在命令選擇區中，選擇您想要部署到計算目標。 

1. 在命令選擇區中，在欄位中，輸入此服務的名稱。  

1. 在命令選擇區中，選取您的鍵盤瀏覽並選取 指令碼檔案上的 Enter 鍵。

1. 在命令選擇區中，選取 瀏覽及選取的 conda 相依性檔案在鍵盤上的 Enter 鍵。

1. 當您完成設定您的服務屬性 視窗右下角時，選取**送出**部署。 在服務屬性檔案中，您可以指定本機 docker 檔案或 schema.json 檔案。

現在已部署 Web 服務。

如何部署 web 服務的範例如下：

[![部署 web 服務](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>使用鍵盤快速鍵

您可以使用鍵盤存取 Visual Studio Code 中的 Azure Machine Learning 功能。 若要了解最重要的鍵盤快速鍵是 Ctrl + Shift + P，它會顯示命令選擇區。 從命令選擇區中，您可以存取所有 Visual Studio 程式碼，包括最常見的作業的鍵盤快速鍵的功能。

[![適用於 Visual Studio Code 的 Azure Machine Learning 的鍵盤快速鍵](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>後續步驟

* 如需如何使用 Visual Studio Code 以外的 Azure Machine Learning 訓練的逐步解說，請參閱[教學課程：使用 Azure Machine Learning 將模型定型](tutorial-train-models-with-aml.md)。
* 如何編輯、 執行和偵錯在本機的程式碼的逐步解說，請參閱[Python hello world 教學課程](https://code.visualstudio.com/docs/python/python-tutorial)。
