---
title: '視覺化介面範例 #5：預測流失的分類 + appetency + 向上銷售'
titleSuffix: Azure Machine Learning
description: 這個視覺化介面範例管線會顯示變換的二元分類器預測，這是客戶關係管理（CRM）的一般工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693566"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>範例 5-分類：預測流失、appetency 和向上銷售 

瞭解如何使用視覺化介面來建立複雜的機器學習管線，而不需要撰寫任何一行程式碼。

這個管線會訓練三**個類別的推進式決策樹**分類器，以預測客戶關係管理（CRM）系統的一般工作：變換、appetency 和向上銷售。 資料值和標籤會分割成多個資料來源，並以匿名的客戶資訊進行編碼，不過，我們仍然可以使用視覺化介面來結合資料集，並使用遮蔽的值來定型模型。

因為您正試著回答「哪一個？」這個問題， 這稱為分類問題，但您可以套用此範例中所示的相同邏輯，以處理任何類型的機器學習服務問題，無論是回歸、分類、叢集等。

以下是此管線的完成圖形：

![管線圖表](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取範例5管線的 [**開啟**] 按鈕。

    ![開啟管線](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>資料

此管線的資料來自 KDD 杯2009。 它有50000個數據列和230個功能資料行。 這項工作是針對使用這些功能的客戶，預測變換、appetency 和向上銷售。 如需有關資料和工作的詳細資訊，請參閱[KDD 網站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="pipeline-summary"></a>管線摘要

這個視覺化介面範例管線會顯示變換、appetency 和向上銷售的二元分類器預測，這是客戶關係管理（CRM）的一般工作。

首先，部分簡單的資料處理。

- 原始資料集有許多遺漏值。 使用 [**清除遺漏的資料**] 模組，將遺漏的值取代為0。

    ![清除資料集](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- 功能和對應的變換、appetency 和向上銷售標籤位於不同的資料集中。 使用 [**加入資料行**] 模組，將標籤資料行附加至特徵資料行。 第一個資料行**Col1**是標籤資料行。 其餘的資料行（ **Var1**、 **Var2**等等）都是特徵資料行。

    ![加入資料行資料集](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- 使用**分割資料**模組，將資料集分割成定型和測試集。

- 然後使用「促進式決策樹」二進位分類器搭配預設參數來建立預測模型。 為每個工作建立一個模型，也就是每一個模型都可預測向上銷售、appetency 和變換。

## <a name="results"></a>結果

將 [**評估模型**] 模組的輸出視覺化，以查看測試集上模型的效能。 針對向上銷售工作，ROC 曲線顯示模型的效能優於隨機模型。 曲線下的區域（AUC）為0.857。 在閾值0.5，精確度為0.7，召回為0.463，而 F1 分數為0.545。

![評估結果](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 您可以移動 [**臨界值**] 滑杆，並查看二元分類工作的計量變更。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索視覺介面可用的其他範例：

- [範例 1-回歸：預測汽車的價格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險（區分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 6-分類：預測航班延誤](how-to-ui-sample-classification-predict-flight-delay.md)
- [範例 7-文字分類：書籍評論](how-to-ui-sample-text-classification.md)
