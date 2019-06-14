---
title: 決策樹系迴歸分析：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的決策樹系迴歸模組，建立根據一整團的決策樹迴歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65442369"
---
# <a name="decision-forest-regression-module"></a>決策樹系迴歸模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來建立根據一整團的決策樹迴歸模型。

設定模型之後，您必須使用定型模型加上標籤的資料集和[訓練模型](./train-model.md)模組。  定型的模型接著可用來進行預測。 

## <a name="how-it-works"></a>運作方式

決策樹會執行一系列簡單的測試，每個執行個體，在達到分葉節點 （決策） 為止，周遊二進位樹狀資料結構的非參數化模型。

決策樹有下列優點：

- 它們是在運算和記憶體使用效率定型和預測期間。

- 它們可以代表非線性決策界限。

- 它們執行整合式的特徵選取和分類，並可彈性呈現雜訊特徵。

此迴歸模型是由一整團的決策樹所組成。 在迴歸的決策樹系中的每個樹狀結構會輸出為預測高斯分佈。 透過尋找模型中的所有樹狀結構的結合分佈最接近高斯分佈的樹狀結構的整體執行彙總。

如需有關這個演算法及其實作的理論架構的詳細資訊，請參閱這篇文章：[決策樹系：統一的架構的分類、 迴歸、 密度估計岐管學習和半監督式的學習](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何設定決策樹系迴歸模型

1. 新增**決策樹系迴歸**實驗的模組。 您可以在介面中尋找模組**Machine Learning**，**初始化模型**，並**迴歸**。

2. 開啟模組屬性，至於**重新取樣方法**，選擇用來建立個別的樹狀結構的方法。  您可以從中**封袋**或是**複寫**。

    - **封袋**:也稱為封袋*啟動程序的彙總*。 迴歸的決策樹系中的每個樹狀結構會將輸出透過預測高斯分佈。 若要尋找其前兩個時間符合分鐘才會指定藉由結合個別樹狀結構所傳回的所有 Gaussians Gaussians 混合的高斯是彙總。

         如需詳細資訊，請參閱維基百科項目[啟動程序的彙總](https://wikipedia.org/wiki/Bootstrap_aggregating)。

    - **複寫**:在複寫中，每個樹狀結構是完全相同的輸入資料上定型。 決定哪個分割的述詞用於每個樹狀節點保持隨機和樹狀結構將會不同。

         如需詳細資訊與訓練程序的相關**複寫**選項，請參閱[Computer Vision 及醫療影像分析的決策樹系。Criminisi 和 J.Shotton。Springer 2013。](https://research.microsoft.com/projects/decisionforests/).

3. 指定您要模型定型，藉由設定的方式**建立定型模式**選項。

    - **單一參數**

      如果您知道您要設定模型的方式，您可以提供一組特定的值做為引數。 您可能已經透過實驗知道這些值，或收到這些指導方針。



4. 針對**決策樹數目**，表示要在集團中建立的決策樹的總數。 藉由建立多個決策樹，您可能會取得較佳的涵蓋範圍，但是定型時間會拉長。

    > [!TIP]
    > 此值也會控制當視覺化定型的模型時，顯示的樹狀結構數目。 如果您想要查看或列印單一樹狀結構，您可以設定的值為 1。不過，這表示該只有一個樹狀結構將會產生 （一組初始參數的樹狀目錄），且會執行任何進一步的反覆項目。

5. 針對**決策樹的最大深度**，輸入數字，以限制任何決策樹的最大深度。 增加樹狀結構的深度可能會提高精確度，風險是有過度配適和拉長定型時間。

6. 針對**的每個節點的隨機分割數目**，輸入建置樹狀結構的每個節點時使用的分割數目。 A*分割*表示樹狀結構 （節點） 的每個層級中的功能會隨機分割。

7. 針對**每個分葉節點範例的最小數目**，表示在樹狀目錄中建立任何終端節點 （分葉） 所需的情況下的最小數目。

     藉由增加此值，您會增加建立新規則的臨界值。 例如，預設值為 1，即使單一案例可能會造成要建立新的規則。 如果您增加到 5 的值時，定型資料必須包含至少五個符合相同條件的案例。


9. 加上標籤的資料集連接中，選取單一標籤的資料行，內含不超過兩個結果，並連接到[定型模型](./train-model.md)。

    - 如果您設定**建立定型模式**選項設定為**單一參數**，使用定型模型[訓練模型](./train-model.md)模組。

   

10. 執行實驗。

### <a name="results"></a>結果

訓練完成之後：

+ 若要查看樹狀目錄中建立的每個反覆項目，以滑鼠右鍵按一下模組的輸出訓練，然後選取**視覺化**。

+ 若要查看每個節點的規則，請按一下每個樹狀結構，並向下切入至分割。

+ 若要儲存的定型模型的快照集，以滑鼠右鍵按一下 訓練模組的輸出，然後選取**儲存為定型的模型**。 此模型的複本不會更新在後續的執行中的實驗。 

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 