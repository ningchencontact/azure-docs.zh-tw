---
title: 什麼是工作區
titleSuffix: Azure Machine Learning service
description: 了解哪一個工作區，以及為什麼需要另一個用於 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989840"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什麼是 Azure Machine Learning 工作區？

工作區是 Azure Machine Learning 服務的最上層資源。 它可以在您使用 Azure Machine Learning 服務時，提供集中式位置以處理您建立的所有成品。

工作區會保留定型執行，包括記錄、 計量、 輸出和指令碼的快照集的記錄。 您可以使用此資訊來判斷哪一個定型回合會產生最佳模型。  

您要的模型之後，您會註冊它與工作區。 您可以使用已註冊的模型及評分指令碼部署至 Azure Container Instances，Azure Kubernetes 服務，或做為 REST 架構 HTTP 端點的欄位可程式化 gate array (FPGA)。 您也可以部署到 Azure IoT Edge 裝置的模型做為模組。

## <a name="taxonomy"></a>分類法 

下圖說明工作區的分類：

[![工作區分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

下圖顯示工作區的下列元件：

+ 工作區可以包含[Notebook Vm](quickstart-run-cloud-notebook.md)，雲端資源以執行 Azure Machine Learning 所需的 Python 環境設定。
+ [使用者角色](how-to-assign-roles.md)可讓您與其他使用者、 小組或專案中共用您的工作區。
+ [計算目標](concept-azure-machine-learning-architecture.md#compute-target)用來執行您的實驗。
+ 當您建立工作區中，[相關聯的資源](#resources)也會為您建立。
+ [實驗](concept-azure-machine-learning-architecture.md#experiment)是您用來建置您的模型定型執行。  您可以建立並執行與實驗
    + [Azure SDK 適用於 Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
    + [自動化 （預覽） 的機器學習服務實驗](how-to-create-portal-experiments.md)在 Azure 入口網站中的區段。
    + [視覺化介面 （預覽）](ui-concept-visual-interface.md)。
+ [管線](concept-azure-machine-learning-architecture.md#pipeline)是用於定型和重新定型模型的可重複使用工作流程。
+ [資料集](concept-azure-machine-learning-architecture.md#dataset)協助管理您用於建立的模型訓練和管線的資料。
+ 您想要部署的模型之後，您會建立[已註冊的模型](concept-azure-machine-learning-architecture.md#model-registry)。
+ 使用已註冊的模型和評分指令碼來建立[部署](concept-azure-machine-learning-architecture.md#image-registry)。

## <a name="tools-for-workspace-interaction"></a>工作區互動的工具

您可以透過下列方式互動與您工作區：

+ 網路：
    + [Azure 入口網站](https://azure.portal.com)
    + [視覺介面 （預覽）](ui-concept-visual-interface.md)
+ 在 Python 中使用 Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ 使用 Azure Machine Learning 命令列上[CLI 擴充功能](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>機器學習服務工作區

機器學習工作讀取和/或寫入您的工作區中的成品。 

+ 執行實驗來定型模型-寫入實驗的工作區的執行的結果。
+ 使用自動化來定型模型-ML 訓練結果寫入工作區。
+ 在工作區中註冊模型。
+ 部署模型-使用已註冊的模型來建立部署。
+ 建立並執行可重複使用的工作流程。
+ 檢視機器學習服務實驗、 管線、 模型、 部署的成品。
+ 追蹤和監視模型。

## <a name="workspace-management"></a>工作區管理

您也可以執行下列工作區管理工作：

| 工作區管理工作   | 入口網站              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| 建立工作區        | **&check;**     | **&check;** | **&check;** |
| 建立和管理計算資源    | **&check;**   | **&check;** |  **&check;**   |
| 管理工作區的存取    | **&check;**   | |  **&check;**    |
| 建立 Notebook VM | **&check;**   | |     |

開始使用服務時[建立工作區](setup-create-workspace.md)。

## <a name="resources"></a> 相關聯的資源

建立新的工作區時，會自動建立工作區使用的幾個 Azure 資源：

+ [Azure 容器登錄](https://azure.microsoft.com/services/container-registry/)：註冊您在定型期間及部署模型時使用的 Docker 容器。 若要降低成本，是 ACR**消極式載入**直到部署映像建立。
+ [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)：用來作為工作區的預設資料存放區。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)：儲存與模型有關的監視資訊。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：儲存計算目標所使用的密碼與工作區所需的其他機密資訊。

> [!NOTE]
> 除了建立新的版本之外，您也可以使用現有的 Azure 服務。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Machine Learning 服務，請參閱：

+ [Azure Machine Learning 服務概觀](overview-what-is-azure-ml.md)
+ [建立工作區](setup-create-workspace.md)
+ [管理工作區](how-to-manage-workspace.md)
+ [教學課程：將模型定型](tutorial-train-models-with-aml.md)
