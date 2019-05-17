---
title: 分類：預測信用風險
titleSuffix: Azure Machine Learning service
description: 了解如何建置機器學習的分類器，而不需要撰寫一行程式碼使用視覺化介面。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: f37c945758cfbd03889d79acf764e7f67022267a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789408"
---
# <a name="sample-3---classification-predict-credit-risk"></a>範例 3-分類：預測信用風險

了解如何建置機器學習的分類器，而不需要撰寫一行程式碼使用視覺化介面。 此範例來定型**二元推進式的決策樹**來預測信用風險 （高或低） 會根據信用應用程式資訊，例如信用記錄、 年齡和信用卡的數目。

因為我們想要回答問題"哪一個？ 」 這稱為分類問題。 不過，您可以套用相同的基本程序來處理任何類型的機器學習服務問題，無論是迴歸、 分類、 叢集、 等等。

以下是完成這項實驗圖形：

![實驗的圖形](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取 **開啟**範例 3 實驗的按鈕：

    ![開啟實驗](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>相關的範例

[範例 4-分類：信用風險預測 （成本敏感）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)提供進階的實驗，可以解決這項實驗中相同的問題。 它會顯示如何執行_成本敏感_使用分類**執行 Python 指令碼**模組，並比較兩個二進位的分類演算法的效能。 如果您想要深入了解如何建置分類實驗，請參閱。

## <a name="data"></a>資料

我們從 UC Irvine 存放庫使用德國信用卡資料集。
資料集包含 1,000 範例，其中含有 20 個功能和 1 個標籤。 每個範例代表個人。 這些功能包括數值和分類特徵。 請參閱[UCI 網站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)意義的分類功能。 最後一個資料行是標籤，表示信用風險，且其只有兩個可能的值： 高信用風險 = 2，而低信用風險 = 1。

## <a name="experiment-summary"></a>實驗摘要

我們遵循下列步驟來建立實驗：

1. 德國信用卡 UCI 資料集模組拖曳至實驗畫布。
1. 新增**編輯中繼資料**模組，讓我們可以加入有意義的名稱，每個資料行。
1. 新增**分割資料**模組建立訓練和測試集。 設定為 0.7 的第一個輸出資料集的資料列比例。 此設定指定之模組的左側連接埠的輸出和 rest 的右側連接埠，將會 70%的資料。 我們可以使用左邊資料集進行定型和測試的適合。
1. 新增**二級促進式決策樹**初始化推進式的決策樹分類器的模組。
1. 新增**定型模型**模組。 從上一個步驟的分類器連線的左側輸入連接埠**定型模型**。 將定型集 (左側輸出連接埠**資料分割**) 的右側輸入連接埠**定型模型**。 **訓練模型**將定型分類器。
1. 新增**評分模型**模組並連線**定型模型**給它的模組。 然後加入測試集 (右側連接埠**資料分割**) 來**評分模型**。 **評分模型**會做出預測。 您可以選取它的輸出連接埠，來查看預測和正類別的機率。
1. 新增**評估模型**模組和評分的資料集連接到其左側的輸入連接埠。 若要查看評估結果，請選取 輸出連接埠**評估模型**模組，然後選取**視覺化**。

以下是完整的實驗圖形：

![實驗的圖形](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>結果

![評估結果](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

在評估結果中，您可以看到模型的 AUC 為 0.776。 在 臨界值 0.5，有效位數是 0.621 重新叫用 0.456，，是 F1 分數是 0.526。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

瀏覽可用的視覺介面的其他範例：

- [範例 1-迴歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-迴歸：比較針對汽車價格預測演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 4-分類：預測信用風險 （成本機密）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測客戶流失](ui-sample-classification-predict-churn.md)