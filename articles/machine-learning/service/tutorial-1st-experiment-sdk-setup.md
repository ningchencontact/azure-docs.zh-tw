---
title: 教學課程：建立您的第一個 ML 實驗：設定
titleSuffix: Azure Machine Learning
description: 在本教學課程系列中，您會遵循端對端步驟來開始使用在 Jupyter 筆記本中執行的 Azure Machine Learning Python SDK。  第一部分中已建立雲端筆記本伺服器環境及工作區來管理您的實驗和機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 3bbda22689bb330acc836173162a64b840f1bbd8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828041"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>教學課程：使用 Python SDK 開始建立您的第一個 ML 實驗

在本教學課程中，您會遵循端對端步驟來開始使用在 Jupyter 筆記本中執行的 Azure Machine Learning Python SDK。 本教學課程是**教學課程系列的第一部分 (共兩部分)** ，其中包含 Python 環境的安裝和設定，以及如何建立工作區來管理您的實驗和機器學習模型。 [**第二部分**](tutorial-1st-experiment-sdk-train.md)會以此為基礎來定型多個機器學習模型，並使用 Azure 入口網站和 SDK 來引進模型管理程序。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立要在下一個教學課程中使用的 [Azure Machine Learning 工作區](concept-workspace.md)。
> * 將教學課程 Notebook 複製到工作區中的資料夾。
> * 建立已安裝並預先設定 Azure Machine Learning Python SDK 的雲端式 Jupyter Notebook VM。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與服務中容易使用的物件結合。 

您透過 Azure 入口網站建立工作區 (管理 Azure 資源的 Web 型主控台)。 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> 記下您的**工作區**和**訂用帳戶**。 您會需要這些項目，以確保您在正確位置建立實驗。 


## <a name="azure"></a>複製 Notebook 資料夾

此範例會使用您工作區中的雲端 Notebook 伺服器，讓您擁有免安裝和預先設定的體驗。 如果您想要控制您的環境、套件和相依性，[請使用您自己的環境](how-to-configure-environment.md#local)。

您將在工作區登陸頁面 (預覽) 中完成下列實驗設定和執行步驟，該頁面是統一的介面，為所有技能等級的資料科學從業人員，提供執行資料科學情節的機器學習工具。

1. 登入[工作區登陸頁面](https://ml.azure.com/) \(英文\)。

1. 選取訂用帳戶與您建立的工作區。

1. 選取左側的 [Notebook 和檔案]  。

1. 選取 **samples** 資料夾。

1. 選取 **tutorials** 資料夾右側的 **"..."** ，然後選取 [複製]  。

    ![複製資料夾](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. 每個存取工作區的使用者，都會顯示一個資料夾。  選取您的資料夾，以將 **tutorial** 資料夾複製到該處。

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">選取要執行 Notebook 的 VM

1. 在 [使用者檔案]  下開啟您的資料夾，然後開啟複製的 **tutorials** 資料夾。

    ![開啟教學課程資料夾](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > 您可以查看 **samples** 資料夾中的 Notebook，但無法從該處執行 Notebook。  若要執行 Notebook，請務必在 [使用者檔案]  區段中開啟複製的 Notebook 版本。
    
1. 在 **tutorials** 資料夾中選取 **tutorial-1st-experiment-sdk-train.ipynb** 檔案。

1. 在頂端列中，選取要用來執行 Notebook 的 Notebook VM。 這些 VM 已預先設定執行 Azure Machine Learning 所需的一切。 您可以選取您工作區的任何使用者所建立的 VM。 

1. 如果找不到任何 VM，請選取 [+ 新增 VM]  來建立 VM。

    ![建立 VM](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. 在建立 VM 時，請提供名稱。  此名稱的長度必須介於 2 到 16 個字元之間。 有效的字元是字母、數字和 - 字元，且在您的 Azure 訂用帳戶中必須是唯一的。

    1. 然後選取 [建立]  。 設定 VM 大約需要 5 分鐘的時間。

1. VM 可供使用後，會顯示在頂端工具列中。  您現在可以使用工具列中的 [全部執行]  來執行 Notebook，或在 Notebook 的程式碼資料格中使用 **Shift+Enter** 來執行。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列工作：

* 建立 Azure Machine Learning 工作區。
* 建立及設定您工作區中的雲端 Notebook 伺服器。

在本教學課程的**第二部分**中，您會在 `tutorial-1st-experiment-sdk-train.ipynb` 中執行程式碼以定型機器學習模型。 

> [!div class="nextstepaction"]
> [教學課程：定型您的第一個模型](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> 如果您不打算遵循本教學課程的第 2 部分或任何其他教學課程，請在未使用時[停止雲端 Notebook 伺服器 VM](tutorial-1st-experiment-sdk-train.md#clean-up-resources) 來降低成本。


