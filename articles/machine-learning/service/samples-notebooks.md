---
title: 範例 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: 尋找並使用 Jupyter Notebook 範例在 Python 中探索 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6befe3a3fee80dd65fd3ac5be241c558707224e6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811089"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>使用 Jupyter Notebook 探索 Azure Machine Learning 服務


為了方便起見，我們已開發一系列可用來探索 Azure Machine Learning 服務的 Jupyter Python Notebook。 

了解如何搭配此網站上的文件使用服務，並使用這些 Notebook 來根據您的情況進行自訂。 

## <a name="prerequisite"></a>必要條件

完成 [Azure Machine Learning Python 快速入門](quickstart-get-started.md)以建立工作區並啟動 Azure Notebooks。

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>試用 Azure Notebooks：雲端中的免費 Jupyter Notebook

您可以輕鬆地開始使用 Azure Notebooks！ 我們已為您在 [Azure Notebooks](https://notebooks.azure.com/) 上安裝並設定[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)。 Azure 服務會自動管理安裝和未來的更新。
  
+ 若要執行**核心教學課程 Notebook**：
  1. 移至 [Azure Notebooks](https://notebooks.azure.com/)。
    
  1. 在進行必要快速入門時建立的**使用者入門**程式庫中，尋找 **tutorials** 資料夾。
    
  1. 開啟您想要執行的 Notebook。
    
+ 若要執行**其他 Notebook**：

  1. [將 Notebook 範例匯入](https://aka.ms/aml-clone-azure-notebooks)到 Azure Notebooks。

  1. 使用以下其中一種方法將工作區組態檔新增至程式庫：
     + 將**使用者入門**程式庫中的 **config.json** 檔案複製到所複製的新程式庫。

     + 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的程式碼建立新的工作區。
    
  1. 開啟您想要執行的 Notebook。     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>使用資料科學虛擬機器 (DSVM)

我們已為您在 DSVM 上安裝並設定[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 和 Notebook 伺服器。 使用下列步驟執行 Notebook。

1. [建立 DSVM](how-to-configure-environment.md#dsvm)。

1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

1. 使用以下其中一種方法將工作區組態檔新增至程式庫：
    * 將您使用必要快速入門建立的 **aml_config\config.json** 檔案複製到已複製的目錄。

    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的程式碼建立新的工作區。

1. 從複製的目錄中啟動 Notebook 伺服器。

## <a name="use-your-own-jupyter-notebook-server"></a>使用您自己的 Jupyter Notebook 伺服器

使用下列步驟在您的電腦上建立本機 Jupyter Notebook 伺服器。

1. 請確定您已完成用於安裝 Azure Machine Learning SDK 的必要快速入門。

1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

1. 使用以下其中一種方法將工作區組態檔新增至程式庫：
    * 將您使用必要快速入門建立的 **aml_config\config.json** 檔案複製到已複製的目錄。
    
    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的程式碼建立新的工作區。

1. 從複製的目錄中啟動 Notebook 伺服器。

1. 請移至包含 Notebook 的資料夾。

1. 開啟 Notebook。

<a name="auto"></a>

## <a name="automated-ml-setup"></a>自動化 ML 安裝 

**這些步驟只適用於 `automated-machine-learning` 資料夾中的 Notebook。**

您可以使用上述任何一個選項，也可以使用下列指示，同時安裝環境和建立工作區。 

1. 安裝 [Mini-conda](https://conda.io/miniconda.html)。 選擇 3.7 或更高版本。 遵循提示來進行安裝。 
   >[!NOTE]
   >您可以使用現有的 Conda，前提是版本必須是 4.4.10 或更高版本。 使用 `conda -V` 來顯示版本。 您可以使用此命令來更新 Conda 版本：`conda update conda`。 無須特別安裝 mini-conda。

1. 從 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) 下載範例 Notebook (zip 檔)，並將內容解壓縮至本機目錄。 自動化機器學習 Notebook 位於 `how-to-use-azureml/automated-machine-learning` 資料夾。

1. 設定新的 Conda 環境。 
   1. 在本機電腦上開啟 Conda 提示字元。
   
   1. 瀏覽至您解壓縮到本機電腦的檔案。
   
   1. 開啟 `automated-machine-learning` 資料夾。
   
   1. 在 Windows 的 Conda 提示字元中執行 `automl_setup.cmd`，或是執行您作業系統的 `.sh`。 執行時間大約 10 分鐘。

      安裝指令碼：
      + 建立新的 Conda 環境
      + 安裝必要套件
      + 設定小工具
      + 啟動 Jupyter Notebook
      
   >[!NOTE]
   > 指令碼會將 Conda 環境名稱當作選擇性參數。 預設的 Conda 環境名稱是 `azure_automl`。 確切的命令視作業系統而定。 如果您要建立新環境或升級至新版本，這會相當有用。 例如，您可以使用 'automl_setup.cmd azure_automl_sandbox' 來建立環境名稱 azure_automl_sandbox。 
      
1. 完成指令碼之後，您會在瀏覽器中看到 Jupyter Notebook 首頁。

1. 瀏覽至儲存 Notebook 的路徑。 

1. 開啟 automated-machine-learning 資料夾，然後開啟 `configuration.ipynb` Notebook。 

1. 執行 Notebook 中的資料格以註冊 Machine Learning 服務資源提供者，並建立工作區。

您現在已可開啟並執行本機電腦上儲存的 Notebook。


## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Azure Machine Learning 服務的 GitHub Notebook 存放庫](https://aka.ms/aml-notebooks)

試試以下教學課程︰
+ [使用 MNIST 定型和部署影像分類模型](tutorial-train-models-with-aml.md)

+ [準備資料並使用自動化機器學習服務以 NYC 計程車資料集定型迴歸模型](tutorial-data-prep.md)
