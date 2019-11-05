---
title: 使用設計工具建立 ML 模型
titleSuffix: Azure Machine Learning
description: 瞭解構成 Azure Machine Learning 設計工具的術語、概念和工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517861"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>什麼是 Azure Machine Learning 設計工具（預覽）？ 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning 的設計工具可讓您準備資料、定型、測試、部署、管理和追蹤機器學習模型，而不需要撰寫程式碼。

不需要任何程式設計，您會以視覺化方式連接[資料集](#datasets)和[模組](#module)來建立模型。

設計工具會使用您的 Azure Machine Learning[工作區](concept-workspace.md)來執行下列動作：

+ 在工作區中建立、編輯及執行[管線](#pipeline)。
+ 存取[資料集](#datasets)。
+ 使用工作區中的[計算資源](#compute)來執行管線。 
+ 註冊[模型](concept-azure-machine-learning-architecture.md#models)。
+ 將管線[發佈](#publish)為 REST 端點。
+ 將模型[部署](#deployment)為管線端點（用於批次推斷）或工作區中計算資源上的即時端點。

![設計工具總覽](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流程

設計工具會提供互動式的視覺化畫布，讓您快速建立、測試及逐一查看模型。 

+ 您可以將[資料集](#datasets)和[模組](#module)拖放到畫布上。
+ 將模組連接在一起以形成[管線](#pipeline)。
+ 使用 Machine Learning 服務工作區的計算資源來執行管線。
+ 藉由編輯管線並再次執行，逐一查看您的模型設計。
+ 當您準備好時，請將**定型管線**轉換成**推斷管線**。
+ 如果您想要重新提交管線作為 REST 端點，請將它[發佈](#publish)，而不要以 Python 程式碼加以建立。
+ 將推斷管線[部署](#deployment)為管線端點或即時端點，讓其他人可以存取您的模型。

## <a name="pipeline"></a>管線

從頭開始建立 ML[管線](concept-azure-machine-learning-architecture.md#ml-pipelines)，或使用現有的範例管線做為範本。 每次執行管線時，成品都會儲存在您的工作區中。 管線執行會分組到[實驗](concept-azure-machine-learning-architecture.md#experiments)中。

管線是由資料集和分析模組所組成，您可以將它們連接在一起，以建立模型。 具體而言，有效的管線具有下列特性：

* 資料集可能只會連接到模組。
* 模組可能會連接到資料集或其他模組。
* 模組的所有輸入埠都必須有資料流程的一些連接。
* 必須設定每個模組的所有必要參數。


若要瞭解如何開始使用設計工具，請參閱[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)。

## <a name="datasets"></a>資料集

機器學習資料集可讓您輕鬆地存取和使用您的資料。 有一些範例資料集會包含在設計工具中，供您進行實驗。 您可以視需要[註冊](./how-to-create-register-datasets.md)更多資料集。

## <a name="module"></a>模組

模組是指您在資料上可執行的演算法。 設計工具有許多模組，範圍從資料輸入功能到定型、評分和驗證程式。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性] 窗格中。 您可以在此窗格中修改參數來調整模型。

![模組屬性](media/ui-concept-visual-interface/properties.png)

如需流覽可用機器學習服務演算法程式庫的協助，請參閱[演算法 & 模組參考總覽](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>計算資源

使用您工作區中的計算資源來執行管線，並將部署的模型裝載為即時端點或管線端點（適用于批次推斷）。 支援的計算目標包括：

| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

計算目標會附加至您的 Machine Learning[工作區](concept-workspace.md)。 您可以在[Azure Machine Learning studio](https://ml.azure.com)中管理工作區中的計算目標。

## <a name="publish"></a>發佈

準備好管線之後，您可以將它發佈為 REST 端點。 您可以提交[PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) ，而不需要用來建立它的 Python 程式碼。

此外，PublishedPipeline 還可以用來重新提交具有不同 PipelineParameter 值和輸入的管線。

## <a name="deployment"></a>部署

當您的預測模型就緒之後，請直接從設計工具將它部署為管線端點或即時端點。

管線端點是一個 PublishedPipeline，您可以使用不同的 PipelineParameter 值和輸入來提交管線執行，以進行批次推斷。

即時端點會在應用程式和評分模型之間提供介面。 外部應用程式可以即時與計分模型通訊。 對即時端點的呼叫會將預測結果傳回外部應用程式。 若要對即時端點進行呼叫，請傳遞部署端點時所建立的 API 金鑰。 端點是以 REST 為基礎，這是 web 程式設計專案的熱門架構選擇。

若要瞭解如何部署您的模型，請參閱[教學課程：使用設計工具部署機器學習服務模型](tutorial-designer-automobile-price-deploy.md)。

## <a name="moving-from-the-visual-interface-to-the-designer"></a>從視覺化介面移至設計工具

視覺介面（預覽）已更新，現在已 Azure Machine Learning 設計工具（預覽）。 設計工具已重新架構為使用管線型後端，可完全與 Azure Machine Learning 的其他功能整合。 

由於這些更新的結果，視覺介面的一些概念和詞彙已變更或重新命名。 請參閱下表，以瞭解最重要的概念變更。 

| 設計工具中的概念 | 先前在視覺化介面中 |
| ---- |:----:|
| 管線草稿 | 實驗 |
| 即時端點 | Web 服務 |

### <a name="migrating-to-the-designer"></a>遷移至設計工具

您可以在設計工具中，將現有的視覺化介面實驗和 web 服務轉換成管線和即時端點。 使用下列步驟來遷移您的視覺化介面資產：

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>後續步驟

* 瞭解預測性分析和機器學習服務的基本概念[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)
* 使用其中一個範例，並進行修改以符合您的需求：

- [範例 1-回歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3-使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4-分類：預測信用風險（區分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6-分類：預測航班延誤](how-to-designer-sample-classification-flight-delay.md)
- [範例 7-文字分類：維琪百科 SP 500 資料集](how-to-designer-sample-text-classification.md)

