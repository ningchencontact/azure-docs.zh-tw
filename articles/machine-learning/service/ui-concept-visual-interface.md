---
title: 視覺化介面
titleSuffix: Azure Machine Learning service
description: 深入了解條款、 概念和 Azure Machine Learning 服務的視覺化介面 （預覽） 所組成的工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 0b158a1d713e0de8f3d1b2672aed442fce66e724
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917156"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>什麼是 Azure Machine Learning 服務的視覺化介面？ 

Azure Machine Learning 服務的視覺化介面 （預覽） 可讓您準備資料、 定型、 測試、 部署、 管理及追蹤機器學習服務模型，而不需要撰寫程式碼。

不需要任何程式設計，以視覺化方式連接[資料集](#dataset)並[模組](#module)來建構您的模型。

視覺化介面會使用您的 Azure Machine Learning 服務[工作區](concept-workspace.md)來：

+ 寫入的成品[實驗](#experiment)執行到的工作區。
+ 存取權[資料集](#dataset)。
+ 使用[計算資源](#compute)將工作區中執行實驗。 
+ 註冊[模型](concept-azure-machine-learning-architecture.md#model)。
+ [部署](#deployment)模型做為 web 服務上的計算的工作區中的資源。

![視覺化介面的概觀](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流程

視覺化介面，可讓您快速建置、 測試及模型，以反覆執行互動式的視覺化畫布。 

+ 您拖放[模組](#module)拖曳至畫布。
+ 模組連接在一起組成[實驗](#experiment)。
+ 執行實驗使用的機器學習服務工作區的計算資源。
+ 逐一查看模型設計，編輯實驗，然後再次執行它。
+ 當您準備好時，轉換您**訓練實驗**要**預測性實驗**。
+ [部署](#deployment)預測性實驗的 web 服務，讓其他人可以存取您的模型。

## <a name="experiment"></a>實驗

從頭建立實驗，或使用現有的範例實驗做為範本。  每次執行實驗，成品會儲存在您的工作區。

實驗是由資料集和您連接在一起，以建構模型中的分析模組所組成。 明確地說，有效的實驗有三個特性：

* 資料集可能僅能連線至模組。
* 模組可以連接到資料集或其他模組。
* 所有模組的輸入連接埠必須有一些連接到資料流程。
* 所有必要必須設定每個模組的參數。

如需簡易實驗的範例，請參閱[快速入門：準備及視覺化資料，而不需要在 Azure Machine Learning 中撰寫程式碼](ui-quickstart-run-experiment.md)。

預測性分析解決方案的更完整逐步解說，請參閱[教學課程：預測汽車的價格，透過視覺化介面](ui-tutorial-automobile-price-train-score.md)。

## <a name="dataset"></a>Dataset

資料集是已上傳至視覺化介面，以在模型化程序中使用的資料。 幾個範例資料集的隨附為您實驗，而且您可以上傳多個資料集，您需要的時候。

## <a name="module"></a>模組

模組是指您在資料上可執行的演算法。 視覺化介面的範圍是從資料輸入函數到訓練、 評分和驗證程序的模組數目。

模組可能有一組參數可用來設定模組的內部演算法。 當您選取畫布上的模組時，模組的參數會顯示在畫布右邊的 [屬性] 窗格中。 您可以在此窗格中修改參數來調整模型。

![模組屬性](media/ui-concept-visual-interface/properties.png)

某些說明瀏覽可用的機器學習演算法的程式庫，請參閱[演算法和模組參考概觀](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> 計算資源

使用計算執行實驗或主應用程式已部署的模型作為 web 服務工作區的資源。 支援的計算目標包括：


| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

計算目標會附加至您的 Machine Learning[工作區](concept-workspace.md)。 您管理您的工作區中的計算目標[Azure 入口網站](https://portal.azure.com)。

## <a name="deployment"></a>部署

您的預測性分析模型準備就緒後，您將它部署為 web 服務，直接從視覺化介面。

Web 服務提供應用程式與您的計分模型之間的介面。 外部應用程式可以與即時計分模型通訊。 Web 服務呼叫會傳回外部應用程式的預測結果。 若要進行 Web 服務呼叫，您可以傳遞在部署 Web 服務時所建立的 API 金鑰。 Web 服務根據其餘部分，web 程式設計專案是受歡迎的架構的選擇。

若要了解如何部署您的模型，請參閱[教學課程：部署機器學習服務模型透過視覺化介面](ui-tutorial-automobile-price-deploy.md)。

## <a name="next-steps"></a>後續步驟

* 了解基本概念的預測性分析和機器學習服務與[快速入門：準備及視覺化資料，而不需要在 Azure Machine Learning 中撰寫程式碼](ui-quickstart-run-experiment.md)。
* 使用其中一個範例，並修改套件為您的需求：
    * [範例 1-迴歸：預測價格](ui-sample-regression-predict-automobile-price-basic.md)
    * [範例 2-迴歸：預測價格，並比較演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
    * [範例 4-分類：預測信用風險 （成本機密）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [範例 5-分類：預測客戶流失、 appetency，和向上銷售](ui-sample-classification-predict-churn.md)
