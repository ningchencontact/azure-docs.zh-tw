---
title: 轉換成資料集：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的 [轉換成資料集] 模組，將資料輸入轉換成 Microsoft Azure Machine Learning 所使用的內部資料集格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516249"
---
# <a name="convert-to-dataset"></a>轉換成資料集

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的 [[轉換成資料集](convert-to-dataset.md)] 模組，將管線的任何資料轉換成設計工具所使用的內部格式。
  
在大部分的情況下，不需要轉換，因為在資料上執行任何作業時，Azure Machine Learning 會隱含地將資料轉換成其原生資料集格式。 

不過，如果您已在一組資料上執行某種正規化或清除，而且想要確保在進一步的管線中使用這些變更，建議您將資料儲存為資料集格式。  
  
> [!NOTE]
> [轉換成資料集](convert-to-dataset.md)只會變更資料的格式。 它不會將新的資料複本儲存在工作區中。 若要儲存資料集，請按兩下輸出埠，選取 [**另存為資料集**]，然後輸入新的名稱。  
  
## <a name="how-to-use-convert-to-dataset"></a>如何使用轉換成資料集  

我們建議您先使用 [[編輯中繼資料](edit-metadata.md)] 模組來準備資料集，再使用 [[轉換成資料集](convert-to-dataset.md)]。  您可以新增或變更資料行名稱、調整資料類型等等。

1.  將 [[轉換成資料集](convert-to-dataset.md)] 模組新增至您的管線。 您可以在設計工具的 [**資料轉換**] 分類中找到此模組。 

2. 將它連接到輸出資料集的任何模組。   

    只要資料是[表格式](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)的，您就可以將它轉換成資料集。 這包括使用 [匯[入資料](import-data.md)] 載入的資料、[手動使用 [輸入資料](enter-data-manually.md)] 建立的資料，或使用 [套用[轉換](apply-transformation.md)] 轉換的資料集。

3.  在 [**動作**] 下拉式清單中，指出您是否想要在儲存資料集之前對資料進行任何清除：  
  
    - **無**：依其使用資料。  
  
    - **SetMissingValue**：將資料集內的特定值設定為遺漏值。 預設預留位置是問號字元（？），但您可以使用 [**自訂遺漏值**] 選項來輸入不同的值。 例如，如果您為**自訂遺漏值**輸入「計程車」，則資料集中的所有「計程車」都會變更為「遺漏值」。
  
    - **ReplaceValues**：使用此選項來指定要取代為任何其他確切值的單一確切值。  您可以藉由設定 [**取代**方法：-**遺漏**] 來取代遺漏值或自訂值：選擇此選項以取代輸入資料集中的遺漏值。 針對 [**新值**]，輸入要用來取代遺漏值的值。
            - **自訂**：選擇此選項以取代輸入資料集中的自訂值。 針對 [**自訂值**]，輸入您想要尋找的值。 例如，假設您的資料包含用來做為遺漏值預留位置的字串 `obs`，您會輸入 `obs`。 針對 [**新值**]，輸入要用來取代原始字串的新值。
  
    請注意， **ReplaceValues**作業只適用于完全相符的專案。 例如，這些字串不會受到影響： `obs.`、`obsolete`。  
 
  
5.  執行管線，或以滑鼠右鍵按一下 [[轉換成資料集](convert-to-dataset.md)] 模組，然後選取 [**執行選取**的]。  

## <a name="results"></a>結果

+  若要使用新名稱儲存產生的資料集，請以滑鼠右鍵按一下 [[轉換成資料集](convert-to-dataset.md)] 的輸出，然後選取 [**另存為資料集**]。  
  
## <a name="technical-notes"></a>技術說明  

本章節包含執行詳細資料、秘訣和常見問題的解答。

-   任何接受資料集做為輸入的模組也可以接受 CSV 或 TSV 中的資料。 在執行任何模組程式碼之前，會執行輸入的前置處理，這相當於在輸入上執行 [[轉換成資料集](convert-to-dataset.md)] 模組。  
  
-   您無法從 SVMLight 格式轉換為資料集。  
  
-   指定自訂取代作業時，搜尋和取代作業會套用至完整值;不允許部分符合的專案。 例如，您可以將3取代為-1 或使用33，但您不能以兩位數的數位取代3，例如35。  
  
-   針對自訂取代作業，如果您使用做為取代任何不符合資料行之目前資料類型的字元，取代將會無訊息地失敗。  

  
## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
