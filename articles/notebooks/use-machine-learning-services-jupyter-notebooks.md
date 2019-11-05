---
title: 在 Azure Notebooks 中使用 Azure Machine Learning
description: 概述可用於 Azure Notebooks Azure Machine Learning 的範例筆記本。
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496637"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>在筆記本中使用 Azure Machine Learning

Azure Notebooks 已預先設定為使用[Azure Machine Learning](/azure/machine-learning/service/)所需的環境。 您可以輕鬆將範例專案複製到 Notebook 帳戶，探索各種不同的 Machine Learning 服務案例。

## <a name="clone-the-sample-into-your-account"></a>將範例複製到帳戶

1. 登入 [Azure Notebooks](https://notebooks.azure.com/)。
1. 選取 [**我的專案**] 移至 [專案] 儀表板。
1. 選取 [**上傳 github**存放庫] \ （向上箭號）按鈕，以開啟 [**上傳 github 儲存**機制] 快顯視窗。
1. 在快顯視窗中，輸入**GitHub 存放庫**中的 `Azure/MachineLearningNotebooks`，在 [**專案名稱**] 中提供專案的名稱（例如 "Azure Machine Learning"），在 [**專案**識別碼] 中提供識別碼，**如有需要則選取**[清除]，然後選取 [匯**入**]。

    ![將 Azure Machine Learning Notebook 範例匯入 Notebooks 帳戶](media/azureml-import-project.png)

1. 一或兩分鐘之後，Azure Notebooks 會自動將您帶往新專案的儀表板。

## <a name="run-a-sample-notebook"></a>執行範例 Notebook

1. 選取 **00 - configuration.ipynb** 啟動 Notebook 的設定區段，並遵循其指示來建立 Azure Machine Learning 工作區。

    - 由於 Azure Notebooks 已包含必要的 Python 套件，您可以在「必要條件」的步驟 2 直接執行程式碼片段，驗證 Azure ML SDK 版本。

1. 設定完成後，請選取 [ **01. 開始**使用] 以開啟包含13個不同範例筆記本的資料夾，其中每一個都是一目了然的。

## <a name="next-steps"></a>後續步驟

Azure Machine Learning 檔包含各種不同的資源，可引導您在筆記本中使用 Machine Learning：

- [快速入門：使用 Python 來開始使用 Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [教學課程 #1：使用 Azure Machine Learning 將影像分類模型定型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [教學課程 #2：在 Azure 容器實例（ACI）中部署影像分類模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [教學課程：在 Azure Machine Learning 中使用自動化機器學習將分類模型定型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

另請參閱 [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 文件。
