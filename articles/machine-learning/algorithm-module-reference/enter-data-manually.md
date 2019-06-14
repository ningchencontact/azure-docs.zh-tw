---
title: 以手動方式輸入資料：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用手動輸入資料模組在 Azure Machine Learning 服務中建立小型資料集輸入值。 資料集可以有多個資料行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028601"
---
# <a name="enter-data-manually-module"></a>輸入手動資料模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組來建立小型資料集輸入值。 資料集可以有多個資料行。
  
此模組可以在下列情況很有幫助：  
  
- 產生較少的值進行測試  
  
- 建立一份標籤
  
- 輸入要插入資料集內的資料行名稱清單

## <a name="enter-data-manually"></a>手動輸入資料 
  
1.  新增[手動輸入資料](./enter-data-manually.md)模組至您的實驗。 您可以找到此模組中的**資料輸入和輸出**Azure Machine Learning 中的類別。 
  
2.  針對**DataFormat**，選取下列選項之一。 這些選項會決定您所提供的資料剖析的方式。 每一種格式的需求大不相同，因此請務必閱讀相關的主題。  
  
    -   **ARFF**。 屬性關聯檔案格式，由 Weka。   
  
    -   **CSV**。 以逗號分隔值格式。 如需詳細資訊，請參閱 <<c0> [ 轉換為 CSV](./convert-to-csv.md)。  
  
    -   **SVMLight**。 Vowpal Wabbit 和其他機器學習架構所使用的一種格式。  
  
    -   **TSV**。 定位字元分隔值格式。

     如果您選擇的格式，並不會提供符合格式規格的資料，就會發生執行階段錯誤。
  
3.  按一下內部**資料**文字方塊中，若要開始輸入資料。 下列格式需要特別注意：  
  
    - **CSV**:若要建立多個資料行，在以逗號分隔的文字中，貼上或輸入欄位之間使用逗號的多個資料行。
  
        如果您選取**HasHeader**選項時，您可以使用第一個資料列的值做為資料行標題。  
  
        如果您取消選取此選項，資料行名稱、 Col1，Col2，等等，會使用。 您可以新增或變更資料行名稱之後使用[編輯中繼資料](./edit-metadata.md)。  
  
    - **TSV**:若要建立多個資料行，在 tab 分隔的文字中，貼上或輸入使用索引標籤的欄位之間的多個資料行。  
  
        如果您選取**HasHeader**選項時，您可以使用第一個資料列的值做為資料行標題。  
  
        如果您取消選取此選項，資料行名稱、 Col1，Col2，等等，會使用。 您可以新增或變更資料行名稱之後使用[編輯中繼資料](./edit-metadata.md)。  
  
    -   **ARFF**:貼上現有的 ARFF 格式檔案。 如果您正在直接輸入值，請務必資料的開頭加入的選擇性標頭和必要的屬性欄位。 
    
        例如，下列標頭和屬性資料列無法加入簡單的清單。 資料行標題會是`SampleText`。
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**:輸入或貼上使用 SVMLight 格式的值。  
  
        例如，下列範例代表血捐贈資料集，SVMight 格式的前幾行：  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        當您執行[手動輸入資料](./enter-data-manually.md)模組，這幾行轉換為資料行的資料集，且索引值，如下所示：  
  
        |Col1|Col2|Col3|第 4 欄|標籤|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  每個資料列，以開始新的一行之後按 ENTER 鍵。  
  
     **請務必在最後一個資料列之後按 ENTER。** 
     
     如果您按 ENTER 鍵多次，以新增多個空的後端的資料列，最後一個空白資料列會移除修剪過，但其他空的資料列會被視為遺漏的值。  
  
     如果您建立資料列有遺漏值時，您可以一律加以篩選更新版本。  
  
5.  以滑鼠右鍵按一下模組並選取**執行所選**來剖析資料，並將其載入至您的工作區，做為資料集。  
  
     若要檢視資料集，請按一下輸出連接埠，然後選取**視覺化**。  
## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 