---
title: 選取資料行轉換：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的 [選取資料行轉換] 模組來建立轉換，以選取資料行的相同子集，如同指定的資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: def15b3c28b2278f884b36acc39fa75982fcf8c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516197"
---
# <a name="select-columns-transform"></a>選取資料行轉換

本文說明如何在 Azure Machine Learning 設計工具（預覽）中使用 [**選取資料行轉換**] 模組。 [**選取資料行轉換**] 模組的目的是要確保在下游機器學習作業中一律會使用可預測且一致的資料行集合。

此模組對於需要特定資料行的計分等工作很有説明。 [可用的資料行] 中的變更可能會中斷管線或變更結果。

您可以使用 [**選取資料行] 轉換**來建立及儲存一組資料行。 然後，使用 [套用[轉換](apply-transformation.md)] 模組將這些選項套用至新的資料。

## <a name="how-to-use-select-columns-transform"></a>如何使用選取資料行轉換

此案例假設您想要使用特徵選取來產生一組將用於定型模型的動態資料行。 若要確保評分程式的資料行選取專案相同，您可以使用 [**選取資料行轉換**] 模組來捕捉資料行選取專案，並將其套用至管線中的其他位置。

1. 在設計工具中，將輸入資料集新增至您的管線。

2. 新增以[篩選器為基礎的特徵選取](filter-based-feature-selection.md)實例。

3. 連接模組並設定特徵選取模組，以在輸入資料集中自動尋找一些最佳功能。

4. 新增[定型模型](train-model.md)的實例，並使用以[篩選器為基礎的特徵選取](filter-based-feature-selection.md)輸出作為定型的輸入。

    > [!IMPORTANT]
    > 因為功能重要性是根據資料行中的值決定，所以您無法事先知道哪些欄位可能可用於[定型模型](train-model.md)的輸入。  
5. 現在，附加 [**選取資料行轉換**] 模組的實例。 

    這會產生一個資料行選取專案，做為可儲存或套用至其他資料集的轉換。 此步驟可確保儲存特徵選取所識別的資料行，以供其他模組重複使用。

6. 新增 [[評分模型](score-model.md)] 模組。 

    **請勿連接輸入資料集。**

    相反地，請新增 [套用[轉換](apply-transformation.md)] 模組，並連接 [特徵選取] 轉換的輸出。

   > [!IMPORTANT]
   > 您不能預期會將以[篩選器為基礎的特徵選取](filter-based-feature-selection.md)套用至評分資料集，並取得相同的結果。 由於特徵選取是以值為基礎，因此可能會選擇一組不同的資料行，這會導致評分作業失敗。
7. 執行管道。

儲存並套用資料行選取的這個程式，可確保相同的資料結構描述可用於定型和計分。


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
