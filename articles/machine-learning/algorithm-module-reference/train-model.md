---
title: 定型模型：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用**訓練模型**模組來定型分類或迴歸模型的 Azure Machine Learning 服務中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028106"
---
# <a name="train-model-module"></a>訓練模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來定型分類或迴歸模型。 訓練之後，您已定義的模型，並設定其參數，而且需要加上標籤的資料。 您也可以使用**定型模型**重新定型現有的模型使用新的資料。 

## <a name="how-the-training-process-works"></a>在定型程序的運作方式

Azure Machine Learning 中建立和使用機器學習模型通常是三步驟程序。 

1. 您可以選擇特定類型的演算法，並定義其參數或超參數設定的模型。 選擇任何下列模型類型： 

    + **分類**類神經網路、 決策樹及決策樹系，和其他演算法為基礎的模型。
    + **迴歸**模型，其中可以包含標準的線性迴歸，或使用其他演算法，包括 類神經網路和貝氏迴歸。  

2. 提供的資料集標示為，而且具有與演算法相容的資料。 將資料和模型連接**定型模型**。

    會產生哪些訓練是特定的二進位格式，稱為.ilearner，封裝將資料從中學到的統計模式。 您無法直接修改或讀取此格式;不過，其他模組可以使用此定型的模型。 
    
    您也可以檢視模型的屬性。 如需詳細資訊，請參閱 「 結果 」 區段。

3. 訓練完成之後，使用定型的模型，其中[評分模組](./score-model.md)，以對新的資料進行預測。

## <a name="how-to-use-train-model"></a>如何使用**定型模型**  
  
1.  在 Azure Machine Learning 中設定的分類模型或迴歸模型。
    
2. 新增**定型模型**實驗的模組。  您可以找到此模組下的**Machine Learning**類別目錄。 依序展開**定型**，然後將拖曳**定型模型**到您的實驗的模組。
  
3.  在左側的輸入，將附加的未定型的模式。 將訓練資料集附加至右側的輸入**定型模型**。

    訓練資料集必須包含標籤資料行。 沒有標籤的任何資料列都會被忽略。
  
4.  針對**標籤資料行**，按一下**啟動資料行選取器**，然後選擇 單一資料行，其中包含模型可用於定型的結果。
  
    - 針對分類問題，標籤資料行必須包含**分類**的值或**離散**值。 某些範例可能是/否評等、 疾病分類程式碼或名稱或收入群組。  如果您選擇非類別資料行時，模組會在定型期間傳回錯誤。
  
    -   迴歸問題，標籤資料行必須包含**數值**代表回應變數的資料。 在理想情況下數字的資料代表連續的刻度。 
    
    範例可能是信用風險分數，預計時間失敗的硬碟或呼叫撥接中心的預測的數目在指定的日期或時間。  如果您沒有選擇數值資料行，您可能會發生錯誤。
  
    -   如果您未指定要使用哪一個標籤資料行，Azure Machine Learning 會嘗試推斷哪個是適當的標籤資料行中，使用資料集的中繼資料。 它會取得錯誤的資料行，如果使用的資料行選取器來更正它。
  
    > [!TIP] 
    > 如果您有使用資料行選取器時發生問題，請參閱文章[選取資料集中的資料行](./select-columns-in-dataset.md)的提示。 它會描述一些常見的案例和使用的秘訣**與規則**並**依名稱**選項。
  
5.  執行實驗。 如果您有大量資料時，這可能需要一段時間。

## <a name="bkmk_results"></a> 結果

定型模型之後是：

+ 若要檢視的模型參數和特徵權數，以滑鼠右鍵按一下輸出並選取**視覺化**。
+ 若要使用模型中其他實驗，以滑鼠右鍵按一下模型，然後選取**儲存模型**。 輸入模型的名稱。 

    這會將模型儲存的快照，不會重複執行實驗的更新。
+ 若要使用的模型預測新的值，將其連接到[評分模型](./score-model.md)模組，以及新的輸入資料。


## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 