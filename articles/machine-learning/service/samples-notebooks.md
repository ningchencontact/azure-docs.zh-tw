---
title: 範例 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: 尋找並使用 Jupyter Notebook 範例在 Python 中探索 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035374"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>使用 Jupyter Notebook 探索 Azure Machine Learning 服務

為了方便起見，我們已開發一系列可用來探索 Azure Machine Learning 服務的 Jupyter Python Notebook。 

了解如何搭配此網站上的文件使用服務，並使用這些 Notebook 來根據您的情況進行自訂。 

使用下列其中一個途徑來執行具有這些範例 Notebook 的 Notebook 伺服器。  在伺服器執行時，請在 **tutorials** 資料夾中找出教學課程 Notebook，或瀏覽 **how-to-use-azureml** 資料夾中的不同功能。

## <a name="a-managed-cloud-notebook-server"></a>受控雲端 Notebook 伺服器

您可以輕鬆地開始使用自己的雲端式 Notebook 伺服器。 我們已在您建立此雲端資源後，為您安裝及設定範例 Notebook 和[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)。  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* 這些範例可從 Notebook 網頁取得。

## <a name="a-data-science-virtual-machine-dsvm"></a>資料科學虛擬機器 (DSVM)

我們已為您在 DSVM 上安裝並設定[適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 和 Notebook 伺服器。 

[建立 DSVM](how-to-configure-environment.md#dsvm) 之後，請使用下列步驟在 DSVM 上執行 Notebook。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>您自己的 Jupyter Notebook 伺服器

使用下列步驟在您的電腦上建立本機 Jupyter Notebook 伺服器。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

安裝指示將安裝您執行快速入門和教學課程 Runbook 所需的套件。  其他範例 Notebook 可能需要安裝其他元件。  如需這些元件的詳細資訊，請參閱 [安裝 適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install)。

## <a name="azure-notebooks"></a>Azure Notebooks

我們已為您在 [Azure Notebooks](https://notebooks.azure.com/) 上安裝並設定範例 Notebook 和 [適用於 Python 的 Azure Machine Learning SDK](https://aka.ms/aml-sdk)。 Azure 服務會自動管理安裝和未來的更新。

透過 [Azure 入口網站](https://portal.azure.com)開始使用 Azure Notebooks。  開啟您的工作區，並從 [概觀] 區段中選取 [開始使用 Azure Notebooks]。

## <a name="next-steps"></a>後續步驟

+ 在下列 GitHub 存放庫中瀏覽 Azure Machine Learning 服務的範例 Notebook： https://aka.ms/aml-notebooks

試試以下教學課程︰
+ [使用 MNIST 定型和部署影像分類模型](tutorial-train-models-with-aml.md)

+ [準備資料並使用自動化機器學習服務以 NYC 計程車資料集定型迴歸模型](tutorial-data-prep.md)