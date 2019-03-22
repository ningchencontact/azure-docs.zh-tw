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
ms.openlocfilehash: 5e7e405bdb251bbebc5da81253eea2414b434cb8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838303"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>開始使用適用於 Visual Studio Code 的 Azure Machine Learning

在本文中，您將了解如何安裝**適用於 Visual Studio Code 的 Azure Machine Learning** 擴充功能，並在 Visual Studio Code (VS Code) 中使用 Azure Machine Learning 服務建立第一個實驗。

利用 Visual Studio Code 中的 Azure Machine Learning 擴充功能，使用 Azure Machine Learning 服務來準備您的資料、定型，以及在本機和遠端計算目標上測試機器學習模型、部署那些模型，並追蹤自訂計量和實驗。

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

1. 隨即會在 VS Code 中開啟該擴充功能的歡迎使用索引標籤，並將 Azure 符號新增至活動列。

   ![Visual Studio Code 活動列中的 Azure 圖示](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在對話方塊中，按一下 [登入]，然後依照螢幕上的提示來向 Azure 進行驗證。 
   
   已與適用於 VS Code 的 Azure Machine Learning 擴充功能一起安裝的 Azure 帳戶擴充功能，可協助使用您的 Azure 帳戶進行驗證。 請參閱 [Azure 帳戶擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) \(英文\) 頁面中的命令清單。

> [!Tip] 
> 請查看[適用於 VS Code 的 IntelliCode 擴充功能 (預覽)](https://go.microsoft.com/fwlink/?linkid=2006060) \(英文\)。 IntelliCode 會在 Python 中提供一組適用於 IntelliSense 的 AI 輔助功能，例如，根據目前的程式碼內容來推斷最相關的自動完成。

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK 安裝

1. 確定已安裝 Python 3.5 或更新版本，並透過 VS Code 來辨識。 如果您立即安裝它，接著重新啟動 VS Code，然後使用 https://code.visualstudio.com/docs/python/python-tutorial 上的指示來選取 Python 解譯器。

1. 在整合式終端機視窗中，指定要使用的 Python 解譯器，或者您可以按 **Enter** 來使用預設的 Python 解譯器。

   ![選擇解譯器](./media/vscode-tools-for-ai/python.png)

1. 視窗的右下角會出現通知，指出正在自動安裝 Azure ML SDK。    隨即會建立本機私人 Python 環境，使其具備適用於 Azure Machine Learning 的 Visual Studio Code 先決條件。

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

### <a name="use-keyboard-shortcuts"></a>使用鍵盤快速鍵

如同大部分的 VS Code，VS Code 中的 Azure Machine Learning 功能均可從鍵盤存取。 需知道的最重要按鍵組合是 Ctrl+Shift+P，其會顯示命令選擇區。 從這裡開始，您可以存取 VS Code 的所有功能，包括最常見作業的鍵盤快速鍵。

[![適用於 VS Code 的 Azure Machine Learning 的鍵盤快速鍵](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>後續步驟

您現在可以使用 Visual Studio Code 來與 Azure Machine Learning 搭配運作。

了解如何[在 Visual Studio Code 中建立計算目標、定型和部署模型](how-to-vscode-train-deploy.md)。
