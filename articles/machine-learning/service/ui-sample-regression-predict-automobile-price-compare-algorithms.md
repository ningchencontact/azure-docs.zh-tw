---
title: '視覺化介面範例 #3：回歸至價格並比較演算法'
titleSuffix: Azure Machine Learning
description: 本文說明如何使用視覺化介面來建立複雜的機器學習實驗，而不需要撰寫任何一行程式碼。 瞭解如何定型和比較多個回歸模型，以根據技術功能來預測汽車的價格
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 2c45ccf55b1adde7150dee17fd562b24ee4777ba
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997092"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>範例 2-回歸：預測價格和比較演算法

瞭解如何使用視覺化介面來建立複雜的機器學習實驗，而不需要撰寫任何一行程式碼。 這個範例會訓練並比較多個回歸模型，以根據其技術功能來預測汽車的價格。 我們將提供此實驗中所做選擇的基本原理，讓您可以處理自己的機器學習服務問題。

如果您只是開始使用機器學習，您可以查看此實驗的[基本版本](ui-sample-regression-predict-automobile-price-basic.md)，以查看基本的回歸實驗。

以下是此實驗的完成圖形：

[![實驗的圖形](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取範例2實驗的 [**開啟**] 按鈕：

    ![開啟實驗](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>實驗摘要

我們會使用下列步驟來建立實驗：

1. 取得資料。
1. 前置處理資料。
1. 將模型定型。
1. 測試、評估和比較模型。

## <a name="get-the-data"></a>取得資料

在此實驗中，我們會使用來自 UCI Machine Learning 儲存機制的**汽車價格資料（原始）** 資料集。 此資料集包含26個數據行，其中包含汽車的相關資訊，包括品牌、型號、價格、車輛特徵（例如柱面數）、MPG，以及保險風險分數。 此實驗的目標是要預測汽車的價格。

## <a name="pre-process-the-data"></a>預先處理資料

主要的資料準備工作包括資料清除、整合、轉換、減少和離散化或量化。 在視覺化介面中，您可以在左面板的 [**資料轉換**] 群組中，找到執行這些作業和其他資料前置處理工作的模組。

在此實驗中，我們使用 [**選取資料集中的資料行**] 模組來排除具有許多遺漏值的正規化損失。 然後，我們會使用 [**清除遺漏的資料**] 來移除具有遺漏值的資料列。 這有助於建立一組全新的定型資料。

![資料預先處理](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>訓練模型

機器學習服務的問題不盡相同。 常見的機器學習工作包括分類、叢集、回歸和推薦系統，其中每個都可能需要不同的演算法。 您選擇的演算法通常取決於使用案例的需求。 在您挑選演算法之後，您需要調整其參數，以定型更精確的模型。 接著，您必須根據精確度、清晰度和效率等計量來評估所有模型。

因為此實驗的目標是要預測汽車價格，而且標籤資料行（價格）包含實數，所以回歸模型是不錯的選擇。 考慮到功能的數目相對較小（小於100），而且這些功能不是稀疏的，因此決策界限可能是非線性的。

為了比較不同演算法的效能，我們使用兩個非線性演算法，促進式**決策樹回歸**和決策樹系**回歸**來建立模型。 這兩種演算法都有您可以變更的參數，但我們使用此實驗的預設值。

我們會使用**分割資料**模組來隨機分割輸入資料，讓訓練資料集包含 70% 的原始資料，而測試資料集會包含 30% 的原始資料。

## <a name="test-evaluate-and-compare-the-models"></a>測試、評估和比較模型

我們會使用兩個不同的隨機播放資料集來定型模型，然後再加以測試，如上一節所述。 我們會分割資料集，並使用不同的資料集來定型和測試模型，讓模型的評估更具目標。

在模型定型之後，我們會使用**評分模型**和**評估模型**模組來產生預測的結果，並評估模型。 **計分模型**會使用定型的模型來產生測試資料集的預測。 接著，我們會將分數傳遞給**評估模型**，以產生評估計量。

在此實驗中，我們會使用兩個**評估模型**實例來比較兩組模型。

首先，我們會比較訓練資料集上的兩個演算法。
第二，我們比較測試資料集上的兩個演算法。
結果如下︰

![比較結果](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

這些結果顯示使用促進式**決策樹回歸**所建立的模型，其根平均平方誤差低於建立在決策樹系**回歸**的模型。

這兩種演算法在訓練資料集上的錯誤比未可見的測試資料集更低。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索視覺介面可用的其他範例：

- [範例 1-回歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險（區分成本）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測流失](ui-sample-classification-predict-churn.md)
- [範例 6-分類：預測航班延誤](ui-sample-classification-predict-flight-delay.md)
