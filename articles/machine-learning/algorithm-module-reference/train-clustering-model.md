---
title: 定型群集模型：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何在 Azure Machine Learning 服務中使用訓練叢集模型模組來定型群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: fc6d889fb9be40c98e1a2a0de6fddb29939f3b5d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693658"
---
# <a name="train-clustering-model"></a>定型叢集模型

本文描述適用于 Azure Machine Learning 服務的視覺化介面（預覽）模組。

使用此模組來定型群集模型。

此模組會採用您已經使用[K 意指](k-means-clustering.md)叢集模組設定的未定型叢集模型，並使用加上標籤或未標記的資料集來訓練模型。 此模組會建立可用於預測的定型模型，以及定型資料中每個案例的一組叢集指派。

> [!NOTE]
> 叢集模型無法使用[訓練模型](train-model.md)模組進行定型，這是用來定型機器學習模型的一般模組。 這是因為[定型模型](train-model.md)僅適用于受監督的學習演算法。 K-意指和其他群集演算法允許不受監督學習，這表示演算法可以從未標記的資料中學習。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用定型群集模型  
  
1.  將**訓練叢集模型**模組新增至您在 Studio 中的管線。 您可以在 [**定型**] 分類的 [ **Machine Learning 模組**] 底下找到模組。  
  
2. 新增[K 表示](k-means-clustering.md)叢集模組，或另一個建立相容叢集模型的自訂模組，並設定群集模型的參數。  
    
3.  將訓練資料集附加至**定型群集模型**的右側輸入。
  
5.  在 [資料**行集**] 中，選取要在建立叢集中使用之資料集的資料行。 請務必選取可提供良好功能的資料行：例如，避免使用具有唯一值的識別碼或其他資料行，或具有所有相同值的資料行。

    如果有可用的標籤，您可以使用它做為功能，或將其保留。  
  
6. 如果您想要將定型資料與新的叢集標籤一起輸出，請選取 [**僅針對結果檢查附加] 或 [取消**核取] 選項。

    如果您取消選取此選項，則只會輸出叢集指派。 

7. 執行管線，或按一下 [**定型群集模型**] 模組，然後選取 [**執行選取**]。  
  
### <a name="results"></a>結果

訓練完成之後：


+  若要查看資料集中的值，請以滑鼠右鍵按一下模組，選取 [**結果資料集**]，然後按一下 [**視覺化**]。

+ 若要儲存定型的模型以供稍後重複使用，請以滑鼠右鍵按一下模組，選取 [**定型模型**]，然後按一下 [**另存為定型模型**]。

+ 若要從模型產生分數，請使用 [[將資料指派給群集](assign-data-to-clusters.md)]。



## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 