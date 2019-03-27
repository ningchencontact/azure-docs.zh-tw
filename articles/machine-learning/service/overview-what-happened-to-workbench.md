---
title: Machine Learning Workbench 發生什麼事？
titleSuffix: Azure Machine Learning service
description: 了解 Machine Learning Workbench 應用程式發生什麼事、Azure Machine Learning 服務中有什麼變更，以及支援時間表為何。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 01/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5ff065816e287e0d4e37cdea9a7770c9ff806520
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890741"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench 發生什麼事？

Azure Machine Learning Workbench 應用程式和一些其他早期的功能已在 2018 年 9 月版本中淘汰並由其他項目取代，以便為改善的[架構](concept-azure-machine-learning-architecture.md)做準備。 

為了改善您的體驗，該版本中包含了許多由客戶意見反應所促成的重大更新。 從實驗執行到模型部署的核心功能沒有變更。 但您現在可以使用健全的 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> 和 [Azure CLI](reference-azure-machine-learning-cli.md) 來完成機器學習工作和管線。  

在舊版 Azure Machine Learning 服務中建立的大部分成品都儲存在您的本機或雲端儲存體。 這些成品永遠不會消失。

在本文中，您會了解變更的項目為何，以及它們如何影響使用 Azure Machine Learning Workbench 和其 API 的既有工作。

>[!Warning]
>本文適用對象不是 Azure Machine Learning Studio 使用者。 這適用於 Azure Machine Learning 服務客戶，而這些客戶已安裝 Workbench (預覽) 應用程式及/或具有實驗和模型管理預覽帳戶。


## <a name="what-changed"></a>變更的項目為何？

最新版的 Azure Machine Learning 服務包括下列功能：
+ [簡化的 Azure 資源模型](concept-azure-machine-learning-architecture.md)。
+ [新的入口網站 UI](how-to-track-experiments.md)，可管理您的實驗和計算目標。
+ 全新且更全面的 Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>。
+ 全新且經過擴充的適用於機器學習的 [Azure CLI 擴充功能](reference-azure-machine-learning-cli.md)。

