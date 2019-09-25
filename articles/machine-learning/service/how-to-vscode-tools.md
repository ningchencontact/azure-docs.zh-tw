---
title: 使用適用于機器學習服務的 Visual Studio Code
titleSuffix: Azure Machine Learning
description: 瞭解如何安裝 Visual Studio Code 的 Azure Machine Learning，並在 Azure Machine Learning 中建立實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: a93c71a97cdb1f6296919a248cf7ef545f7b307f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269246"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>開始使用適用於 Visual Studio Code 的 Azure Machine Learning

在本文中，您將瞭解如何使用 Visual Studio Code 延伸模組的**Azure Machine Learning**來訓練和部署機器學習模型。

[Azure Machine Learning 服務](overview-what-is-azure-ml.md)簡化了機器學習模型的建立、定型和部署。
+ 針對訓練，它提供在本機或遠端執行實驗的支援。 針對每個實驗，您可以記錄多個執行的自訂計量，以微調超參數
+ 您也可以使用 Azure Machine Learning 服務，輕鬆地為您的測試和生產需求部署機器學習模型。

## <a name="prerequisites"></a>必要條件

+ 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

+ 安裝[Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)，這是在 Windows、Mac 和 Linux 上執行的輕量程式碼編輯器。

+ [安裝 Python 3.5 或更新版本](https://www.anaconda.com/download/)。


## <a name="install-the-extension"></a>安裝延伸模組

當您安裝 Azure Machine Learning 擴充功能時，會自動安裝兩個以上的延伸模組。 它們是[Azure 帳戶延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)模組和[Microsoft Python 擴充](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python)功能。 如需有關使用 Python 擴充功能來編輯、執行及對 Python 程式碼進行偵錯工具的詳細資訊，請參閱[python hello world 教學](https://code.visualstudio.com/docs/Python/Python-tutorial)課程。

若要安裝 Azure Machine Learning 延伸模組:

1. 開啟 Visual Studio Code。

1. 切換至 [擴充功能] 索引標籤，然後搜尋 "Azure Machine Learning"。

1. 在 [擴充功能] 索引標籤上, 選取 [**安裝**]。

1. 延伸模組的 [歡迎使用] 索引標籤會在 Visual Studio Code 中開啟，而 Azure 符號（在下列螢幕擷取畫面中以紅色反白顯示）會新增至活動列。

   ![Visual Studio Code 活動列上的 Azure 圖示](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在對話方塊中, 選取 [登**入**], 並遵循提示向 Azure 進行驗證。

   與 Visual Studio Code 延伸模組的 Azure Machine Learning 一起安裝的 Azure 帳戶擴充功能, 可協助您使用 Azure 帳戶進行驗證。 如需命令清單, 請參閱[Azure 帳戶延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)模組的頁面。

> [!TIP]
> 您也可以直接從適用于 Visual Studio Code 延伸模組的 Azure Machine Learning 下載延伸模組安裝程式[（預覽）](https://aka.ms/vscodetoolsforai)。

## <a name="quickstart-with-azure-machine-learning"></a>Azure Machine Learning 的快速入門
有多種方法可以使用 Azure Machine Learning 服務來執行定型腳本。 如果您剛開始使用，讓我們先逐步解說如何快速提交要在 Azure 中執行的訓練腳本。

如果您已經很熟悉 Azure Machine Learning 的概念，而且想要瞭解如何管理和使用延伸模組的詳細資訊，請參閱以下[VS Code 深入瞭解 Azure Machine Learning](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) 。

## <a name="run-an-existing-python-training-script-in-azure"></a>在 Azure 中執行現有的 Python 訓練腳本
如果您有現有的訓練腳本，VS Code 的 Azure Machine Learning 延伸模組不僅提供絕佳的編輯、偵錯工具和來源管理體驗，還可讓您輕鬆地在 Azure 中執行和儲存腳本的計量。

那就開始吧。 您可以使用自己的訓練腳本（如果您已準備好），或複製範例[vscode-ai](https://github.com/microsoft/vscode-tools-for-ai)存放庫。 這是用來提出此延伸模組問題的公用存放庫。 它也包含一個小型的**mnist**範例資料夾，我們將在此範例中使用。

1. 在 VS Code 中開啟 [ **mnist** ] 資料夾。

1. 使用您最愛的虛擬環境封裝或 Anaconda 來建立新的 Python 環境，並安裝 TensorFlow 和 numpy 套件。

1. 在 [VS Code] 狀態列的左下角，選取您建立的新環境做為 Python 解譯器。

1. 開啟**train.py** ，然後按下 [執行] 按鈕（或直接按 F5）來執行它。

   [![執行 MNIST 訓練](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

如果所有專案都已正確安裝，腳本將會執行，並在 [輸出] 資料夾中建立 TensorFlow 模型。

[![顯示 TensorFlow 模型](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

既然您已知道腳本正確執行，讓我們在 Azure 中執行它！

這可以輕鬆地完成，不需要額外修改**train.py**。 不過，只要進行幾項簡單的變更，您就可以利用 Azure Machine Learning，自動追蹤您對每個定型執行所選擇的重要計量。

### <a name="make-azure-aware-of-your-run-metrics"></a>讓 Azure 知道您的執行計量
若要修改您的專案，以便讓 Azure 知道您執行中的重要資訊：

1. 在與**train.py**相同的資料夾中建立名為**amlrun.py**的檔案

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. 在**train.py**中匯入 amlrun 檔案

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. 初始化**train.py**中的執行物件

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. 使用執行 .log （）函數將計量記錄到 Azure：

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>在 Azure 中執行腳本
就這麼容易！ 現在只要使用延伸模組在雲端中執行您的腳本！ 請注意，下列逐步解說影片可讓您自由地壓縮建立新的 Azure ML 工作區和計算所需的時間，以及執行訓練腳本所花費的時間。

   [![啟動 Azure ML 實驗](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

按一下 [執行實驗] 按鈕之後，請回答提示，如下所示：

1. 選擇 Azure 訂用帳戶
1. 選擇建立*新*的 Azure ML 工作區
1. 從一組預先設定的範本中挑選，以初始化執行的 Python 環境。 範本會提供一個起點，並包含下列設定：
    1. **PyTorch**、 **TensorFlow**或**scikit-learn-學習**
    1. **單一**或**分散式**計算訓練
    1. 自訂環境的**一般**
1. 藉由新增範本中未包含的任何套件，確保腳本的 pip 和 conda 套件清單已完成。
1. 檢查實驗執行的預設名稱和規格，然後按一下 json 檔案中的 [**提交實驗**] 連結。 Json 檔案不會儲存，因為它只是為了讓您在提交前檢查或變更實驗設定。
1. 在延伸模組為您設定所有專案並執行您的腳本之前，請先坐下來並放寬！

    [![在雲端中定型](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

幾秒鐘後，您會收到通知，告知您已將實驗提交至 Azure，此時您可以在 Azure 入口網站中，按一下 VS Code 通知中的 [ **View 實驗執行**] 連結，或按下 [重新整理]，在 VS Code 內查看其進度[Azure] 索引標籤中的按鈕。

目前，只有在 Azure 入口網站中才支援「查看執行計量」。 前述的「**觀看實驗執行**」連結會帶您前往執行，您會在其中看到您所記錄的計量。
[![在入口網站中執行實驗](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>深入瞭解 VS Code 的 Azure Machine Learning

在上述逐步解說中，我們會遵循最簡單的路徑來提交實驗。 您可能已經注意到，延伸模組會將您需要管理的步驟降到最低，以執行實驗。 在本節中，我們將討論如何個別管理所有的 Azure Machine Learning 概念，並提供最大的控制。

在 Visual Studio Code 中開始訓練和部署機器學習模型之前，您需要在雲端中建立[Azure Machine Learning 工作區](concept-workspace.md)。 此工作區將包含您的模型和資源。

### <a name="create-a-workspace"></a>建立工作區

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

    [![建立工作區](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. 以滑鼠右鍵按一下您的 Azure 訂用帳戶，然後選取 [建立工作區]。 根據預設，會產生包含建立日期和時間的名稱。 將名稱變更為**TeamWorkspace** ，然後按 enter。

1. 如果您知道要選擇或建立一個新的資源群組，請從清單中選取它。 如果建立新的，請選擇最接近您打算部署模型之位置的位置。 在此情況下，我們選擇 [**美國西部 2**]。

1. 按下 enter 之後，Azure Machine Learning 將會收到建立工作區的要求。 您會在 Visual Studio Code 通知區域中通知您。

1. 展開 [訂用帳戶] 節點，以尋找新建立的工作區。

### <a name="create-an-experiment"></a>建立實驗
您可以在工作區中建立一或多個實驗，以追蹤和分析個別的模型定型執行。 執行可在 Azure 雲端或您的本機電腦上完成。

1. 展開 [ **TeamWorkspace** ] 工作區。 以滑鼠右鍵按一下 [**實驗**] 節點，然後從內容功能表中選擇 [**建立實驗**]。

1. 在提示中，輸入您的實驗名稱。 在範例螢幕擷取畫面中, 實驗的名稱是**MNIST**。

1. 選取 Enter 以建立新的實驗。 新實驗會在樹狀結構中顯示為 [**實驗**] 節點的子系。

1. 在工作區中, 您可以用滑鼠右鍵按一下實驗, 將其設定為作用中的實驗。 在雲端中**實驗的作用中實驗連結**，指向您目前已在 Visual Studio Code 中開啟的資料夾。 此資料夾應該包含您的本機 Python 指令碼。 藉由設定作用中的實驗，所有定型回合的重要計量都會儲存在實驗中，而不論其執行位置為何。

    [![建立實驗](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>建立和管理計算目標

使用 Visual Studio Code 的 Azure Machine Learning, 您可以準備資料、將模型定型, 並將它們部署在本機和遠端計算目標上。

延伸模組支援 Azure Machine Learning 的數個遠端計算目標。 如需詳細資訊, 請參閱 Azure Machine Learning 支援的[計算目標](how-to-set-up-training-targets.md)完整清單。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>在 Visual Studio Code 中建立 Azure Machine Learning 的計算目標

若要建立計算目標:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning 工作區。

1. 在工作區節點下方，以滑鼠右鍵按一下 [計算] 節點，然後選擇 [建立計算]。

1. 從清單中選擇計算目標類型。

1. 在 [命令選擇區] 提示中，選取虛擬機器大小。 您可以使用文字（例如「gpu」）篩選計算。

1. 在 [命令選擇區] 提示字元中，輸入計算目標的名稱。

1. 輸入名稱之後，將會使用預設參數建立計算。 若要變更參數，請以滑鼠右鍵按一下新的計算，然後選擇 [**編輯計算**]。

1. 在顯示的 json 中，進行所需的變更，然後按一下 [儲存並繼續] CodeLens （使用鍵盤，您可以按下**ctrl-shift-p**來叫用命令選擇區 **，並執行 Azure ML：儲存並繼續**命令）

以下是如何建立和編輯 Azure Machine Learning 計算（AMLCompute）的範例：

[![在 Visual Studio Code 中建立 AML 計算](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>執行設定檔

若要在計算上執行 Azure Machine Learning 實驗，必須適當地設定該計算。 執行設定檔是指定此環境所使用的機制。

以下是如何為上述建立的 AmlCompute 建立執行設定的範例。

[![建立計算的執行設定](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

若要在本機電腦上執行 Azure ML 實驗，仍然需要執行設定檔。 建立本機執行設定時，使用的 Python 環境會預設為您在 VS Code 內設定的解譯器路徑。

### <a name="train-and-tune-models"></a>定型及調整模型

使用適用于 VS Code 的 Azure ML 擴充功能有多種方式可在實驗中執行定型腳本。

1. 以滑鼠右鍵按一下定型腳本，然後**選擇 Azure ML：在 Azure 中執行身分實驗**
1. 按一下 [執行實驗] 工具列圖示。
1. 以滑鼠右鍵按一下 [回合設定] 節點。
1. 使用 VS Code 命令選擇區來執行**Azure ML：執行實驗**

若要執行 Azure Machine Learning 實驗：

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning 工作區。

1. 在 [工作區] 節點底下，展開 [**實驗**] 節點，然後以滑鼠右鍵按一下您想要執行的實驗。

1. 選取 [執行實驗]。

1. 選擇您想要執行以定型模型的 Python 檔案名，然後按 enter 以提交執行。 注意：所選擇的檔案必須位於您目前已在 VS Code 中開啟的資料夾。

1. 提交回合之後，[執行]**節點**會出現在您選擇的實驗底下。 使用此節點來監視執行的狀態。 注意：可能需要定期重新整理視窗，才能看到最新狀態。

以下範例示範如何在先前建立的計算上執行實驗：

[![在本機執行實驗](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>部署和管理模型
在 Azure Machine Learning 中, 您可以在雲端和邊緣部署和管理您的機器學習模型。

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>註冊您的模型以從 Visual Studio Code Azure Machine Learning

既然您已訓練模型, 您可以在工作區中註冊它。 您可以追蹤和部署已註冊的模型。

若要註冊您的模型:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning 工作區。

1. 在工作區節點下方，以滑鼠右鍵按一下 [模型]，然後選擇 [註冊模型]。

1. 在 [命令選擇區] 的欄位中, 輸入模型名稱。

1. 從清單中, 選擇是否要上傳**模型**檔案 (適用于單一模型) 或**模型資料夾**(適用于具有多個檔案的模型, 例如 TensorFlow)。

1. 選取您的資料夾或檔案。

1. 當您完成模型屬性的設定時, 請在視窗的右下角選取 [**提交**]。

以下是如何註冊模型以 Azure Machine Learning 的範例:

[![向 AML 註冊模型](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>從 Visual Studio Code 部署您的服務

在 Visual Studio Code 中, 您可以將 web 服務部署至:
+ 用於測試的 Azure 容器實例 (ACI)。
+ 適用于生產環境的 Azure Kubernetes Service (AKS)。

您不需要事先建立 ACI 容器來進行測試，因為 ACI 容器會視需要建立。 不過, 您必須事先設定 AKS 叢集。 如需詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

若要部署 web 服務:

1. 在 Visual Studio Code 活動列上, 選取 Azure 圖示。 Azure Machine Learning 提要欄位隨即出現。

1. 在樹狀檢視中，展開您的 Azure 訂用帳戶和 Azure Machine Learning 工作區。

1. 在工作區節點下方，展開 [模型] 節點。

1. 以滑鼠右鍵按一下您要部署的模型, 然後從內容功能表選擇 [**從已註冊的模型部署服務**]。

1. 在 [命令選擇區] 中, 選擇您想要部署到的計算目標。

1. 在 [命令選擇區] 的欄位中, 輸入此服務的名稱。

1. 在 [命令選擇區] 中, 選取鍵盤上的 Enter 鍵以流覽並選取腳本檔案。

1. 在 [命令選擇區] 中, 選取鍵盤上的 Enter 鍵以流覽並選取 conda 相依性檔案。

1. 當您完成設定服務屬性時, 請在視窗的右下角選取 [**提交**] 以進行部署。 在服務屬性檔案中, 您可以指定本機 docker 檔案或架構. json 檔案。

現在已部署 Web 服務。

以下是如何部署 web 服務的範例:

[![部署 web 服務](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>試驗其他功能

您可以使用命令選擇區來存取 Visual Studio Code 中的許多 Azure Machine Learning 功能。 若要叫用命令選擇區，請按 Ctrl + Shift + P。 從這裡，您可以搜尋延伸模組的其他 Azure ML 功能。

[![Visual Studio Code Azure Machine Learning 的鍵盤快速鍵](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>後續步驟

* 如需如何使用 Visual Studio Code 外的 Azure Machine Learning 進行定型的逐步解說, [請參閱教學課程:使用 Azure Machine Learning 將模型定型](tutorial-train-models-with-aml.md)。
* 如需如何在本機編輯、執行及偵錯工具代碼的逐步解說, 請參閱[Python hello world 教學](https://code.visualstudio.com/docs/Python/Python-tutorial)課程。
