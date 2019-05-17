---
title: 迴歸分析：預測價格
titleSuffix: Azure Machine Learning service
description: 了解如何建置機器學習模型來預測汽車的價格，而不需要撰寫一行程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787850"
---
# <a name="sample-1---regression-predict-price"></a>範例 1-迴歸：預測價格

了解如何建置機器學習迴歸模型，而不需要撰寫一行程式碼使用視覺化介面。

此實驗 train**決策樹系迴歸輸入變數**來預測汽車的價格以技術的功能，例如廠牌、 型號、 馬力，以及大小。 因為我們要試著回答 「 多少？ 」 這稱為 「 迴歸問題 」。 不過，您可以套用相同的基本步驟，在此實驗中以處理任何類型的機器學習服務問題，無論是迴歸、 分類、 叢集、 等等。

訓練機器學習服務模型的基本步驟如下：

1. 取得資料
1. 前置處理資料
1. 訓練模型
1. 評估模型

以下是最終且已完成實驗，我們將努力的圖形。 因此您可以自行進行類似的決策，我們將提供所有模組的基本原理。

![實驗的圖形](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取 **開啟**範例 1 實驗的按鈕：

    ![開啟實驗](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>取得資料

在此實驗中，我們會使用**汽車價格資料 （原始）** 資料集，也就是從 UCI 機器學習儲存機制。 資料集包含 26 個資料行包含汽車，包括廠牌、 型號、 價格、 車輛功能 （例如磁柱數目）、 MPG、 以及保險風險評分的相關資訊。 這項實驗的目標是預測汽車價格。

## <a name="pre-process-the-data"></a>前置處理資料

主要資料準備工作包括清理的資料、 整合、 轉換、 縮減和離散化或量化。 視覺化介面，在中，您可以找到要執行這些作業和其他的資料前置處理工作模組**Data Transformation**群組在左面板中。

我們會使用**選取資料集中的資料行**模組，以排除 自負虧損，有許多遺漏值。 我們接著使用**清除遺漏資料**移除有遺漏值的資料列。 這可協助建立全新的培訓資料集。

![資料預先處理](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>訓練模型

機器學習服務問題而有所不同。 常見的機器學習工作包括分類、 叢集、 迴歸和推薦系統，每次都可能會需要不同的演算法。 您選擇的演算法通常取決於使用案例的需求。 您挑選的演算法之後，您需要調整它的參數來定型更準確的模型。 接著，您必須評估所有模型，根據計量，例如精確度、 清晰度和效率。

由於這項實驗的目標是要預測汽車的價格，而且標籤資料行 （價格） 包含實際數字，迴歸模型會是不錯的選擇。 請考慮 （低於 100） 是相對較小的特徵數目，以及這些功能不是疏鬆，則很可能是非線性中決策邊界。 因此我們會使用**決策樹系迴歸**此實驗中。

我們會使用**分割資料**隨機將輸入的資料分割，使訓練資料集包含 70%的原始資料，以及測試資料集包含 30%的原始資料的模組。

## <a name="test-evaluate-and-compare"></a>測試、 評估和比較

 我們將資料集，並使用不同的資料集來訓練及測試模型，進行評估的模型更客觀。

訓練模型之後，我們會使用**評分模型**並**評估模型**模組來產生預測的結果及評估模型。

**評分模型**使用定型的模型，以產生測試資料集的預測。 若要檢查結果，請選取 輸出連接埠**評分模型**，然後選取**視覺化**。

![評分結果](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

我們接著將傳遞至分數**評估模型**模組來產生評估度量。 若要檢查結果，請選取 輸出連接埠**評估模型**，然後選取**視覺化**。

![評估結果](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

瀏覽可用的視覺介面的其他範例：

- [範例 2-迴歸：比較針對汽車價格預測演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險 （成本機密）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測客戶流失](ui-sample-classification-predict-churn.md)