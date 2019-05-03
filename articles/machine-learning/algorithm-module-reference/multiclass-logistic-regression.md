---
title: 多級羅吉斯迴歸：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務中使用多級羅吉斯迴歸模組，來建立羅吉斯迴歸模型，可用來預測多個值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029321"
---
# <a name="multiclass-logistic-regression-module"></a>多級羅吉斯迴歸模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組建立羅吉斯迴歸模型，可用來預測多個值。

使用羅吉斯迴歸分類是一種監督式的學習方法，並因此需要加上標籤的資料集。 例如做為模組的輸入提供的模型和加上標籤的資料集定型模型[訓練模型](./train-model.md)。 定型的模型可用來預測新輸入範例的值。

Azure Machine Learning 也提供[二級羅吉斯迴歸](./two-class-logistic-regression.md)模組，適合用於二元或二分變數的分類。

## <a name="about-multiclass-logistic-regression"></a>關於多級羅吉斯迴歸

羅吉斯迴歸是知名的方法，可用來預測結果的機率也普遍用於分類工作的統計資料。 演算法會預測有事件發生的機率，羅吉斯函數的資料。 

在 多級羅吉斯迴歸，分類器可用來預測多個結果。

## <a name="configure-a-multiclass-logistic-regression"></a>設定多級羅吉斯迴歸

1. 新增**多級羅吉斯迴歸**實驗的模組。

2. 指定您要模型定型，藉由設定的方式**建立定型模式**選項。

    + **單一參數**:如果您知道您要設定模型，並提供一組特定的值做為引數的方式，請使用此選項。

    + **參數範圍**:如果您不確定最佳參數，而且想要使用參數掃掠，請使用此選項。

3. **最佳化允差**，指定最佳化工具收斂的臨界值。 如果反覆項目之間的改進少於臨界值，此演算法就會停止，並傳回目前的模型。

4. **L1 正則化權數**， **L2 正則化權數**:輸入要用於 L1 與 L2 正則化參數值。 為非零值建議用於兩者。

    Regularization 是防止過度膨脹 penalizing 模型與極端的係數值的方法。 正則化的運作方式是加入的錯誤假設的係數值相關聯的負面影響。 會被扣精確的模型與極端的係數值，但較不精確的模型，使用更保守的值會受到較低的懲罰而。

     L1 與 L2 regularization 有不同的效果，並使用。 L1 可以套用到疏鬆的模型，這在使用高維度的資料時很有用。 相較之下，L2 regularization 是不是疏鬆的資料。  此演算法支援 L1 與 L2 正則化值的線性組合： 也就是說，如果`x = L1`並`y = L2`，`ax + by = c`定義的正則化詞彙的線性範圍。

     不同的 L1 與 L2 詞彙的線性組合已設計出針對羅吉斯迴歸模型，這類[彈性 net 的正則化](https://wikipedia.org/wiki/Elastic_net_regularization)。

6. **隨機號碼種子**:輸入的整數值，如果您想要的結果是可重複執行，做為種子使用的演算法。 否則，會使用系統時鐘的值，做為種子，可能會產生相同實驗執行中的稍有不同結果。

8. 加上標籤的資料集，以及其中一個定型模組連線：

    + 如果您設定**建立定型模式**要**單一參數**，使用[定型模型](./train-model.md)模組。

9. 執行實驗。

## <a name="results"></a>結果

訓練完成之後，您可以看到模型的參數，以及從訓練課程中學到的特徵權數的摘要，以滑鼠右鍵按一下的輸出[定型模型](./train-model.md)模組，然後選取**視覺化**.


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 