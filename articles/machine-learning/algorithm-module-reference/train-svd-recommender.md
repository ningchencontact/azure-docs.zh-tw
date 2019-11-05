---
title: 定型 SVD 推薦：模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的 [定型 SVD 推薦] 模組，使用 SVD 演算法來定型貝氏推薦。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515664"
---
# <a name="train-svd-recommender"></a>訓練 SVD 推薦

本文說明如何在 Azure Machine Learning 設計工具（預覽）中使用 [**定型 SVD 推薦**] 模組。 您可以使用此模組，根據**SVD** （單一值分解）演算法來定型建議模型。  

[**定型 SVD 推薦**] 模組會讀取使用者-專案-評等三合一的資料集。 它會傳回定型的 SVD 推薦。 接著，您可以使用[計分 SVD 推薦](score-svd-recommender.md)模組，透過定型的模型來預測評等或產生建議。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>深入瞭解建議模型和 SVD 推薦  

建議系統的主要目的是建議系統*使用者*的一個或多個*專案*。 專案的範例可以是電影、餐廳、書籍或歌曲。 使用者可以是個人、人員群組或具有專案喜好設定的其他實體。  

有兩種主要的方法可推薦系統。 

+ 第一種是以**內容為基礎**的方法，它會利用使用者和專案的功能。 使用者可能會以「年齡」和「性別」等屬性來描述，而專案可能會由「作者」和「製造商」等屬性來描述。 以內容為基礎的建議系統的一般範例可以在社交配對網站上找到。 
+ 第二種方法是共同作業**篩選**，其只使用使用者和專案的識別碼，並從使用者提供給專案的評等（稀疏）矩陣取得這些實體的隱含資訊。 我們可以從已評等的專案和其他已分級相同專案的使用者，瞭解使用者。  

SVD 推薦會使用使用者的識別碼和專案，以及使用者提供給專案的評等矩陣。 這是共同作業的**推薦**。 

如需有關 SVD 推薦的詳細資訊，請參閱相關的研究論文：[推薦系統的矩陣分解技術](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何設定定型 SVD 推薦  

+ [準備定型資料](#prepare-data)
+ [訓練模型](#train-the-model)

### <a name="prepare-data"></a>準備資料

在嘗試使用模組之前，您必須先以建議模型所需的格式來表示您的資料。 需要**使用者-專案-評等三**合一的訓練資料集。

#### <a name="required-dataset-of-user-item-ratings"></a>使用者-專案-評等的必要資料集

定型所使用的輸入資料必須包含正確的格式資料，這點很重要： 

+ 第一個資料行必須包含使用者識別碼。
+ 第二個數據行必須包含專案識別碼。
+ 第三個數據行包含使用者-專案組的評等。 評等值必須是數數值型別。  

                                                                                                                                                                                                           

Azure Machine Learning 設計工具中的**餐廳分級**資料集（按一下 [**已儲存的資料集**] 和 [**範例**]）會示範預期的格式：

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

從這個範例中，您可以看到單一使用者已分級兩個不同的餐廳。 

### <a name="train-the-model"></a>訓練模型

1.  將**訓練 SVD 推薦**模組新增至您在設計工具中的管線，並將其連接至定型資料。  
   
2.  針對 [**因素數目**]，輸入指定要與推薦搭配使用之因數數目的數位。  
    
    每個因素都會測量使用者與專案相關的數量。 因數數目也是潛在因數空間的維度。 隨著使用者和專案數目的增加，設定較多的因素會比較好。 不過，如果數位太大，效能可能會降低。
    
3.  **建議演算法反復專案的數目**，指出演算法應該處理輸入資料的次數。 這個數位愈高，預測就越精確;不過，定型的速度較慢。 預設值為30。

4.  針對 [**學習速率**]，輸入介於0.0 和2.0 之間的數位，以定義學習時的步驟大小。

    學習速率會決定每個反復專案所採取的步驟大小。 如果步驟大小太大，您可能會下限最佳的解決方案。 如果步驟大小太小，訓練會花更長的時間在最佳解決方案上融合。 
  
5.  執行管道。  


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