[架構](concept-azure-machine-learning-architecture.md)已經過重新設計，以方便您使用。 您只需要一個 [Azure Machine Learning 服務工作區](concept-azure-machine-learning-architecture.md#workspace)，而不是多個 Azure 資源和帳戶。 您可以在 [Azure 入口網站](quickstart-get-started.md)中快速建立工作區。 藉由使用工作區，多個使用者可以儲存定型和部署計算目標、模型實驗、Docker 映像、已部署的模型等等。

雖然目前的版本中有改善的新 CLI 和 SDK 用戶端，但桌面工作區應用程式本身已被淘汰。 您可以在 [Azure 入口網站中的工作區儀表板](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)中管理實驗。 使用儀表板來取得實驗記錄、管理附加到工作區的計算目標、管理模型和 Docker 映像，甚至可以部署 Web 服務。

<a name="timeline"></a>

## <a name="support-timeline"></a>支援時間表

在 2019 年 1 月 9 日後，Machine Learning Workbench、Azure Machine Learning 測試和模型管理帳戶及其相關 SDK 和 CLI 的支援均已終止。 

使用新的 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>、[CLI](reference-azure-machine-learning-cli.md) 和[入口網站](quickstart-get-started.md)即可使用所有最新功能。

## <a name="what-about-run-histories"></a>那麼執行歷程記錄呢？

較舊的執行歷程記錄已無法存取，您如何繼續在最新版本中檢視您的執行。 

執行歷程記錄現在稱為**實驗**。 您可以使用 SDK、CLI 或 Azure 入口網站來收集模型的實驗和探索它們。

只有在 Microsoft Edge、Chrome 和 Firefox 上才支援入口網站的工作區儀表板：

[![線上入口網站](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

開始使用新的 CLI 和 SDK 來訓練模型以及追蹤執行歷程記錄。 您可以了解如何使用[教學課程：使用 Azure Machine Learning 服務訓練模型](tutorial-train-models-with-aml.md)。

## <a name="can-i-still-prep-data"></a>我是否仍可以準備資料？

您現有的資料準備檔案無法攜帶到最新的版本，因為我們已經沒有 Machine Learning Workbench。 但您仍可準備任何規模的資料集來進行模型化。   

對於任何規模的資料集，您可以藉由撰寫 Python 程式碼，使用 [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk) 在模型化之前快速準備您的資料。 

您可以遵循[本教學課程](tutorial-data-prep.md)，來深入了解如何使用 Azure Machine Learning 資料準備 SDK。

## <a name="will-projects-persist"></a>專案是否會保存？

您不會遺失任何程式碼或作品。 在舊版中，專案是具有本機目錄的雲端實體。 在最新版中，您將使用本機設定檔將本機目錄附加到 Azure Machine Learning 服務工作區。 [請參閱最新架構的圖表](concept-azure-machine-learning-architecture.md)。

大部分專案內容都已經在您的本機電腦上。 因此，您只需要在該目錄中建立設定檔，並在您的程式碼中參照它，就能連線到您的工作區。 若要繼續使用包含您檔案和指令碼的本機目錄，請在 ['experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python 命令中或使用 'az ml project attach' CLI 命令指定目錄的名稱。  例如︰
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

了解如何[透過主要 SDK](quickstart-create-workspace-with-python.md) 或使用 [Azure 入口網站](quickstart-get-started.md)來開始使用 Python。

## <a name="what-about-my-registered-models-and-images"></a>那麼我的註冊模型和映像呢？

如果您要繼續使用在舊模型登錄中註冊的模型，您必須將它們遷移到新工作區。 若要移轉模型，請下載模型並在新的工作區中重新加以註冊。 

您在舊映像登錄中建立的映像，必須在新工作區中重新建立才能繼續使用它們。 您可以按照[設定與建立映像](how-to-deploy-and-where.md#configureimage)章節中的步驟來重新建立這些映像。 

## <a name="what-about-deployed-web-services"></a>那麼已部署的 Web 服務呢？

現在，舊版 CLI 的支援已終止，您已無法重新部署模型，或管理原本使用模型管理帳戶進行部署的 Web 服務。 不過，只要仍支援 Azure Container Service (ACS)，這些 Web 服務就會繼續運作。

在最新版本中，模型會以 Web 服務形式部署至「Azure 容器執行個體」(ACI) 或 Azure Kubernetes Service (AKS) 叢集。 您也可以部署至 FPGA 或 Azure IoT Edge。 

請在下列文章中深入了解：
+ [部署模型的位置和方式](how-to-deploy-and-where.md)
+ [教學課程：使用 Azure Machine Learning 服務來部署模型](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>那麼舊的 SDK 和 CLI 呢？

是，它們會繼續運作到一月。 請參閱上述的[時間表](#timeline)。 建議您開始使用最新的 SDK 或 CLI 來建立新的實驗和模型。

藉由使用最新版本中的新 Python SDK，您可以在任何 Python 環境中與 Azure Machine Learning 服務互動。 了解如何安裝最新的 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>。 您也可以使用更新的 [Azure Machine Learning CLI 擴充功能](reference-azure-machine-learning-cli.md)，其中包含豐富的 `az ml` 命令集，可在任何命令列環境 (包括 Azure Cloud Shell) 中與服務互動。

## <a name="what-about-visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI 有何改變？

在這個最新版本中，擴充功能已重新命名為「適用於 Visual Studio Code 的 Azure Machine Learning」，並已經過擴充和改善，可與上述新功能搭配運作。

[![適用於 Visual Studio Code 的 Azure Machine Learning](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>那麼領域套件呢？

適用於電腦視覺、文字分析和預測的領域套件無法與最新版的 Azure Machine Learning 搭配使用。 不過，您仍然可以使用最新版的 Azure Machine Learning Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> 來建置電腦視覺、文字和預測模型，以及將它們定型。 若要了解如何遷移使用電腦視覺、文字分析和預測套件建置的現有模型，請連絡 [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com)。

## <a name="next-steps"></a>後續步驟

了解 [Azure Machine Learning 服務的最新架構](concept-azure-machine-learning-architecture.md)。 

如需此服務的概觀，請參閱[什麼是 Azure Machine Learning 服務？](overview-what-is-azure-ml.md)

如需快速入門以顯示如何使用最新版 Azure Machine Learning 服務來建立工作區、建立專案、執行指令碼，以及探索指令碼的執行歷程記錄，請嘗試[開始使用 Azure Machine Learning 服務](quickstart-get-started.md)。

如需此工作流程的更深入體驗，請遵循[完整教學課程](tutorial-train-models-with-aml.md)，其中包含使用 Azure Machine Learning 服務訓練和部署模型的詳細步驟。 
