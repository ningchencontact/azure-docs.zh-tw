---
title: 設計工具：預測汽車價格（基本）範例
titleSuffix: Azure Machine Learning
description: 建立 ML 回歸模型來預測汽車的價格，而不需要使用 Azure Machine Learning 設計工具撰寫一行程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 758852c64ae906119ba315787a759eb5d988be6b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659915"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>透過 Azure Machine Learning 設計工具使用回歸來預測汽車價格

**設計工具（預覽）範例1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

瞭解如何使用設計工具（預覽）來建立機器學習回歸模型，而不需要撰寫任何一行程式碼。

此管線會訓練**線性回歸輸入變數**，以根據品牌、型號、動力和大小等技術功能來預測汽車的價格。 因為您正試著回答「多少？」這個問題 這稱為回歸問題。 不過，在此範例中，您可以套用相同的基本步驟來處理任何類型的機器學習問題，不論其為回歸、分類、叢集等。

定型機器學習模型的基本步驟如下：

1. 取得資料
1. 預先處理資料
1. 訓練模型
1. 評估模型

以下是管線的最後一個完成圖形。 本文提供所有模組的基本原理，讓您可以自行做出類似的決策。

![管線的圖形](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下範例1以開啟它。


## <a name="get-the-data"></a>取得資料

此範例使用來自 UCI Machine Learning 儲存機制的**汽車價格資料（原始）** 資料集。 Dataset 包含26個數據行，其中包含汽車的相關資訊，包括品牌、型號、價格、車輛特徵（例如圓柱數目）、MPG，以及保險風險分數。 此範例的目標是要預測汽車的價格。

## <a name="pre-process-the-data"></a>預先處理資料

主要的資料準備工作包括資料清除、整合、轉換、減少和離散化或量化。 在設計工具中，您可以在左面板的 [**資料轉換**] 群組中找到用來執行這些作業和其他資料前置處理工作的模組。

使用 [**選取資料集中的資料行**] 模組，即可排除具有許多遺漏值的正規化損失。 然後使用 [**清除遺漏的資料**] 來移除具有遺漏值的資料列。 這有助於建立一組全新的定型資料。

![資料預先處理](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>訓練模型

機器學習服務的問題不盡相同。 常見的機器學習工作包括分類、叢集、回歸和推薦系統，其中每個都可能需要不同的演算法。 您選擇的演算法通常取決於使用案例的需求。 在您挑選演算法之後，您需要調整其參數，以定型更精確的模型。 接著，您必須根據精確度、清晰度和效率等計量來評估所有模型。

因為此範例的目標是要預測汽車價格，而且標籤資料行（價格）包含實數，所以回歸模型是不錯的選擇。 考慮到功能的數目相對較小（小於100），而且這些功能不是稀疏的，因此決策界限可能是非線性的。 因此，我們使用此管線的**決策樹系回歸**。

使用**分割資料**模組隨機分割輸入資料，讓訓練資料集包含70% 的原始資料，而測試資料集則包含30% 的原始資料。

## <a name="test-evaluate-and-compare"></a>測試、評估和比較

分割資料集，並使用不同的資料集來定型和測試模型，讓模型的評估更具目標。

在模型定型之後，您可以使用 [**評分模型**] 和 [**評估模型**] 模組來產生預測的結果，並評估模型。

**計分模型**會使用定型的模型來產生測試資料集的預測。 若要檢查結果，請選取**評分模型**的輸出埠，然後選取 [**視覺化**]。

![分數結果](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

將分數傳遞至 [**評估模型**] 模組以產生評估計量。 若要檢查結果，請選取**評估模型**的輸出埠，然後選取 [**視覺化**]。

![評估結果](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索適用于設計工具的其他範例：

- [範例 2-回歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3-使用特徵選取進行分類：收入預測](service/how-to-designer-sample-classification-predict-income.md)
- [範例 4-分類：預測信用風險（區分成本）](service/how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測流失](service/how-to-designer-sample-classification-churn.md)
- [範例 6-分類：預測航班延誤](service/how-to-designer-sample-classification-flight-delay.md)
- [範例 7-文字分類：維琪百科 SP 500 資料集](how-to-designer-sample-text-classification.md)