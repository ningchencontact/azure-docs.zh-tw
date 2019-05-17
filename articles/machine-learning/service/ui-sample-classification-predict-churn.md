---
title: '分類：預測客戶流失、 appetency，和向上銷售 '
titleSuffix: Azure Machine Learning service
description: 這個視覺化介面的範例實驗示範二元分類器預測客戶流失，客戶關係管理 (CRM) 的一般工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 42724f5fcb3101015cef0d218a3d548f349646be
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785828"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>範例 5-分類：預測客戶流失、 appetency，和向上銷售 

了解如何建置複雜的機器學習服務實驗，而不需要撰寫一行程式碼使用視覺化介面。

這項實驗中訓練三**二元推進式的決策樹**來預測客戶關係管理 (CRM) 系統的一般工作的分類器： 客戶流失、 appetency，和向上銷售。 資料值和標籤會分成多個資料來源並以匿名客戶資訊，不過，我們仍然可用的視覺介面來結合資料集，並使用變碼的值來定型模型。

因為我們想要回答問題"哪一個？ 」 這稱為分類問題。 不過，您可以套用相同的步驟，在此實驗中以處理任何類型的機器學習服務問題，無論是迴歸、 分類、 叢集、 等等。

以下是完成這項實驗圖形：

![實驗圖形](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取 **開啟**範例 5 實驗的按鈕。

    ![開啟實驗](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>資料

我們使用這項實驗的資料是來自 KDD Cup 2009。 資料集有 50,000 個資料列和 230 特徵資料行。 工作是要預測客戶流失、 appetency，和向上銷售的客戶使用這些功能。 如需詳細資料和工作的詳細資訊，請參閱[KDD 網站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="experiment-summary"></a>實驗摘要

這個視覺化介面的範例實驗示範二元分類器預測客戶流失、 appetency，和向上銷售、 客戶關係管理 (CRM) 的一般工作。

首先，我們會執行一些簡單的資料處理。

- 未經處理的資料集包含許多遺漏值。 我們會使用**清除遺漏資料**模組來取代遺漏值 0。

    ![清除資料集](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- 功能，其對應的變換，appetency，和向上銷售標籤是在不同的資料集。 我們會使用**加入資料行**来附加至的特徵資料行的 標籤資料行的模組。 第一個資料行中， **Col1**，標籤資料行。 其餘的資料行**Var1**， **Var2**，依此類推，是特徵資料行。

    ![加入資料行的資料集](./media/ui-sample-classification-predict-churn/added-column1.png)

- 我們會使用**分割資料**模組將資料集分割成定型和測試集。

    然後我們使用促進式決策樹的二元分類器以預設的參數來建立預測模型。 我們建置每個工作，也就是一種模型來預測向上銷售、 appetency 和變換每一個模型。

## <a name="results"></a>結果

以視覺化方式檢視的輸出**評估模型**模組可看到模型的效能測試集。 ROC 曲線向上銷售項工作中，會顯示模型，並會優於隨機的模型。 曲線 (AUC) 底下的區域是 0.857。 在 臨界值 0.5，有效位數為 0.7 重新叫用 0.463，，是 F1 分數是 0.545。

![評估結果](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 您可以移動**閾值**滑桿和計量變更為二進位分類工作，請參閱。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

瀏覽可用的視覺介面的其他範例：

- [範例 1-迴歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-迴歸：比較針對汽車價格預測演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險 （成本機密）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
