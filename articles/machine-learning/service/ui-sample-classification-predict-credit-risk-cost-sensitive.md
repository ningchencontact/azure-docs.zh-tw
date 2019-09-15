---
title: '視覺化介面範例 #4：用來預測信用風險的分類（區分成本）'
titleSuffix: Azure Machine Learning
description: 本文說明如何使用視覺化介面來建立複雜的機器學習實驗。 您將瞭解如何執行自訂 Python 腳本和比較多個模型，以選擇最佳選項。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 977ff101b0f697a48b3e5595834c98fef0f1119a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997034"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>範例 4-分類：預測信用風險（區分成本）

本文說明如何使用視覺化介面來建立複雜的機器學習實驗。 您將瞭解如何使用 Python 腳本來執行自訂邏輯，並比較多個模型來選擇最佳選項。

這個範例會將分類器定型，以使用信用額度記錄、年齡和信用卡號碼等點數應用程式資訊來預測信用風險。 不過，您可以套用本文中的概念來處理您自己的機器學習問題。

如果您只是開始使用機器學習，您可以先查看[基本分類器範例](ui-sample-classification-predict-credit-risk-basic.md)。

以下是此實驗的完成圖形：

[![實驗的圖形](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取範例4實驗的 [**開啟**] 按鈕：

    ![開啟實驗](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

我們會使用來自 UC Irvine 存放庫的德國信用卡資料集。 此資料集包含1000範例，其中含有20個功能和1個標籤。 每個範例都代表一個人。 20個功能包含數值和類別功能。 如需資料集的詳細資訊，請參閱[UCI 網站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最後一個資料行是代表信用風險的標籤，只有兩個可能的值： [高信用風險 = 2] 和 [低信用風險] = 1。

## <a name="experiment-summary"></a>實驗摘要

在此實驗中，我們會比較兩種不同的方法來產生模型，以解決此問題：

- 使用原始資料集進行定型。
- 使用複寫的資料集進行定型。

使用這兩種方法時，我們會使用測試資料集搭配複寫來評估模型，以確保結果與成本函數一致。 我們會使用兩種方法來測試兩個分類器：二元**支援向量機器**和**雙類別促進式決策樹**。

將低風險範例比為 [高] 的成本是1，而將高風險範例比為 [低] 的成本則是5。 我們會使用**執行 Python 腳本**模組來考慮此分類誤判成本。

以下是實驗的圖形：

[![實驗的圖形](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>資料處理

首先，我們會使用 [**中繼資料編輯器**] 模組來加入資料行名稱，以更有意義的名稱取代預設的資料行名稱，並從 UCI 網站上的資料集描述取得。 我們會在 [**中繼資料編輯器**] 的 [**新資料行**名稱] 欄位中，提供新的資料行名稱做為逗號分隔值。

接下來，我們會產生用來開發風險預測模型的定型和測試集。 我們使用**分割資料**模組，將原始資料集分割成相同大小的定型和測試集。 若要建立相同大小的集合，我們會將**第一個輸出資料集選項中的資料列小數**設定為0.5。

### <a name="generate-the-new-dataset"></a>產生新的資料集

因為低估風險的成本很高，所以我們將分類誤判的成本設定如下：

- 針對高風險的案例，分類錯誤為低風險：5
- 針對低風險的案例，分類錯誤為高風險：1

為了反映此成本函數，我們會產生新的資料集。 在新的資料集中，每個高風險範例都會複寫五次，但低風險範例的數目不會變更。 我們會先將資料分割成定型和測試資料集，再進行複寫，以避免在這兩個集合中出現相同的資料列。

為了複寫高風險的資料，我們會將此 Python 程式碼放入**執行 Python 腳本**模組：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

[**執行 Python 腳本**] 模組會複寫定型和測試資料集。

### <a name="feature-engineering"></a>特徵設計

**雙類別支援向量機器**演算法需要正規化資料。 因此，我們使用正規化**資料**模組，將所有數值特徵的範圍標準化，並`tanh`進行轉換。 `tanh`轉換會將所有數值特徵轉換成0和1範圍內的值，同時保留值的整體分佈。

二元**支援向量機器**模組會處理字串功能，將它們轉換成類別特徵，然後再轉換為值為0或1的二進位特徵。 因此，我們不需要將這些功能標準化。

## <a name="models"></a>模型

因為我們會套用兩個分類器、**兩個類別的支援向量機器**（SVM）和**兩個類別**的推進式決策樹，而且也會使用兩個資料集，所以我們總共會產生四個模型：

- 以原始資料定型的 SVM。
- SVM 已使用複寫的資料進行定型。
- 以原始資料定型的推進式決策樹。
- 以複寫的資料定型的推進式決策樹。

我們會使用標準實驗性工作流程來建立、定型和測試模型：

1. 使用**二級支援向量機器**和**二級促進式決策樹**來初始化學習演算法。
1. 使用**訓練模型**將演算法套用至資料，並建立實際的模型。
1. 使用**評分模型**，透過測試範例來產生分數。

下圖顯示此實驗的一部分，其中會使用原始和已複寫的定型集來定型兩個不同的 SVM 模型。 **定型模型**會連接到定型集，而**評分模型**會連接到測試集。

![實驗圖表](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

在實驗的評估階段中，我們會計算四個模型的精確度。 在此實驗中，我們會使用**評估模型**來比較具有相同分類誤判成本的範例。

[**評估模型**] 模組可以計算多達兩個評分模型的效能計量。 因此，我們會使用一個**評估模型**的實例來評估兩個 SVM 模型，以及另一個**評估模型**實例，以評估兩個推進式決策樹模型。

請注意，複寫的測試資料集會當做**評分模型**的輸入使用。 換句話說，最終的精確度分數會包含取得標籤錯誤的成本。

## <a name="combine-multiple-results"></a>合併多個結果

「**評估模型**」模組會產生一個包含各種計量的單一資料列的資料表。 若要建立一組精確度結果，我們會先使用 [**加入資料列**] 將結果合併成單一資料表。 然後，我們會在 [**執行 Python 腳本**] 模組中使用下列 Python 腳本，針對結果資料表中的每個資料列加入模型名稱和定型方法：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>結果

若要查看實驗的結果，您可以用滑鼠右鍵按一下最後一個**選取資料集中的資料行**模組的 [視覺化] 輸出。

![將輸出視覺化](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

第一個資料行列出用來產生模型的機器學習演算法。
第二個數據行表示定型集的類型。
第三個數據行包含成本相關的精確度值。

從這些結果中，您可以看到，以**雙類別支援向量機器**建立的模型所提供的最佳準確度，並已在複寫的訓練資料集上定型。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索視覺介面可用的其他範例：

- [範例 1-回歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 5-分類：預測流失](ui-sample-classification-predict-churn.md)
- [範例 6-分類：預測航班延誤](ui-sample-classification-predict-flight-delay.md)
