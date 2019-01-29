---
title: 透過 Azure 入口網站快速入門
titleSuffix: Azure Machine Learning service
description: 開始使用 Azure Machine Learning 服務。 使用 Azure 入口網站建立工作區，此工作區是雲端中用來實驗、訓練及部署機器學習模型的基礎區塊。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: e3569f560224ab8c9a64ababb2fcea7e96e87367
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812449"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>快速入門：利用 Azure 入口網站開始使用 Azure Machine Learning

使用 Azure 入口網站來建立 Azure Machine Learning 工作區。 此工作區是雲端中使用 Machine Learning 來實驗、訓練及部署機器學習模型的基礎區塊。 本快速入門使用雲端資源，因此不需要安裝。 若要改為設定您自己的 Jupyter Notebook 伺服器，請參閱[快速入門：使用 Python 來開始使用 Azure Machine Learning](quickstart-create-workspace-with-python.md)。  
 
在本快速入門中，您會執行下列動作：

* 在您的 Azure 訂用帳戶中建立工作區。
* 試著在 Jupyter Notebook 中使用 Python 加以執行，並記錄多次反覆運算的值。
* 在工作區中檢視記錄的值。

下列 Azure 資源可在區域內取得時，就會自動新增至您的工作區：

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure 儲存體](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)

您所建立的資源可用來作為其他 Machine Learning 服務教學課程和操作說明文章的必要條件。 如同使用其他 Azure 服務，對於與 Machine Learning 相關聯的特定資源有一些限制。 例如，計算叢集大小。 深入了解[預設限制及如何增加配額](how-to-manage-quotas.md)。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。


## <a name="create-a-workspace"></a>建立工作區 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]


## <a name="use-the-workspace"></a>使用工作區

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



現在，請了解工作區如何協助您管理機器學習服務指令碼。 在本節中，您會執行下列步驟：

* 在 Azure Notebooks 中開啟 Notebook。
* 執行程式碼來建立一些記錄的值。
* 在工作區中檢視記錄的值。

這個範例示範工作區如何協助您追蹤指令碼中所產生的資訊。 

### <a name="open-a-notebook"></a>開啟 Notebook 

[Azure Notebooks](https://notebooks.azure.com) 會為 Jupyter Notebook 提供免費的雲端平台，其中預先設定了您執行 Machine Learning 所需的一切。 在您的工作區中，您可以啟動此平台以開始使用 Azure Machine Learning 服務工作區。

1. 在工作區頁面上，選取 [探索您的 Azure Machine Learning 服務工作區]。

 ![探索工作區](./media/quickstart-get-started/explore_aml.png)

1. 選取 [開啟 Azure Notebooks]，在 Azure Notebooks 中嘗試您的第一個實驗。  Azure Notebooks 是一項個別的服務，可讓您在雲端中免費執行 Jupyter Notebook。  當您使用服務的這個連結時，有關於如何連線到工作區的資訊將會新增至您在 Azure Notebooks 中建立的程式庫。

 ![開啟 Azure Notebooks](./media/quickstart-get-started/explore_ws.png)

1. 登入 Azure Notebooks。  請確實使用您用來登入 Azure 入口網站的相同帳戶進行登入。 您的組織可能需要[系統管理員的同意](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent)，您才能登入。

1. 登入之後，即會開啟新索引標籤並出現 `Clone Library` 提示。 複製此程式庫時，會將一組 Notebook 和其他檔案載入您的 Azure Notebooks 帳戶中。  這些檔案有助於您探索 Azure Machine Learning 的功能。

1. 取消核取 [公用]，使您不會與他人共用您的工作區資訊。

1. 選取 [複製]。

 ![複製程式庫](./media/quickstart-get-started/clone.png)

1. 如果您看到專案狀態為「已停止」，請按一下 [以免費計算執行]，以使用免費的 Notebook 伺服器。

    ![以免費計算執行專案](./media/quickstart-get-started/run-project.png)

### <a name="run-the-notebook"></a>執行 Notebook

在此專案的檔案清單中，您會看到 `config.json` 檔案。 此組態檔包含與您在 Azure 入口網站中建立的工作區有關的資訊。  此檔案可讓您的程式碼連線至工作區以及在其中新增資訊。

1. 選取 **01.run-experiment.ipynb** 以開啟 Notebook。

1. 狀態區域會指示您等待核心啟動。  核心準備就緒後，此訊息就會消失。

    ![等待核心啟動](./media/quickstart-get-started/wait-for-kernel.png)

1. 核心啟動後，請使用 **Shift+Enter** 逐一執行資料格。 或者，選取 [資料格] > [全部執行] 以執行整個 Notebook。 如果您在資料格旁邊看到星號 __*__，表示資料格正在執行中。 該資料格的程式碼完成後，就會出現一個數字。 

1. 依照 Notebook 中的指示驗證您的 Azure 訂用帳戶。

在 Notebook 中的所有資料格都執行完成後，您可以在工作區中檢視記錄的值。

## <a name="view-logged-values"></a>檢視記錄的值

1. `run` 資料格的輸出包含連回 Azure 入口網站的連結，可讓您在工作區中檢視實驗結果。 

    ![檢視實驗](./media/quickstart-get-started/view_exp.png)

1. 按一下 [連結至 Azure 入口網站]，在您的工作區中檢視執行的相關資訊。  此連結會在 Azure 入口網站中開啟您的工作區。

1. 您看到的記錄值繪圖已自動建立於工作區中。 每當您使用相同的名稱參數記錄多個值時，系統就會自動為您產生繪圖。

   ![檢視歷程記錄](./media/quickstart-get-started/web-results.png)

用於大致估計 pi 的程式碼會使用隨機值，因此您的圖會顯示不同的值。  

## <a name="clean-up-resources"></a>清除資源 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

您也可以保留資源群組，但刪除單一工作區。 顯示工作區屬性，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

您已建立了進行實驗和部署模型所需的資源。 您也在 Notebook 中執行了一些程式碼。 而且在雲端的工作區中探索了該程式碼的執行歷程記錄。

如需深入的工作流程體驗，請按照 Machine Learning 教學課程來定型和部署模型：  

> [!div class="nextstepaction"]
> [教學課程：將影像分類模型定型](tutorial-train-models-with-aml.md)
