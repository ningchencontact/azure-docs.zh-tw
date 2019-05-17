---
title: 分類：預測信用風險 （成本機密）
titleSuffix: Azure Machine Learning service
description: 這篇文章會示範如何建置複雜的機器學習實驗使用視覺化介面。 您將了解如何實作自訂的 Python 指令碼，並比較多個模型以選擇最佳選項。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d714756c19b94eafc40cc0dbeffbc07704e8f94e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787823"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>範例 4-分類：預測信用風險 （成本機密）

這篇文章會示範如何建置複雜的機器學習實驗使用視覺化介面。 您將了解如何實作使用 Python 指令碼的自訂邏輯和比較多個模型以選擇最佳選項。

此範例來定型分類器預測信用風險使用信用應用程式資訊，例如信用記錄、 年齡和信用卡的數目。 不過，您可以套用來處理您自己的機器學習問題的這篇文章中的概念。

如果您剛開始使用 machine learning，您可以看看[基本分類器範例](ui-sample-classification-predict-credit-risk-basic.md)第一次。

以下是完成這項實驗圖形：

[![實驗的圖形](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 選取 **開啟**範例 4 實驗的按鈕：

    ![開啟實驗](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>資料

我們從 UC Irvine 存放庫使用德國信用卡資料集。 此資料集包含 1,000 範例，其中含有 20 個功能和 1 個標籤。 每個範例代表個人。 20 個功能包括數值和分類特徵。 請參閱[UCI 網站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)取得的資料集的詳細資訊。 最後一個資料行是標籤，表示信用風險，且其只有兩個可能的值： 高信用風險 = 2，而低信用風險 = 1。

## <a name="experiment-summary"></a>實驗摘要

在此實驗中，我們會比較兩個不同的方法，來產生模型，以解決此問題：

- 訓練與原始資料集。
- 訓練與複寫的資料集。

利用兩種方法中，我們會使用複寫中的測試資料集，以確保結果會配合此成本函式評估模型。 我們來測試兩個分類器，利用兩種方法：**二級支援向量機器**並**二級促進式的決策樹**。

誤判為高的低風險範例成本為 1，且成本誤判為低風險範例為 5。 我們會使用**執行 Python 指令碼**考量成本此分類誤判的模組。

以下是實驗圖形：

[![實驗的圖形](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>資料處理

我們一開始使用**中繼資料編輯器**從 UCI 網站上的資料集描述取得的模組以新增更有意義的名稱，以取代預設資料行名稱的資料行名稱。 我們提供新的資料行名稱以逗號分隔值作為**新的資料行**的 [名稱] 欄位**中繼資料編輯器**。

接下來，我們會產生定型和測試集用來開發風險預測模型。 我們將原始資料集分割成相同大小的訓練和測試集上使用**分割資料**模組。 若要建立一組相同的大小，我們將設定**的第一個輸出資料集中的資料列比例**為 0.5 的選項。

### <a name="generate-the-new-dataset"></a>產生新的資料集

低估了風險的成本很高，因為我們會設定如下的錯誤分類成本：

- 高風險的情況下被歸類為低風險：5
- 低風險的情況下誤分類為高風險：1

若要反映此成本函式，我們會產生新的資料集。 在新的資料集，每個高風險範例都會複寫五次，但不會變更的低風險範例數目。 我們會將資料分成定型集和測試資料集，以防止相同的資料列在兩個集合的複寫之前。

若要複寫的高風險資料，我們將此 Python 程式碼**執行 Python 指令碼**模組：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**執行 Python 指令碼**模組會將複寫的定型和測試資料集。

### <a name="feature-engineering"></a>特徵設計

**二級支援向量機器**演算法需要標準化的資料。 因此我們會使用**標準化資料**模組來標準化與所有數值特徵的範圍`tanh`轉換。 A`tanh`轉換將所有數值特徵轉換成在 0 和 1 的範圍內的值同時保留值的整體分佈。

**二級支援向量機器**模組會處理字串的功能，將它們轉換成類別的功能，以及再為二進位值是 0 或 1 的功能。 所以我們不需要標準化這些功能。

## <a name="models"></a>模型

因為我們會套用兩個分類器，因此**二級支援向量機器**(SVM) 和**二級促進式決策樹**，也使用兩個資料集，我們會產生四個模型的總計：

- 使用原始的資料訓練 SVM。
- 使用訓練的 SVM 會複寫資料。
- 使用原始的資料訓練促進式的決策樹。
- 使用訓練的促進式的決策樹複寫資料。

我們可以使用標準的實驗性工作流程來建立、 定型和測試的模型：

1. 初始化學習演算法，使用**二級支援向量機器**並**二級促進式決策樹**。
1. 使用**定型模型**將演算法套用至資料，並建立實際的模型。
1. 使用**評分模型**來產生分數，使用測試範例。

下圖顯示此實驗中，在它的原始和複寫的定型集用來定型兩個不同的 SVM 模型的一部分。 **訓練模型**連線至定型集，並**評分模型**連接至測試集。

![實驗圖形](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

在實驗的評估階段中，我們會計算每四個模型的精確度。 此實驗中，我們會使用**評估模型**比較範例具有相同的錯誤分類成本。

**評估模型**模組可以計算最多可達兩個評分模型的效能度量。 讓我們使用的一個執行個體**評估模型**來評估兩個 SVM 模型與另一個執行個體**評估模型**評估兩個的促進式決策樹模型。

請注意，複寫的測試資料集使用做為輸入**評分模型**。 換句話說，最終的精確度分數會包括可用來取得標籤錯誤費用。

## <a name="combine-multiple-results"></a>結合多個結果

**評估模型**模組會產生具有單一資料列，其中包含各種度量的資料表。 若要建立一組傳回精確度結果，我們先使用**加入資料列**結合成單一資料表的結果。 我們接著使用下列中的 Python 指令碼**執行 Python 指令碼**模組以新增的模型名稱和每個資料列的訓練方法的結果資料表中：

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

若要檢視實驗的結果，您可以以滑鼠右鍵按一下視覺化輸出的最後**選取資料集中的資料行**模組。

![將輸出視覺化](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

第一個資料行中列出的機器學習服務演算法用來產生模型。
第二個資料行表示定型集的類型。
第三個資料行包含成本考量精確度值。

從這些結果中，您可以看到與已建立的模型，提供最佳精確度**二級支援向量機器**和複寫的訓練資料集上定型。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

瀏覽可用的視覺介面的其他範例：

- [範例 1-迴歸：預測汽車的價格](ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-迴歸：比較針對汽車價格預測演算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](ui-sample-classification-predict-credit-risk-basic.md)
- [範例 5-分類：預測客戶流失](ui-sample-classification-predict-churn.md)
