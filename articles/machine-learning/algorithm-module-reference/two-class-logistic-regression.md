---
title: 雙類別羅吉斯回歸：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何在 Azure Machine Learning 服務中使用兩個類別的羅吉斯回歸模組來建立羅吉斯回歸模型，以用來預測兩個（而且只有兩個）結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 7020bf529fa635d74959a9dac071aa6e2b134c5b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692613"
---
# <a name="two-class-logistic-regression-module"></a>雙類別羅吉斯回歸模組

本文描述適用于 Azure Machine Learning 服務的視覺化介面（預覽）模組。

您可以使用此模組來建立羅吉斯回歸模型，以用來預測兩個（而且只有兩個）結果。 

羅吉斯回歸是知名的統計技術，可用來建立許多種類問題的模型。 此演算法是一種受*監督的學習*方法; 因此，您必須提供已包含結果的資料集來定型模型。  

### <a name="about-logistic-regression"></a>關於羅吉斯回歸  

羅吉斯回歸是統計資料中知名的方法，用來預測結果的機率，而且特別適合分類工作。 此演算法會藉由將資料與羅吉斯函式進行調整，來預測事件發生的機率。
  
在此課程模組中，分類演算法已針對二分或 binary 變數優化。 如果您需要分類多個結果，請使用[多元羅吉斯回歸](./multiclass-logistic-regression.md)模組。

##  <a name="how-to-configure"></a>如何設定  

若要定型此模型，您必須提供包含標籤或類別資料行的資料集。 因為此模組適用于雙類別的問題，所以標籤或類別資料行必須剛好包含兩個值。 

例如，[標籤] 資料行可能是 [投票]，可能的值為 [是] 或 [否]。 或者，它可能是 [信用風險]，可能的值為「高」或「低」。 
  
1.  將**兩個類別的羅吉斯回歸**模組新增至您的管線。  
  
2.  藉由設定 [**建立定型模式]** 選項，指定您要如何訓練模型。  
  
    -   **單一參數**：如果您知道要如何設定模型，可以提供一組特定值做為引數。  
  
3.  針對 [**優化容錯**]，指定優化模型時要使用的臨界值。 如果反覆運算之間的改進低於指定的臨界值，則會將演算法視為在解決方案上具有交集，而定型會停止。  
  
4.  針對 [ **l1 正規化權數**] 和 [ **l2 正規化權數**]，輸入要用於正規化參數 L1 和 l2 的值。 建議同時使用非零值。  
  
     *正規化*是一種防止過度學習的方法，penalizing 具有極端係數值的模型。 正規化的運作方式是將與係數值相關聯的負面影響加入假設的錯誤中。 因此，具有極端係數值的精確模型會懲罰更多，但具有較保守值的較不精確模型會懲罰較少。  
  
     L1 和 L2 正規化具有不同的效果，並使用。  
  
    -   L1 可以套用至 sparse 模型，這在使用高維度資料時很有用。  
  
    -   相反地，L2 正規化適用于不是稀疏的資料。  
  
     此演算法支援 L1 和 L2 正規化值的線性組合：也就是說，如果 <code>x = L1</code> 和 <code>y = L2</code>，則 <code>ax + by = c</code> 會定義正規化詞彙的線性範圍。  
  
    > [!NOTE]
    >  想要深入瞭解 L1 和 L2 正規化嗎？ 下列文章會討論 L1 和 L2 正規化的差異，以及它們如何影響模型的調整，並提供用於羅吉斯回歸和類神經網路模型的程式碼範例： [Machine Learning 的 l1 和 L2 正規化](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > 已為羅吉斯回歸模型設計了 L1 和 L2 詞彙的不同線性組合：例如，[彈性網路正規化](https://wikipedia.org/wiki/Elastic_net_regularization)。 我們建議您參考這些組合，以定義在您的模型中有效的線性組合。
      
5.  針對**l-BFGS 的記憶體大小**，指定用於*l BFGS*優化的記憶體數量。  
  
     L BFGS 代表「有限的記憶體 Broyden-Fletcher-Goldfarb-Shanno」。 這是常用於參數估計的優化演算法。 此參數表示要儲存的過去位置和漸層數目，以供下一個步驟的計算之用。  
  
     此優化參數會限制用來計算下一個步驟和方向的記憶體數量。 當您指定較少的記憶體時，定型的速度會更快，但較不精確。  
  
6.  針對 [**亂數字種子**]，輸入整數值。 如果您想要在相同管線的多個回合之間重現結果，則定義種子值是很重要的。  
  
  
8. 將加上標籤的資料集新增至管線，並連接其中一個[定型模組](module-reference.md)。  
  
    -   如果您將 [**建立訓練員模式]** 設定為 [**單一參數**]，請使用 [[定型模型](./train-model.md)] 模組。  
  
9. 執行管道。  
  
## <a name="results"></a>結果

定型完成後：

+ 若要查看模型參數的摘要，以及從定型學習到的特徵權數，請以滑鼠右鍵按一下[定型模型](./train-model.md)的輸出，然後選取 [**視覺化**]。   
  
+ 若要對新資料進行預測，請使用定型的模型和新的資料做為[評分模型](./score-model.md)模組的輸入。 


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 