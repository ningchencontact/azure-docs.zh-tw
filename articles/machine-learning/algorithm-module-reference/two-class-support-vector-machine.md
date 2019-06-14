---
title: 二級支援向量機器：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用**二級支援向量機器**模組在 Azure Machine Learning 服務來建立支援向量機器演算法為基礎的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027926"
---
# <a name="two-class-support-vector-machine-module"></a>二級支援向量機器模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來建立支援向量機器演算法為基礎的模型。 

支援向量機器 (Svm) 是透徹的監督式的學習方法。 這個特定的實作適合用於預測的兩個可能的結果，根據連續或類別的變數。

定義模型參數之後，請使用訓練單元，並提供訓練模型*標記的資料集*包含標籤或結果資料行。

## <a name="about-support-vector-machines"></a>關於支援向量機器

支援向量機器是其中一個最早的機器學習演算法，並已使用 SVM 模型在許多應用程式，從資訊擷取到文字和影像分類。 Svm 可以用於分類和迴歸工作。

此 SVM 模型是一種監督式的學習的模型，需要加上標籤的資料。 在訓練過程中，演算法會分析輸入的資料，並辨識模式中呼叫的多維度的特徵空間*超平面*。  所有的輸入的範例會表示為在此空間中的點，以及對應至輸出方式分類為整個除以，並清除間距，以盡可能的類別。

預測中，SVM 演算法會將新的範例指派到其中一個類別另一個，將它們對應到該相同的空間。 

## <a name="how-to-configure"></a>如何設定 

此模型類型，建議您將資料集正規化再用來定型分類器。
  
1.  新增**二級支援向量機器**模組至您的實驗。  
  
2.  指定您要模型定型，藉由設定的方式**建立定型模式**選項。  
  
    -   **單一參數**:如果您知道您要設定模型的方式，您可以提供一組特定的值做為引數。  

3.  針對**反覆項目數**，輸入一個數字，代表數字，建立模型時所使用的反覆運算。  
  
     此參數可用來控制定型速度和精確性之間的取捨。  
  
4.  針對**Lambda**，輸入要用於 L1 正則化權數的值。  
  
     這個正則化係數可用來微調模型。 較大的值會造成更複雜的模型。  
  
5.  選取選項，**正規化特徵**，如果您想要將正規化特徵再進行定型。
  
     如果您套用正規化，定型之前，資料點會置於平均值，並調整到有一個單位的標準差。
  
6.  選取選項，**專案，以單位球面**來正規化係數。
  
     投射到空間單位的值，表示定型之前，資料點，會在 0 置中和在調整到有一個單位的標準差。
  
7.  在 **隨機號碼種子**，鍵入整數值，以做為種子，如果您想要確保在執行之間的重現性。  否則，會使用系統時鐘的值，做為種子，這可能會導致在執行之間的稍有不同的結果。
  
9. 連接加上標籤的資料集，以及其中一個[訓練單元](module-reference.md):
  
    -   如果您設定**建立定型模式**要**單一參數**，使用[定型模型](train-model.md)模組。
  

10. 執行實驗。

## <a name="results"></a>結果

訓練完成之後：

+ 若要查看模型的參數，以及從訓練，學習而得的特徵權數的摘要中以滑鼠右鍵按一下的輸出[定型模型](./train-model.md)，然後選取**視覺化**。

+ 若要使用定型的模型進行預測，連線為定型的模型以[評分模型](score-model.md)模組。


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 