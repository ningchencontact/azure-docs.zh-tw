---
title: 視覺化介面
titleSuffix: Azure Machine Learning
description: 瞭解組成 Azure Machine Learning 的視覺介面（預覽）的術語、概念和工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 65daf1468d27825d9904a14e42e43796d3985321
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996513"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Azure Machine Learning 的視覺化介面是什麼？ 

Azure Machine Learning 的視覺化介面（預覽）可讓您準備資料、定型、測試、部署、管理和追蹤機器學習模型，而不需要撰寫程式碼。

不需要任何程式設計，您會以視覺化方式連接[資料集](#dataset)和[模組](#module)來建立模型。

視覺化介面會使用您的 Azure Machine Learning[工作區](concept-workspace.md)來執行下列動作：

+ 將[實驗](#experiment)執行的構件寫入工作區。
+ 存取[資料集](#dataset)。
+ 使用工作區中的[計算資源](#compute)來執行實驗。 
+ 註冊[模型](concept-azure-machine-learning-architecture.md#models)。
+ 在工作區中的計算資源上，將模型[部署](#deployment)為 web 服務。

![視覺化介面的總覽](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流程

視覺化介面提供互動式的視覺化畫布，可讓您快速建立、測試及逐一查看模型。 

+ 將[模組](#module)拖放到畫布上。
+ 將模組連接在一起以形成[實驗](#experiment)。
+ 使用 Machine Learning 服務工作區的計算資源來執行實驗。
+ 藉由編輯實驗並再次執行，逐一查看您的模型設計。
+ 當您準備好時，請將您的**訓練實驗**轉換成**預測實驗**。
+ 將預測性實驗[部署](#deployment)為 web 服務，讓其他人可以存取您的模型。

## <a name="experiment"></a>實驗

從頭開始建立實驗，或使用現有的範例實驗做為範本。  每次執行實驗時，成品都會儲存在您的工作區中。

實驗包含資料集和分析模組，您可以將它們連接在一起，以建立模型。 明確地說，有效的實驗有三個特性：

* 資料集可能只會連接到模組。
* 模組可能會連接到資料集或其他模組。
* 模組的所有輸入埠都必須有資料流程的一些連接。
* 必須設定每個模組的所有必要參數。


若要瞭解如何開始使用視覺化介面，請參閱[教學課程：透過視覺化介面預測汽車價格](ui-tutorial-automobile-price-train-score.md)。

## <a name="dataset"></a>資料集

「資料集」（dataset）是已上傳至視覺化介面以在模型化程式中使用的資料。 其中包含一些範例資料集供您進行實驗，而且您可以視需要上傳更多資料集。

## <a name="module"></a>模組

模組是指您在資料上可執行的演算法。 視覺化介面具有許多模組，範圍從資料輸入功能到定型、評分和驗證程式。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性] 窗格中。 您可以在此窗格中修改參數來調整模型。

![模組屬性](media/ui-concept-visual-interface/properties.png)

如需流覽可用機器學習服務演算法程式庫的協助，請參閱[演算法 & 模組參考總覽](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>計算資源

使用工作區中的計算資源來執行實驗，或將部署的模型裝載為 web 服務。 支援的計算目標包括：


| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

計算目標會附加至您的 Machine Learning[工作區](concept-workspace.md)。 您可以在[Azure 入口網站](https://portal.azure.com)或[工作區登陸頁面（預覽）](https://ml.azure.com)中，管理工作區中的計算目標。

## <a name="deployment"></a>部署

預測性分析模型準備就緒之後，您就可以直接從視覺化介面將它部署為 web 服務。

Web 服務會在應用程式和評分模型之間提供介面。 外部應用程式可以即時與計分模型通訊。 呼叫 web 服務會將預測結果傳回外部應用程式。 若要進行 Web 服務呼叫，您可以傳遞在部署 Web 服務時所建立的 API 金鑰。 Web 服務是以 REST 為基礎，這是一種熱門的 web 程式設計專案架構選擇。

若要瞭解如何部署您的模型， [請參閱教學課程：使用視覺化介面](ui-tutorial-automobile-price-deploy.md)部署機器學習模型。

## <a name="next-steps"></a>後續步驟

* 使用[教學課程來瞭解預測性分析和機器學習的基本概念：透過視覺化介面預測汽車價格](ui-tutorial-automobile-price-train-score.md)
* 使用其中一個範例，並進行修改以符合您的需求：
    * [範例 1-回歸：預測價格](ui-sample-regression-predict-automobile-price-basic.md)
    * [範例 2-回歸：預測價格和比較演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
    * [範例 4-分類：預測信用風險（區分成本）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [範例 5-分類：預測流失、appetency 和向上銷售](ui-sample-classification-predict-churn.md)
