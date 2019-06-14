---
title: 在 Azure Notebooks 中使用 Azure Machine Learning 服務
description: 您可與 Azure Notebooks 一起使用的 Azure Machine Learning 服務範例 Notebooks 概觀。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2ef327721fd42e5274381834721fd987ec7e9d75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240516"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>在 Notebook 中使用 Azure Machine Learning 服務

Azure Notebooks 已預先設定必要的環境，可與 [Azure Machine Learning 服務](/azure/machine-learning/service/)搭配運作。 您可以輕鬆將範例專案複製到 Notebook 帳戶，探索各種不同的 Machine Learning 服務案例。

## <a name="clone-the-sample-into-your-account"></a>將範例複製到帳戶

1. 登入 [Azure Notebooks](https://notebooks.azure.com/)。
1. 選取 [我的專案]  ，瀏覽至專案儀表板。
1. 選取 **上傳 GitHub 存放庫** (向上鍵頭) 按鈕，開啟 **上傳 GitHub 存放庫** 快顯視窗。
1. 在快顯視窗的 [GitHub 存放庫]  中，輸入 `Azure/MachineLearningNotebooks`，在 [專案名稱]  中提供專案的名稱 (例如「Azure Machine Learning 服務」)，在 [專案識別碼]  中提供識別碼，視需要取消選取 [公用]  ，然後選取 [匯入]  。

    ![將 Azure Machine Learning Notebook 範例匯入 Notebooks 帳戶](media/azureml-import-project.png)

1. 一或兩分鐘之後，Azure Notebooks 會自動將您帶往新專案的儀表板。

## <a name="run-a-sample-notebook"></a>執行範例 Notebook

1. 選取 **00 - configuration.ipynb** 啟動 Notebook 的設定區段，並遵循其指示來建立 Azure Machine Learning 工作區。

    - 由於 Azure Notebooks 已包含必要的 Python 套件，您可以在「必要條件」的步驟 2 直接執行程式碼片段，驗證 Azure ML SDK 版本。

1. 設定完成後，選取 **01.getting-started**，瀏覽至包含 13 個不同範例 Notebooks 的資料夾，各個範例簡單易懂。

## <a name="next-steps"></a>後續步驟

Azure Machine Learning 服務文件包含各種不同的其他資源，引導您完成在 Notebooks 內使用機器學習服務：

- [快速入門：使用 Python 來開始使用 Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [教學課程 1：使用 Azure Machine Learning 服務將映像分類模型定型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [教學課程 2：在 Azure 容器執行個體 (ACI) 中部署映像分類模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [教學課程：使用 Azure Machine Learning 服務中的自動化機器學習將分類模型定型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

另請參閱 [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 文件。
