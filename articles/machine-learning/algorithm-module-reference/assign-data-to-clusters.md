---
title: 將資料指派至叢集：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的叢集模組來指派資料評分群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028766"
---
# <a name="assign-data-to-clusters"></a>將資料指派給群集

這篇文章說明如何使用[將資料指派給叢集](assign-data-to-clusters.md)視覺化介面，來產生預測使用已使用 K-means 群集演算法來定型群集模型中的模組。

此模組會傳回包含每個新的資料點的可能指派的資料集。 


## <a name="how-to-use-assign-data-to-clusters"></a>如何使用指派給叢集的資料
  
1.  在視覺化介面，找出先前定型的群集模型。 您可以建立並定型群集模型使用其中一種方法：  
  
    - 設定 k-means 演算法使用[K-means 群集](k-means-clustering.md)模組，然後定型模型使用的資料集並[定型群集模型](train-clustering-model.md)模組。  
  
  
    您也可以加入現有的定型群集模型從**儲存模型**群組工作區中。

2. 將定型的模型連接的左側輸入連接埠[將資料指派給叢集](assign-data-to-clusters.md)。  

3. 將新的資料集附加做為輸入。 在此資料集標籤是選擇性的。 一般而言，叢集是一種非監督式的學習方法因此應該不會事先知道類別。

    不過，輸入資料行必須是在定型群集模型中或發生錯誤時所使用的資料行相同。

    > [!TIP]
    > 若要從叢集預測減少輸出資料行的數目，請使用[選取資料集中的資料行](select-columns-in-dataset.md)，然後選取資料行的子集。 
    
4. 將選項保留**檢查是否有附加或取消核取，只在結果**選取如果您想要包含完整的輸入資料集，以及指出結果 （群集指派） 的資料行的結果。
  
    如果您取消選取此選項，您得到的結果。 建立預測 web 服務的一部分時，這可能是很有用。
  
5.  執行實驗。  
  
### <a name="results"></a>結果

 
+  若要檢視資料集內的值，以滑鼠右鍵按一下模組中，選取**產生的資料集**，然後按一下**視覺化**。

