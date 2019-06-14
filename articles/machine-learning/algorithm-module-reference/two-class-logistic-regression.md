---
title: 二級羅吉斯迴歸分析：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務中使用二級羅吉斯迴歸模組，來建立羅吉斯迴歸模型，可用來預測兩個 （且只有兩個） 的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029246"
---
# <a name="two-class-logistic-regression-module"></a>二級羅吉斯迴歸模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組建立羅吉斯迴歸模型，可用來預測兩個 （且只有兩個） 的結果。 

羅吉斯迴歸是知名的統計技術，可用來模型化許多種類的問題。 此演算法*監督式學習*方法; 因此，您必須提供已經包含定型模型結果的資料集。  

### <a name="about-logistic-regression"></a>有關羅吉斯迴歸  

羅吉斯迴歸是知名的方法，可用來預測結果的機率也特別普遍用於分類工作的統計資料。 演算法會預測有事件發生的機率，羅吉斯函數的資料。
  
這個模組中的分類演算法適合用於二分] 或 [二元變數。 如果您要分類多個結果，請使用[多級羅吉斯迴歸](./multiclass-logistic-regression.md)模組。

##  <a name="how-to-configure"></a>如何設定  

若要定型此模型，您必須提供包含標籤或類別資料行的資料集。 此模組適用於兩個類別的問題，因為標籤或類別資料行必須包含兩個值。 

例如，標籤資料行可能會 [投票] 其可能值為"Yes"或"No"。 或者，它可能是 [信用風險]，其可能值為 「 高 」 或 「 低 」。 
  
1.  新增**二級羅吉斯迴歸**模組至您的實驗。  
  
2.  指定您要模型定型，藉由設定的方式**建立定型模式**選項。  
  
    -   **單一參數**:如果您知道您要設定模型的方式，您可以提供一組特定的值做為引數。  
  
3.  針對**最佳化允差**，指定最佳化模型時所要使用的臨界值。 如果反覆運算之間的改進低於指定的臨界值時，演算法會被視為已聚合為解決方案中，並訓練停止。  
  
4.  針對**L1 正則化權數**並**L2 正則化權數**，輸入要用於 L1 與 L2 正則化參數的值。 為非零值建議用於兩者。  
  
     *正則化*是防止過度膨脹 penalizing 模型與極端的係數值的方法。 正則化的運作方式是加入的錯誤假設的係數值相關聯的負面影響。 因此，會更多，被扣精確的模型與極端的係數值，但較不精確的模型，使用更保守的值會較不懲罰。  
  
     L1 與 L2 regularization 有不同的效果，並使用。  
  
    -   L1 可以套用到疏鬆的模型，這在使用高維度的資料時很有用。  
  
    -   相較之下，L2 regularization 是不是疏鬆的資料。  
  
     此演算法支援 L1 與 L2 正則化值的線性組合： 也就是說，如果<code>x = L1</code>並<code>y = L2</code>，然後<code>ax + by = c</code>定義的正則化詞彙的線性範圍。  
  
    > [!NOTE]
    >  想要深入了解 L1 與 L2 正則化嗎？ 下列文章提供的 L1 與 L2 正則化的不同方式，以及它們如何影響模型調整，利用羅吉斯迴歸和類神經網路模型的程式碼範例的討論：[L1 與 L2 正則化的機器學習服務](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > 羅吉斯迴歸模型的誕生的 L1 與 L2 詞彙的不同線性組合： 例如，[彈性 net 的正則化](https://wikipedia.org/wiki/Elastic_net_regularization)。 我們建議您參考這些組合來定義線性組合在您的模型中有效。
      
5.  針對**L-BFGS 的記憶體大小**，指定要使用的記憶體數量*L-BFGS*最佳化。  
  
     L BFGS 代表 「 有限記憶體 Broyden Fletcher-Goldfarb Shanno 」。 就是最受歡迎的參數估計最佳化演算法。 此參數表示的通過位置和梯度數目來儲存，以計算下一個步驟。  
  
     此最佳化參數限制用來計算下一個步驟和方向的記憶體數量。 當您指定較少的記憶體時，定型會較快，但較不精確。  
  
6.  針對**隨機號碼種子**，輸入整數值。 定義一個種子值是實驗的很重要，如果您想要多次執行相同是實驗的可重現的結果。  
  
  
8. 已加上標籤的資料集的實驗，加上連接的其中一個[訓練單元](module-reference.md)。  
  
    -   如果您設定**建立定型模式**要**單一參數**，使用[定型模型](./train-model.md)模組。  
  
9. 執行實驗。  
  
## <a name="results"></a>結果

訓練完成之後：

+ 若要查看模型的參數，以及從訓練，學習而得的特徵權數的摘要中以滑鼠右鍵按一下的輸出[定型模型](./train-model.md)，然後選取**視覺化**。   
  
+ 若要對新的資料進行預測，做為輸入使用的定型的模型和新的資料[評分模型](./score-model.md)模組。 


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 