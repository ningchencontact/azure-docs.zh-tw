---
title: 什麼是工作區
titleSuffix: Azure Machine Learning
description: 工作區是 Azure Machine Learning 的最上層資源。 它會保留所有定型執行的歷程記錄，包括記錄、計量、輸出，以及腳本的快照集。 您可以使用此資訊來判斷哪一個定型回合會產生最佳模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: 677a52572658f80d9cde4de28dd02d6e957407d2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269203"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什麼是 Azure Machine Learning 工作區？

工作區是 Azure Machine Learning 的最上層資源，提供一個集中的位置來處理您在使用 Azure Machine Learning 時所建立的所有成品。  工作區會保留所有定型執行的歷程記錄，包括記錄、計量、輸出，以及腳本的快照集。 您可以使用此資訊來判斷哪一個定型回合會產生最佳模型。  

一旦擁有您想要的模型之後，您就可以向工作區註冊它。 接著，您可以使用已註冊的模型和評分腳本，將部署到 Azure 容器實例、Azure Kubernetes Service 或可現場程式化閘道陣列（FPGA），做為 REST 架構的 HTTP 端點。 您也可以將模型部署到 Azure IoT Edge 裝置作為模組。

## <a name="taxonomy"></a>分類法 

下圖說明工作區的分類：

[![工作區分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

此圖顯示工作區的下列元件：

+ 工作區可以包含[筆記本 vm](tutorial-1st-experiment-sdk-setup.md)、使用執行 Azure Machine Learning 所需的 Python 環境所設定的雲端資源。
+ [使用者角色](how-to-assign-roles.md)可讓您與其他使用者、小組或專案共用您的工作區。
+ [計算目標](concept-azure-machine-learning-architecture.md#compute-targets)是用來執行您的實驗。
+ 當您建立工作區時，也會為您建立[相關聯的資源](#resources)。
+ [實驗](concept-azure-machine-learning-architecture.md#experiments)是用來建立模型的定型回合。  您可以使用來建立及執行實驗
    + [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
    + Azure 入口網站或您的工作區登陸頁面（預覽）中的[自動化機器學習實驗（預覽）](how-to-create-portal-experiments.md)區段。
    + [視覺化介面（預覽）](ui-concept-visual-interface.md)。
+ [管線](concept-azure-machine-learning-architecture.md#ml-pipelines)是可重複使用的工作流程，用於訓練和重新定型您的模型。
+ 資料[集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)可協助管理您用於模型定型和管線建立的資料。
+ 一旦擁有您想要部署的模型之後，您就可以建立一個已註冊的模型。
+ 使用已註冊的模型和評分腳本來建立[部署](concept-azure-machine-learning-architecture.md#deployment)。

## <a name="tools-for-workspace-interaction"></a>工作區互動的工具

您可以透過下列方式與工作區進行互動：

+ 在網站上：
    + [Azure 入口網站](https://portal.azure.com)
    + 您的[工作區登陸頁面（預覽）](https://ml.azure.com)
    + [視覺化介面（預覽）](ui-concept-visual-interface.md)
+ 在 Python 中使用 Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ 在命令列上使用 Azure Machine Learning [CLI 擴充](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)功能

## <a name="machine-learning-with-a-workspace"></a>具有工作區的機器學習服務

機器學習服務工作會讀取及/或寫入成品至您的工作區。 

+ 執行實驗以將模型寫入實驗執行結果定型至工作區。
+ 使用自動化 ML 來定型模型-將定型結果寫入工作區。
+ 在工作區中註冊模型。
+ 部署模型-使用已註冊的模型來建立部署。
+ 建立並執行可重複使用的工作流程。
+ 查看機器學習成品，例如實驗、管線、模型、部署。
+ 追蹤和監視模型。




## <a name="workspace-management"></a>工作區管理

您也可以執行下列工作區管理作業：

| 工作區管理工作   | 入口網站              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| 建立工作區        | **&check;**     | **&check;** | **&check;** |
| 建立和管理計算資源    | **&check;**   | **&check;** |  **&check;**   |
| 管理工作區存取    | **&check;**   | |  **&check;**    |
| 建立 Notebook VM | **&check;**   | |     |

### <a name='create-workspace'></a>建立工作區

有多種方式可建立工作區。

* 使用點和按一下介面的[Azure 入口網站](how-to-manage-workspace.md)，引導您完成每個步驟。
* 使用[適用于 python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) ，從 Python 腳本或木星筆記本即時建立工作區
* 當您需要使用公司安全性標準來自動化或自訂建立時，請使用[Azure Resource Manager 範本](how-to-create-workspace-template.md)或[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) 。
* 如果您在 Visual Studio Code 中工作，請使用[VS Code 延伸](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)模組。

## <a name="resources"></a>相關聯的資源

建立新的工作區時，會自動建立工作區使用的幾個 Azure 資源：

+ [Azure 容器登錄](https://azure.microsoft.com/services/container-registry/)：註冊您在定型期間及部署模型時使用的 Docker 容器。 為了將成本降至最低，在建立部署映射之前，會**延遲載入**ACR。
+ [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)：用來作為工作區的預設資料存放區。  與您的筆記本 Vm 搭配使用的 Jupyter 筆記本也會儲存在這裡。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：儲存與模型有關的監視資訊。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：儲存計算目標所使用的密碼與工作區所需的其他機密資訊。

> [!NOTE]
> 除了建立新的版本之外，您也可以使用現有的 Azure 服務。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning，請參閱：

+ [Azure Machine Learning 總覽](overview-what-is-azure-ml.md)
+ [建立工作區](how-to-manage-workspace.md)
+ [管理工作區](how-to-manage-workspace.md)
+ [教學課程：將模型定型](tutorial-train-models-with-aml.md)
