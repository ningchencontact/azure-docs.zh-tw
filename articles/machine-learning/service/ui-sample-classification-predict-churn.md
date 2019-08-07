---
title: 分類預測變換 + appetency + 向上銷售
titleSuffix: Azure Machine Learning service
description: 此視覺化介面範例實驗會顯示變換的二元分類器預測, 這是客戶關係管理 (CRM) 的一般工作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 172089d5371d8c3e38a2a22b3285b5eb180baf00
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742294"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>範例 5-分類:預測流失、appetency 和向上銷售 

瞭解如何使用視覺化介面來建立複雜的機器學習實驗, 而不需要撰寫任何一行程式碼。

此實驗會將三個類別的推進**式決策樹**分類器定型, 以預測客戶關係管理 (CRM) 系統的一般工作: 變換、appetency 和向上銷售。 資料值和標籤會分割成多個資料來源, 並以匿名的客戶資訊進行編碼, 不過, 我們仍然可以使用視覺化介面來結合資料集, 並使用未編碼的值來定型模型。

因為我們正試著回答「哪一個？」這個問題, 這稱為分類問題。 不過, 您可以在此實驗中套用相同的步驟, 以處理任何類型的機器學習問題, 無論是回歸、分類、叢集等。

以下是此實驗的完成圖形:

![實驗圖表](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取範例5實驗的 [**開啟**] 按鈕。

    ![開啟實驗](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

我們用於此實驗的資料來自 KDD 杯2009。 資料集具有50000個數據列和230個功能資料行。 這項工作是針對使用這些功能的客戶, 預測變換、appetency 和向上銷售。 如需有關資料和工作的詳細資訊, 請參閱[KDD 網站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="experiment-summary"></a>實驗摘要

此視覺化介面範例實驗會顯示變換、appetency 和向上銷售的二元分類器預測, 這是客戶關係管理 (CRM) 的一般工作。

首先, 我們會進行一些簡單的資料處理。

- 原始資料集包含許多遺漏值。 我們使用 [**清除遺漏的資料**] 模組, 將遺漏的值取代為0。

    ![清除資料集](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- 功能和對應的變換、appetency 和向上銷售標籤位於不同的資料集中。 我們使用 [**加入資料行**] 模組, 將標籤資料行附加至特徵資料行。 第一個資料行**Col1**是標籤資料行。 其餘的資料行 ( **Var1**、 **Var2**等等) 都是特徵資料行。

    ![加入資料行資料集](./media/ui-sample-classification-predict-churn/added-column1.png)

- 我們使用**分割資料**模組, 將資料集分割成定型和測試集。

    然後, 我們會使用「促進式決策樹」二進位分類器搭配預設參數來建立預測模型。 我們會為每個工作建立一個模型, 也就是每一個模型都可預測向上銷售、appetency 和變換。

## <a name="results"></a>結果

將 [**評估模型**] 模組的輸出視覺化, 以查看測試集上模型的效能。 針對向上銷售工作, ROC 曲線顯示模型的效能優於隨機模型。 曲線下的區域 (AUC) 為0.857。 在閾值 0.5, 精確度為 0.7, 召回為 0.463, 而 F1 分數為0.545。

![評估結果](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 您可以移動 [**臨界值**] 滑杆, 並查看二元分類工作的計量變更。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索視覺介面可用的其他範例:

- [範例 1-回歸:預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-回歸:比較汽車價格預測的演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類:預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類:預測信用風險 (區分成本)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 6-分類:預測航班延誤](ui-sample-classification-predict-flight-delay.md)