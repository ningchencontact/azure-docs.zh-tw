---
title: 決策樹系回歸:模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何在 Azure Machine Learning 服務中使用雙類別的平均認知模組, 根據平均認知演算法建立機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f560923b0a5457ac5fd03c7f76fc4315c6ca08e8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128432"
---
# <a name="two-class-averaged-perceptron-module"></a>雙類別平均認知模組

本文描述適用于 Azure Machine Learning 服務的視覺化介面 (預覽) 模組。

使用此模組來建立以平均認知演算法為基礎的機器學習模型。  
  
此分類演算法是一種受監督的學習方法, 而且需要包含標籤資料行的已*標記資料集*。 您可以藉由提供模型和加上標籤的資料集做為[定型模型](./train-model.md)的輸入, 來定型模型。 定型的模型接著可用來預測新輸入範例的值。  

### <a name="about-averaged-perceptron-models"></a>關於平均認知模型

*平均認知方法*是早期和簡單的類神經網路版本。 在此方法中, 輸入會根據線性函式分類成幾個可能的輸出, 然後結合一組衍生自特徵向量的加權, 因此名稱為 "認知"。

較簡單的認知模型適合用來學習線性可分離的模式, 而類神經網路 (尤其是深度神經網路) 則可以模型更複雜的類別界限。 不過, 感知器的速度較快, 因為它們會以序列方式處理案例, 所以感知器可以與連續定型搭配使用。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何設定雙類別的平均認知

1.  將**兩個類別的平均認知**模組新增至您的實驗。  

2.  藉由設定 [**建立定型模式]** 選項, 指定您要如何訓練模型。  
  
    -   **單一參數**:如果您知道要如何設定模型, 請提供一組特定值做為引數。
  
3.  針對 [**學習速率**], 指定*學習速率*的值。 學習速率值可控制每次測試和更正模型時, 用於隨機梯度下降的步驟大小。
  
     藉由讓速率變小, 您可以更頻繁地測試模型, 但有可能會卡在本機高原中的風險。 藉由讓此步驟變大, 您可以更快速地融合, 同時也會有 overshooting 真正 minima 的風險。
  
4.  在 [**反覆運算數目上限**] 中, 輸入您想要演算法檢查定型資料的次數。  
  
     早期停止通常會提供更好的一般化。 增加反復專案數目可改善過度學習的風險。
  
5.  針對 [**亂數字種子**], 選擇性地輸入整數值做為種子。 如果您想要確保跨回合執行實驗的重現性, 建議使用種子。  
  
1.  連接訓練資料集和其中一個訓練課程模組:
  
    -   如果您將 [**建立訓練員模式]** 設定為 [**單一參數**], 請使用 [[定型模型](train-model.md)] 模組。

## <a name="results"></a>結果

定型完成後:

+ 若要查看模型參數的摘要, 以及從定型學習到的特徵權數, 請以滑鼠右鍵按一下 [[定型模型](./train-model.md)] 的輸出。


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 