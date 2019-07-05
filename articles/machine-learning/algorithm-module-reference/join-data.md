---
title: 聯結資料：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的聯結資料模組的聯結合併資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518010"
---
# <a name="join-data"></a>聯結資料

這篇文章說明如何使用**聯結資料**模組在 Azure Machine Learning 服務視覺化介面，來合併兩個資料集使用資料庫樣式聯結作業。  

## <a name="how-to-configure-join-data"></a>如何設定聯結的資料

若要在兩個資料集上執行聯結，它們應該相關的索引鍵資料行。 也支援使用多個資料行的複合索引鍵。 

1. 新增您想要結合，然後將拖曳的資料集**聯結資料**模組到您的實驗。 

    您可以找到中的模組**Data Transformation**分類底下**操作**。

1. 連接至資料集**聯結資料**模組。 
 
1. 選取 **啟動資料行選取器**選擇索引鍵資料行。 請務必選擇左邊和右邊輸入資料行。

    單一索引鍵：

    選取這兩個輸入的單一索引鍵資料行。
    
    複合索引鍵：

    選取所有索引鍵的資料行，從左側的輸入和右邊輸入相同的順序。 **聯結資料**符合所有的索引鍵資料行時，模組會聯結資料表。 核取選項**允許重複的項目，並保留選取範圍中的資料行順序**如果資料行順序不是原始的資料表相同。 

    ![資料行選取器](media/module/join-data-column-selector.png)


1. 選取 **大小寫須相符**選項，如果您想要保留的文字資料行聯結的區分大小寫。 
   
1. 使用**聯結類型**下拉式清單，指定應如何結合資料集。  
  
    * **內部聯結**:*內部聯結*是最常見的聯結作業。 只在索引鍵資料行的值相符時，它會傳回合併的資料列。  
  
    * **左方外部聯結**:A*左方外部聯結*傳回聯結左側資料表中的所有資料列的資料列。 當左側資料表中的資料列右側資料表中有不相符的資料列時，傳回的資料列會包含來自右方資料表的所有資料行的遺漏值。 您也可以指定遺漏值的取代值。  
  
    * **完整外部聯結**:A*完整外部聯結*傳回左側資料表中的所有資料列 (**table1**) 與來自右方資料表 (**table2**)。  
  
         針對每個資料表中另有不相符的資料列中的資料列，其結果會包含的資料列包含遺漏值。  
  
    * **左方半聯結**:A*左方半聯結*索引鍵資料行的值相符時，只有值傳回左側資料表中。  

1. 選項**聯結資料表中保留右側索引鍵的資料行**:

    * 選取此選項可檢視這兩個輸入資料表的索引鍵。
    * 取消選取以僅傳回索引鍵資料行左邊輸入中。

1. 執行實驗，或選取 [聯結的資料] 模組，然後選取**選取執行**執行該聯結。

1. 若要檢視結果，以滑鼠右鍵按一下**聯結的資料** > **結果資料集** > **視覺化**。

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 