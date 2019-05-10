---
title: 迴歸分析：預測價格，並比較演算法
titleSuffix: Azure Machine Learning service
description: 這個視覺化介面的範例實驗示範如何比較兩個預測汽車價格迴歸模型的效能。 程序包括訓練、 測試和評估模型的汽車價格資料 （原始） 資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 2a4a9e74fa7f56b67f0f4a64f6619db1c5c69a2c
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442147"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>範例 2-迴歸：預測價格，並比較演算法

這個視覺化介面的範例實驗示範如何比較兩個預測汽車價格迴歸模型的效能。 程序包括訓練、 測試和使用評估模型**汽車價格資料 （原始）** 資料集。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取 **開啟**範例 2 實驗的按鈕：

    ![開啟實驗](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="related-sample"></a>相關的範例

[範例 1-迴歸：汽車價格預測 （基本）](ui-sample-regression-predict-automobile-price-basic.md)提供更簡單的實驗，可以解決這項實驗中相同的問題，但使用只有一個迴歸模型。 如果您要尋找的迴歸的基本範例，請參考它。

## <a name="experiment-summary"></a>實驗摘要

我們可以使用下列步驟來建立實驗：

1. 取得資料。
1. 前置處理資料。
1. 定型模型。
1. 測試、 評估和比較模型。

以下是實驗的完整的圖形:

[![實驗的圖形](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png#lightbox)


## <a name="get-the-data"></a>取得資料

在此實驗中，我們會使用**汽車價格資料 （原始）** 資料集，也就是從 UCI 機器學習儲存機制。 此資料集包含 26 個資料行包含汽車，包括廠牌、 型號、 價格、 車輛功能 （例如磁柱數目）、 MPG、 以及保險風險評分的相關資訊。 這項實驗的目標是要預測汽車價格。

## <a name="pre-process-the-data"></a>前置處理資料

主要資料準備工作包括清理的資料、 整合、 轉換、 縮減和離散化或量化。 視覺化介面，在中，您可以找到要執行這些作業和其他的資料前置處理工作模組**Data Transformation**群組在左面板中。

在此實驗中，我們會使用**選取資料集中的資料行**模組，以排除 自負虧損，有許多遺漏值。 我們接著使用**清除遺漏資料**移除有遺漏值的資料列。 這可協助建立全新的培訓資料集。

![資料預先處理](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>訓練模型

機器學習服務問題而有所不同。 常見的機器學習工作包括分類、 叢集、 迴歸和推薦系統，每次都可能會需要不同的演算法。 您選擇的演算法通常取決於使用案例的需求。 您挑選的演算法之後，您需要調整它的參數來定型更準確的模型。 接著，您必須評估所有模型，根據計量，例如精確度、 清晰度和效率。

由於這項實驗的目標是要預測汽車的價格，而且標籤資料行 （價格） 包含實際數字，迴歸模型會是不錯的選擇。 請考慮 （低於 100） 是相對較小的特徵數目，以及這些功能不是疏鬆，則很可能是非線性中決策邊界。

為了比較提及不同演算法的效能，我們會使用兩種的非線性演算法**促進式決策樹迴歸**並**決策樹系迴歸**，以建立模型。 這兩個演算法具有參數，您可以變更，但我們在此實驗中使用的預設值。

我們會使用**分割資料**隨機將輸入的資料分割，使訓練資料集包含 70%的原始資料，以及測試資料集包含 30%的原始資料的模組。

## <a name="test-evaluate-and-compare-the-models"></a>測試、 評估和比較模型

上一節中所述，我們可以使用來訓練和測試模型的兩個不同的隨機選擇的資料集。 我們將資料集，並使用不同的資料集來訓練及測試模型，進行評估的模型更客觀。

訓練模型之後，我們會使用**評分模型**並**評估模型**模組來產生預測的結果及評估模型。 **評分模型**使用定型的模型，以產生測試資料集的預測。 我們接著將傳遞至分數**評估模型**來產生評估度量。

在此實驗中，我們會使用兩個執行個體**評估模型**來比較兩個配對的模型。

首先，我們會比較兩種演算法定型資料集上。
其次，我們會比較測試資料集上的兩種演算法。
結果如下︰

![比較的結果](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

這些結果顯示，以建置模型**促進式決策樹迴歸**具有較低的根目錄上建立的模型比均方根誤差**決策樹系迴歸**。

這兩個演算法比看不見的測試資料集上定型資料集上有較低的錯誤。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

瀏覽可用的視覺介面的其他範例：

- [範例 1-迴歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險 （成本機密）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測客戶流失](ui-sample-classification-predict-churn.md)
