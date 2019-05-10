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
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467206"
---
# <a name="module-assign-data-to-clusters"></a>模組：將資料指派給群集

這篇文章說明如何使用*將資料指派給叢集*模組在 Azure Machine Learning 的視覺化介面。 此模組會產生透過使用已定型的群集模型的預測*k-means 群集*演算法。

指派資料叢集模組會傳回包含每個新的資料點的可能指派的資料集。 


## <a name="how-to-use-assign-data-to-clusters"></a>如何使用指派給叢集的資料
  
1. 在 Azure Machine Learning 的視覺化介面，找出先前定型的群集模型。 您可以建立並定型群集模型使用其中一種下列方法：  
  
    - 使用設定 k-means 群集演算法[K-means 群集](k-means-clustering.md)模組和定型模型所使用的資料集和定型群集模型 」 模組 （本文）。  
  
    - 您也可以加入現有的定型群集模型從**儲存模型**群組工作區中。

2. 將定型的模型連接的左側輸入連接埠**將資料指派給叢集**。  

3. 將新的資料集附加做為輸入。 

   在此資料集標籤是選擇性的。 一般而言，叢集是一種非監督式的學習方法。 您不應該事先知道類別。 不過，輸入資料行必須是在定型群集模型中或發生錯誤時所使用的資料行相同。

    > [!TIP]
    > 若要減少寫入從叢集預測介面的資料行數目，請使用[選取資料集中的資料行](select-columns-in-dataset.md)，然後選取資料行的子集。 
    
4. 離開**檢查是否有附加或取消核取，只在結果**核取方塊，選取如果您想要包含完整的輸入資料集，包括顯示結果 （群集指派） 的資料行的結果。
  
    如果您清除此核取方塊，只會傳回結果。 當您建立預測 web 服務的一部分時，此選項可能會很有用。
  
5.  執行實驗。  
  
### <a name="results"></a>結果

+  若要檢視資料集內的值，以滑鼠右鍵按一下模組中，選取**產生的資料集**，然後選取**視覺化**。

