---
title: 「一對多」多元分類
titleSuffix: Azure Machine Learning service
description: 瞭解如何在 Azure Machine Learning 服務中使用一對一多元模組，從二元分類模型的集團建立多元分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518160"
---
# <a name="one-vs-all-multiclass"></a>「一對多」多元分類

本文說明如何使用**Vs All 多元**模組 azure Machine Learning 設計工具（預覽），建立可使用「一種與所有」方法來預測多個類別的分類模型。

當結果相依于連續或類別預測變數時，此模組可用於建立預測三個或更多可能結果的模型。 此方法也可讓您針對需要多個輸出類別的問題使用二元分類方法。

### <a name="more-about-one-vs-all-models"></a>深入瞭解一對一與所有模型

雖然某些分類演算法允許設計使用兩個以上的類別，但有些則會將可能的結果限制為兩個值的其中一個（二進位或兩個類別的模型）。 不過，甚至可以使用各種不同的策略，針對多類別分類工作調整二進位分類演算法。 

此模組會執行*一個與所有方法*，其中會為多個輸出類別建立二進位模型。 個別類別的每個二進位模型都會根據其補數（模型中的所有其他類別）進行評估，就好像它是二元分類問題一樣。 接著會執行這些二元分類器，並選擇具有最高信賴分數的預測，藉此進行預測。  

基本上，會建立個別模型的集團，然後合併結果，以建立可預測所有類別的單一模型。 因此，您可以使用任何二元分類器做為一對一模型的基礎。  

 例如，假設您設定了[兩個類別的支援向量機器](two-class-support-vector-machine.md)模型，並提供做為**一對一多元**模組的輸入。 此模組會為輸出類別的所有成員建立兩個類別的支援向量機器模型，然後套用一對一方法以合併所有類別的結果。  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>如何設定一對多分類器  

此模組會建立二元分類模型的集團來分析多個類別。 因此，若要使用此模組，您必須先設定和定型**二元分類**模型。 

接著，您可以將二進位模型連接到**一個 Vs All 多元**模組，並使用[訓練模型](train-model.md)與加上標籤的訓練資料集來定型模型的集團。

當您結合模型時，即使訓練資料集可能會有多個類別值，**一對一的多元**會建立多個二元分類模型、為每個類別優化演算法，然後合併模型。

1. 在設計工具中，將 [**一對一] 和 [全部] 多元**新增至您的管線。 您可以在 [**分類**] 分類中的 [Machine Learning-初始化] 下找到此模組。

    **一對一多元**分類器沒有自己的可設定參數。 任何自訂都必須在提供做為輸入的二元分類模型中完成。

2. 將二元分類模型加入至管線，並設定該模型。 例如，您可能會使用[兩個類別的支援向量機器](two-class-support-vector-machine.md)或[兩個類別](two-class-boosted-decision-tree.md)的推進式決策樹。

3. 將[訓練模型](train-model.md)模組新增至您的管線，並連接未定型的分類器，也就是**一個 Vs-All 多元**的輸出。

4. 在 [[定型模型](train-model.md)] 的其他輸入上，連接具有多個類別值的加上標籤的訓練資料集。

5. 執行管道。

## <a name="results"></a>結果

定型完成之後，您可以使用模型來進行多元預測。

或者，您也可以將未定型的分類器傳遞至[交叉驗證模型](cross-validate-model.md)，以對照已加上標籤的驗證資料集進行交叉驗證。


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
