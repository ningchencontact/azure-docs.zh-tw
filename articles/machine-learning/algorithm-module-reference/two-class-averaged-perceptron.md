---
title: 決策樹系迴歸分析：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用二級平均感知器模組在 Azure Machine Learning 服務來建立機器學習，根據平均的認知演算法的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029231"
---
# <a name="two-class-averaged-perceptron-module"></a>二級平均感知器模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組來建立機器學習，根據平均的認知演算法的模型。  
  
此分類演算法是一種監督式的學習方法，而且需要*標記的資料集*，其中包括標籤資料行。 您可以藉由提供做為輸入的模型和標記的資料集來定型模型[訓練模型](./train-model.md)。 定型的模型可用來預測新輸入範例中的值。  

### <a name="about-averaged-perceptron-models"></a>關於平均的認知模型

*平均認知方法*是早期和簡單的類神經網路版本。 這種方法，將輸入分類成幾個可能的輸出根據線性函式，並結合一組衍生自特徵向量的權數，因此才稱為 「 認知。 」

更簡單的認知模型適合 learning 線性的可分隔模式，而類神經網路 (尤其是深度類神經網路) 可以模型更複雜的級別界限。 不過，感知器較快速，而且 「 認知器 」 因為它們會循序處理案例，可以用於連續定型。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何設定二級平均感知器

1.  新增**二級平均感知器**模組至您的實驗。  

2.  指定您要模型定型，藉由設定的方式**建立定型模式**選項。  
  
    -   **單一參數**:如果您知道您要設定模型的方式，提供一組特定的值做為引數。
  
3.  針對**學習速率**，為指定值*學習速率*。 學習速率值控制項的大小，用於隨機梯度下降每次測試及更正模型的步驟。
  
     藉由進行較小的速率，您測試模型通常與風險，您可能會卡在高原。 藉由進行較大的步驟，您可以更快收斂，風險階梯，則為 true 的最小值。
  
4.  針對**反覆項目數目上限**，輸入數字的次數中，您想要檢查的訓練資料的演算法。  
  
     早期停止常可帶來較佳的一般化。 反覆項目數目增加時，可以改善配適性，過度配適的風險。
  
5.  針對**隨機號碼種子**，並選擇性地輸入整數值來當做種子使用。 如果您想要確保執行的跨實驗重現性，被建議使用的種子。  
  
1.  連接定型資料集，以及其中一個定型模組：
  
    -   如果您設定**建立定型模式**要**單一參數**，使用[定型模型](train-model.md)模組。

## <a name="results"></a>結果

訓練完成之後：

+ 若要查看模型的參數，以及從訓練，學習而得的特徵權數的摘要中以滑鼠右鍵按一下的輸出[定型模型](./train-model.md)。


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 