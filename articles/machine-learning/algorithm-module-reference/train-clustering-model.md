---
title: 定型群集模型：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的定型群集模型 」 模組來定型群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028076"
---
# <a name="train-clustering-model"></a>定型群集模型

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來定型群集模型。

此模組採用未定型的群集模型，您已經設定使用[K-means 群集](k-means-clustering.md)模組，並定型的模型，以利用加上標籤或未標記的資料集。 此模組會建立兩個已定型的模型，您可以將其用於預測，以及定型資料中每個案例的群集指派一組。

> [!NOTE]
> 群集的模型不會使用定型[定型模型](train-model.md)模組，這是適用於訓練機器學習模型的泛型模組。 這是因為[定型模型](train-model.md)只適用於監督式學習演算法。 K-means 和其他叢集的演算法可讓非監督式的學習，這表示此演算法可深入了解從未標記的資料。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用定型群集模型  
  
1.  新增**定型群集模型**至您的實驗在 Studio 中的模組。 您可以找到下的模組**機器學習服務模組**，請在**訓練**類別目錄。  
  
2. 新增[K-means 群集](k-means-clustering.md)模組或另一個自訂的模組，建立相容的叢集模型，然後設定叢集模型的參數。  
    
3.  將訓練資料集附加至右側的輸入**定型群集模型**。
  
5.  在 **資料行集**，從要用於建立叢集的資料集選取資料行。 請務必選取良好的功能的資料行： 例如，請避免使用識別碼或其他資料行的唯一值或有完全相同的值的資料行。

    如果標籤功能，您可以使用它作為一項功能，或省略它。  
  
6. 選取選項，**檢查是否有附加或取消核取，只在結果**，如果您想要搭配新的叢集標籤將訓練資料輸出。

    如果您取消選取此選項，只有叢集指派為輸出。 

7. 執行實驗，或按一下**定型群集模型**模組，然後選取**選取 執行**。  
  
### <a name="results"></a>結果

訓練完成之後：


+  若要檢視資料集內的值，以滑鼠右鍵按一下模組中，選取**產生的資料集**，然後按一下**視覺化**。

+ 若要儲存供稍後重複使用定型的模型，以滑鼠右鍵按一下模組中，選取**Trained 模型**，然後按一下**儲存為定型的模型**。

+ 若要從模型產生分數，使用[將資料指派給叢集](assign-data-to-clusters.md)。



## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 