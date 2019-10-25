---
title: 視覺化介面
titleSuffix: Azure Machine Learning
description: 瞭解組成 Azure Machine Learning 的視覺介面（預覽）的術語、概念和工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692213"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Azure Machine Learning 的視覺化介面是什麼？ 

Azure Machine Learning 的視覺化介面（預覽）可讓您準備資料、定型、測試、部署、管理和追蹤機器學習模型，而不需要撰寫程式碼。

不需要任何程式設計，您會以視覺化方式連接[資料集](#datasets)和[模組](#module)來建立模型。

視覺化介面會使用您的 Azure Machine Learning[工作區](concept-workspace.md)來執行下列動作：

+ 在工作區中建立、編輯及執行[管線](#pipeline)。
+ 存取[資料集](#datasets)。
+ 使用工作區中的[計算資源](#compute)來執行管線。 
+ 註冊[模型](concept-azure-machine-learning-architecture.md#models)。
+ 將管線[發佈](#publish)為 REST 端點。
+ 將模型[部署](#deployment)為管線端點（用於批次推斷）或工作區中計算資源上的即時端點。

![視覺化介面的總覽](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流程

視覺化介面提供互動式的視覺化畫布，可讓您快速建立、測試及逐一查看模型。 

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


若要瞭解如何開始使用視覺化介面，請參閱[教學課程：使用視覺化介面預測汽車價格](ui-tutorial-automobile-price-train-score.md)。

## <a name="datasets"></a>資料集

機器學習資料集可讓您輕鬆地存取和使用您的資料。 視覺化介面中包含一些範例資料集，供您進行試驗。 您可以視需要[註冊](./how-to-create-register-datasets.md)更多資料集。

## <a name="module"></a>模組

模組是指您在資料上可執行的演算法。 視覺化介面具有許多模組，範圍從資料輸入功能到定型、評分和驗證程式。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性] 窗格中。 您可以在此窗格中修改參數來調整模型。

![模組屬性](media/ui-concept-visual-interface/properties.png)

如需流覽可用機器學習服務演算法程式庫的協助，請參閱[演算法 & 模組參考總覽](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>計算資源

使用您工作區中的計算資源來執行管線，並將部署的模型裝載為即時端點或管線端點（適用于批次推斷）。 支援的計算目標包括：

| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

計算目標會附加至您的 Machine Learning[工作區](concept-workspace.md)。 您可以在[Azure 入口網站](https://portal.azure.com)或[工作區登陸頁面（預覽）](https://ml.azure.com)中，管理工作區中的計算目標。

## <a name="publish"></a>發佈

準備好管線之後，您可以將它發佈為 REST 端點。 您可以提交[PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) ，而不需要用來建立它的 Python 程式碼。

此外，PublishedPipeline 還可以用來重新提交具有不同 PipelineParameter 值和輸入的管線。

## <a name="deployment"></a>部署

當您的預測模型準備就緒之後，請直接從視覺化介面將它部署為管線端點或即時端點。

管線端點是一個 [PublishedPipeline，您可以使用不同的 PipelineParameter 值和用於批次推斷的輸入來提交管線執行。

即時端點會在應用程式和評分模型之間提供介面。 外部應用程式可以即時與計分模型通訊。 對即時端點的呼叫會將預測結果傳回外部應用程式。 若要對即時端點進行呼叫，請傳遞部署端點時所建立的 API 金鑰。 端點是以 REST 為基礎，這是 web 程式設計專案的熱門架構選擇。

若要瞭解如何部署您的模型，請參閱[教學課程：使用視覺化介面部署機器學習服務模型](ui-tutorial-automobile-price-deploy.md)。

## <a name="next-steps"></a>後續步驟

* 瞭解預測性分析和機器學習服務的基本概念[教學課程：使用視覺化介面預測汽車價格](ui-tutorial-automobile-price-train-score.md)
* 使用其中一個範例，並進行修改以符合您的需求：

    * [範例 1-回歸：預測價格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [範例 2-回歸：預測價格和比較演算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [範例 3-分類：預測信用風險](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [範例 4-分類：預測信用風險（區分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [範例 5-分類：預測流失、appetency 和向上銷售](how-to-ui-sample-classification-predict-churn.md)
    * [範例 6-分類：預測航班延誤](how-to-ui-sample-classification-predict-flight-delay.md)

