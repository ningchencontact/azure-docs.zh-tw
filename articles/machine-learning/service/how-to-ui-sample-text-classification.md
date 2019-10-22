---
title: '視覺化介面範例 #7：分類書籍評論'
titleSuffix: Azure Machine Learning
description: 瞭解如何建立機器學習模型將書籍評論分類成不同的類別。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 09/20/2019
ms.openlocfilehash: b61cce54699e86a9a1d2cb526f6dec370baaf26b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694658"
---
# <a name="sample-7---text-classification-predict-company-category"></a>範例 7-文字分類：預測公司類別 

這個範例示範如何使用文字分析模組，在 Azure Machine Learning 視覺化介面中建立文字分類管線。

文字分類的目標是要將某個文字片段指派給一或多個預先定義的類別或類別。 文字片段可以是檔、新聞文章、搜尋查詢、電子郵件、推文、支援票證、客戶意見反應、使用者產品評論等等。文字分類的應用程式包括將報紙文章和新聞內容分類為主題、將網頁組織成階層式類別、篩選垃圾郵件、情感分析、從搜尋查詢預測使用者意圖、路由支援票證，以及分析客戶的意見反應。 

此管線會訓練**多元羅吉斯回歸分類器**，以從維琪百科衍生的維琪百科 SP 500 資料集預測公司類別。  

訓練機器學習模型與文字資料的基本步驟如下：

1. 取得資料

1. 前置處理文字資料

1. 特徵設計

   使用功能解壓縮模組（例如特徵雜湊）將文字功能轉換成數值特徵，從文字資料中解壓縮 n 語法功能。

1. 訓練模型

1. 分數資料集

1. 評估模型

以下是我們將使用之實驗的最後一個完成圖形。 我們將提供所有模組的基本原理，讓您可以自行做出類似的決策。

[實驗的 ![Graph](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>資料

在此實驗中，我們會使用**維琪百科 SP 500**資料集。 此資料集會衍生自維琪百科（根據每個 & P 500 公司的文章 https://www.wikipedia.org/) 。 在上傳至 Azure Machine Learning 視覺化介面之前，資料集的處理方式如下：

- 擷取每家特定公司的文字內容
- 移除 wiki 格式
- 移除非英數字元
- 將所有文字轉換為小寫
- 新增了知名公司類別

找不到某些公司的文章，因此記錄的數目小於500。

## <a name="pre-process-the-text-data"></a>前置處理文字資料

我們使用前置處理**文字**模組來前置處理文字資料，包括偵測句子、token 化的句子等等。 您會在前置處理[**文字**](../algorithm-module-reference/preprocess-text.md)一文中找到所有支援的選項。 在前置處理 tex 的資料之後，我們會使用**分割資料**模組來隨機分割輸入資料，讓訓練資料集包含 50% 的原始資料，而測試資料集包含原始資料的 50%。

## <a name="feature-engineering"></a>特徵設計
在此範例中，我們將使用兩個執行特徵工程的方法。

### <a name="feature-hashing"></a>特性雜湊
我們使用了「[**特徵雜湊**](../algorithm-module-reference/feature-hashing.md)」模組，將文章的純文字轉換成整數，並使用整數值做為模型的輸入特徵。 

**功能雜湊**模組可用來將可變長度的文字檔轉換成相等長度的數值特徵向量，使用 Vowpal Wabbit 程式庫所提供的32位 murmurhash v3 雜湊方法。 使用特徵雜湊的目標是維度縮減;此外，特徵雜湊也會在分類時間更快查閱功能權數，因為它會使用雜湊值比較，而不是字串比較。

在範例實驗中，我們會將雜湊位的數目設定為14，並將 n 字母元數設定為2。 使用這些設定時，雜湊表可以保存 2 ^ 14 個專案，其中每個雜湊特徵代表一或多個 n 語法特徵，而它的值代表文字範例中該 n 語法的出現頻率。 對於許多問題而言，此大小的雜湊表比足夠，但在某些情況下，可能需要更多空間來避免衝突。 使用不同數目的 bits 來評估機器學習解決方案的效能。 

### <a name="extract-n-gram-feature-from-text"></a>從文字中解壓縮 N 語法功能

N-# 語法是指定文字序列中 n 個詞彙的連續序列。 大小1的 n-克稱為一元語法;大小為2的 n 克是 bigram;大小3的 n 克是以三向字母。 N-較大的大小（例如，"四-克"、"五-克" 等等）的值有時會將其稱為。

我們使用[**從文字模塊中解壓縮 N-g 功能**](../algorithm-module-reference/extract-n-gram-features-from-text.md)作為功能工程的另一個解決方案。 此模組會先將 n 字母組的集合解壓縮，而在每個 n-# 格式中出現的檔數目會計算（DF）。 在此範例中，會使用 TF-IDF 計量來計算功能值。 然後，它會將非結構化文字資料轉換成相等長度的數值特徵向量，其中每項功能都代表文字範例中 n 元的 TF-IDF。

將文字資料轉換成數值特徵向量之後，會使用**Select Column**模組從資料集移除文字資料。 

## <a name="train-the-model"></a>訓練模型

您選擇的演算法通常取決於使用案例的需求。 因為此實驗的目標是要預測公司類別，所以多類別分類器模型是不錯的選擇。 考慮到功能的數目很大，而且這些功能是稀疏的，我們會針對此實驗使用**多元羅吉斯回歸**模型。

## <a name="test-evaluate-and-compare"></a>測試、評估和比較

 我們會分割資料集，並使用不同的資料集來定型和測試模型，讓模型的評估更具目標。

在模型定型之後，我們會使用 [**評分模型**] 和 [**評估模型**] 模組來產生預測的結果並評估模型。 不過，在使用「**計分模型**」模組之前，您必須先執行我們在定型期間所做的功能工程設計。 

針對「**特徵雜湊**模組」，您可以輕鬆地對評分流程執行「功能工程師」做為定型流程。 直接使用**特徵雜湊**模組來處理輸入文字資料。

若要**從文字模塊中解壓縮 N 語法功能**，我們會將定型資料流程的**結果詞彙輸出**連接到評分資料流程上的**輸入詞彙**，並將**詞彙模式**參數設定為**ReadOnly**.
[n 語法分數的 ![Graph](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

完成工程步驟之後，您可以使用**計分模型**，利用定型的模型來產生測試資料集的預測。 若要檢查結果，請選取**評分模型**的輸出埠，然後選取 [**視覺化**]。

接著，我們會將分數傳遞至「**評估模型**」模組，以產生評估計量。 [**評估模型**] 有兩個輸入埠，因此我們可以評估和比較使用不同方法所產生的評分資料集。 在此範例中，我們會比較使用特徵雜湊方法和 n 語法方法所產生的結果效能。
若要檢查結果，請選取**評估模型**的輸出埠，然後選取 [**視覺化**]。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索視覺介面可用的其他範例：

- [範例 1-回歸：預測汽車的價格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [範例 3-分類：預測信用風險](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [範例 4-分類：預測信用風險（區分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [範例 5-分類：預測流失](how-to-ui-sample-classification-predict-churn.md)
- [範例 6-分類：預測航班延誤](how-to-ui-sample-classification-predict-flight-delay.md)
