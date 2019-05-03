---
title: 促進式的決策樹迴歸：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的促進式決策樹迴歸模組，建立使用促進的迴歸樹集團。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b4ebf1740ec2b0288d8052cb075a61b720b031a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028331"
---
# <a name="boosted-decision-tree-regression-module"></a>促進式的決策樹迴歸模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組來建立使用促進的迴歸樹集團。 *提升*表示每個樹狀結構會相依於先前的樹狀結構。 依符合殘留的樹狀結構，在其前方，學習演算法。 因此，提升決策樹集團中通常會提高精確度，但某些小型風險的涵蓋範圍不足。  
  
這個迴歸方法是一種監督式的學習方法，並因此需要*標示為 資料集*。 標籤資料行必須包含數值。  

> [!NOTE]
> 本單元只適用於使用數值變數的資料集。  

定義模型之後，它使用定型[訓練模型](./train-model.md)。

> [!TIP]
> 想要深入了解所建立的樹狀結構嗎？ 在訓練模型之後，請以滑鼠右鍵按一下的輸出[定型模型](./train-model.md)模組，然後選取**視覺化**查看樹狀目錄中建立的每個反覆項目。 您可以向下鑽研每個樹狀結構的分岔，並查看每個節點的規則。  
  
## <a name="more-about-boosted-regression-trees"></a>深入了解促進式迴歸樹狀結構  

促進 」 也是建立集團模型，以及 封袋、 隨機樹系等傳統方法之一。  在 Azure Machine Learning，促進式的決策樹使用 MART 梯度促進式演算法的有效實作。 梯度促進是機器學習的迴歸問題的技術。 它會建置每個迴歸樹狀結構，按照步驟的方式，來測量每個步驟中的錯誤訊息和更正它，在下一個使用預先定義的損失函式。 因此，預測模型是實際上一整團的較弱預測模型。  
  
在迴歸問題，提升建置一系列促進會以逐步方式，在樹狀結構中，然後選取 使用任意可遺失函式的最佳樹狀結構。  
  
如需詳細資訊，請參閱下列文章：  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    在漸層停駐提升此維基百科文章提供一些背景推進式決策樹。 
  
-  [http://research.microsoft.com/apps/pubs/default.aspx?id=132652](http://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research:從 RankNet 到 LambdaRank 到 LambdaMART:概觀。 By J.C. C.j.c.Burges。

梯度促進式方法也可用在分類問題減少它們具有適當的遺失函式的迴歸。 如需有關用於分類工作的推進式決策的樹實作的詳細資訊，請參閱 <<c0> [ 二級促進式決策樹](./two-class-boosted-decision-tree.md)。  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>如何設定促進式決策樹迴歸

1.  新增**促進式決策樹**模組至您的實驗。 您可以找到此模組下的**Machine Learning**，**初始化**下方**迴歸**類別目錄。 
  
2.  指定您要模型定型，藉由設定的方式**建立定型模式**選項。  
  
    -   **單一參數**:如果您知道您要設定模型，並提供一組特定的值做為引數的方式，請選取此選項。  
   
  
3. **每一個樹狀結構的葉數上限**:表示可由任何樹狀目錄中的終端節點 （分葉） 的數目上限。  

    藉由增加此值，您可能增加樹狀結構的大小，並取得更佳的精確度，風險是過度配適和較長的定型時間。  

4. **每個分葉節點範例的最小數目**:表示在樹狀目錄中建立任何終端節點 （分葉） 所需的案例數目下限。

    藉由增加此值，您會增加建立新規則的臨界值。 例如，預設值為 1，即使單一案例可能會造成要建立新的規則。 如果您增加到 5 的值時，定型資料必須包含至少 5 個符合相同條件的案例。

5. **學習速率**:輸入介於 0 到 1 的定義學習時階梯大小之間的數字。 學習速率決定最佳的解決方案上速度快慢聚合學習模組。 如果階梯大小太大，您可能會衝過最好的解決方案。 如果階梯大小太小，定型會花費較交集的最佳解決方案。

6. **建構的樹狀結構數目**:表示要在集團中建立的決策樹的總數。 藉由建立多個決策樹，您可能會取得較佳的涵蓋範圍，但是定型時間會增加。

    此值也會控制當視覺化定型的模型時，顯示的樹狀結構數目。 如果您想要查看或列印之單一樹狀結構，您可以設定的值為 1。不過，只有一個樹狀結構會產生 （一組初始參數的樹狀目錄），並且會執行任何進一步的反覆項目。

7. **隨機號碼種子**:輸入要當做隨機種子值使用選擇性的非負整數。 指定種子以確保能跨執行具有相同的資料和參數。

    根據預設，隨機種子設為 0，表示從系統時鐘取得初始種子值。
  
8. **允許不明類別層級**:選取此選項可建立未知值的群組中的定型和驗證集。 如果您取消選取此選項時，模型可以接受定型資料中所包含的值。 此模型可能是較不精確的已知值，但它可以提供較佳預測新 （未知） 值。

9. 將訓練資料集和其中一個定型模組：

    - 如果您設定**建立定型模式**選項設定為**單一參數**，使用[定型模型](train-model.md)模組。  
  
    

10. 執行實驗。  
  
### <a name="results"></a>結果

訓練完成之後：

+ 若要查看樹狀目錄中建立的每個反覆項目，以滑鼠右鍵按一下的輸出[定型模型](train-model.md)模組，然後選取**視覺化**。
  
     按一下向下切入至分割，並查看每個節點的規則的每個樹狀結構。  

+ 若要使用模型進行評分，將其連接到[評分模型](./score-model.md)： 預測新輸入範例的值。

+ 若要儲存的定型模型的快照集，以滑鼠右鍵按一下**Trained 模型**輸出的訓練模組，然後選取**另存新檔**。 您將儲存的定型模型的複本不會更新在後續的執行中的實驗。

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 