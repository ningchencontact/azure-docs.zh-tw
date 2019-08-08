---
title: 分類預測信用風險
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用視覺化介面來建立機器學習分類器, 而不需撰寫任何一行程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d1b3b8fa61492f3cf18e44e3e3046f32ea9c9b03
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855977"
---
# <a name="sample-3---classification-predict-credit-risk"></a>範例 3-分類:預測信用風險

瞭解如何使用視覺化介面來建立機器學習分類器, 而不需撰寫任何一行程式碼。 這個範例會訓練**兩個類別**的推進式決策樹, 根據信用額度應用程式資訊 (例如信用歷程記錄、年齡和信用卡數目) 來預測信用風險 (高或低)。

因為我們正試著回答「哪一個？」這個問題, 這稱為分類問題。 不過, 您可以套用相同的基本程式來處理任何類型的機器學習問題, 不論是回歸、分類、叢集等。

以下是此實驗的完成圖形:

![實驗的圖形](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取範例3實驗的 [**開啟**] 按鈕:

    ![開啟實驗](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>相關範例

[範例 4-分類:信用風險預測 (區分成本)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)提供一個先進的實驗, 可解決與此實驗相同的問題。 它會示範如何使用**執行 Python 腳本**模組來執行_成本敏感_的分類, 並比較兩個二元分類演算法的效能。 如果您想要深入瞭解如何建立分類實驗, 請參閱此資訊。

## <a name="data"></a>Data

我們會使用來自 UC Irvine 存放庫的德國信用卡資料集。
此資料集包含1000範例, 其中含有20個功能和1個標籤。 每個範例都代表一個人。 這些功能包括數值和類別功能。 如需類別功能的意義, 請參閱[UCI 網站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最後一個資料行是代表信用風險的標籤, 只有兩個可能的值: [高信用風險 = 2] 和 [低信用風險] = 1。

## <a name="experiment-summary"></a>實驗摘要

我們會依照下列步驟來建立實驗:

1. 將德國信用卡的 UCI 資料資料集模組拖曳至實驗的畫布。
1. 新增 [**編輯中繼資料**] 模組, 讓我們可以為每個資料行新增有意義的名稱。
1. 加入**分割資料**模組, 以建立定型集和測試集。 將第一個輸出資料集中的資料列分數設定為0.7。 此設定會指定將 70% 的資料輸出到模組的左側埠, 並將其餘埠輸出至正確的埠。 我們會使用左側的資料集進行定型, 將它用來測試。
1. 加入**兩個類別的推進式決策樹**模組, 以初始化促進式決策樹分類器。
1. 新增**訓練模型**模組。 將上一個步驟中的分類器連接到**定型模型**的左側輸入埠。 將定型集 (**分割資料**的左側輸出埠) 新增至**定型模型**的右側輸入埠。 **定型模型**會將分類器定型。
1. 新增**評分模型**模組, 並將**定型模型**模組連接到它。 然後將測試集 (**分割資料**的正確埠) 新增至**評分模型**。 **評分模型**會進行預測。 您可以選取其輸出埠, 以查看預測和正面類別機率。
1. 新增 [**評估模型**] 模組, 並將評分資料集連接到其左側輸入埠。 若要查看評估結果, 請選取 [**評估模型**] 模組的輸出埠, 然後選取 [**視覺化**]。

以下是完整的實驗圖形:

![實驗的圖形](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>結果

![評估結果](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

在評估結果中, 您可以看到模型的 AUC 是0.776。 在閾值 0.5, 精確度為 0.621, 召回為 0.456, 而 F1 分數為0.526。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索視覺介面可用的其他範例:

- [範例 1-回歸:預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-回歸:比較汽車價格預測的演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 4-分類:預測信用風險 (區分成本)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類:預測流失](ui-sample-classification-predict-churn.md)
- [範例 6-分類:預測航班延誤](ui-sample-classification-predict-flight-delay.md)