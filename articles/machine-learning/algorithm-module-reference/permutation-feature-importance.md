---
title: 排列功能重要性：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何在 Azure Machine Learning 服務中使用排列功能重要性模組，以根據定型模型和測試資料集計算特徵變數的排列特徵重要性分數。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517939"
---
# <a name="permutation-feature-importance"></a>排列功能重要性

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的[排列功能重要性](permutation-feature-importance.md)模組，來計算資料集的一組功能重要性分數。 您可以使用這些分數來協助您判斷要在模型中使用的最佳功能。

在此課程模組中，功能值會隨機隨機執行，一次一個資料行，而模型的效能會在之前和之後測量。 您可以選擇所提供的其中一個標準計量來測量效能。

模組傳回的分數代表定型模型在排列後的效能**變更**。 重要功能通常對隨機程式較為敏感，因此會產生較高的重要性分數。 

本文提供在機器學習中排列功能重要性、理論基準和其應用程式的良好一般總覽：[排列功能重要性](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列功能重要性

若要產生一組功能分數，您必須擁有已定型的模型，以及測試資料集。  

1.  將**排列功能重要性**模組新增至您的管線。 您可以在 [**特徵選取**] 類別目錄中找到此模組。 

2.  將定型的模型連接到左側輸入。 模型必須是回歸模型或分類模型。  

3.  在正確的輸入中，串連資料組，最好是與用於定型模型的資料集不同。 此資料集會用來根據定型模型進行評分，並在變更功能值之後評估模型。  

4.  針對 [**隨機種子**]，輸入要用來做為隨機設定之種子的值。 如果您指定0（預設值），則會根據系統時鐘產生一個數位。

     種子值是選擇性的，但如果您想要跨相同管線的執行重現性，則應提供值。  

5.  如需**測量效能的度量**，請選取在排列後計算模型品質時要使用的單一計量。  

     根據您要評估分類或回歸模型，Azure Machine Learning 設計工具支援下列計量：  

    -   **分類**

        精確度、精確度、召回率、平均記錄遺失  

    -   **迴歸**

        精確度、召回率、平均絕對錯誤、根平均平方誤差、相對絕對錯誤、相對平方誤差、判斷係數  

     如需這些評估計量和其計算方式的詳細描述，請參閱[評估模型](evaluate-model.md)。  

6.  執行管道。  

7.  此模組會輸出特徵資料行的清單，以及與它們相關聯的分數，並依分數的順序進行排序。  


##  <a name="technical-notes"></a>技術說明

本節提供有關常見問題的執行詳細資料、秘訣和解答。

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>這與其他特徵選取方法有何不同？

排列功能重要性的運作方式是隨機變更每個特徵資料行的值，一次一個資料行，然後評估模型。 

排列功能重要性所提供的排名通常與您從 [以[篩選器為基礎的特徵選取](filter-based-feature-selection.md)] 所取得的順序不同，這會在建立模型**之前**計算分數。 

這是因為排列功能重要性並不會測量功能與目標值之間的關聯，而是會從模型中的預測來捕捉每項功能的影響程度。
  
## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
