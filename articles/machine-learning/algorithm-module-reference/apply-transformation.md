---
title: 套用轉換：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務中使用套用轉換模組，以修改根據先前計算的轉換輸入資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028706"
---
# <a name="apply-transformation-module"></a>套用轉換模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來修改根據先前計算的轉換輸入資料集。  
  
例如，如果您用來將定型資料正規化所使用的 z 分數**標準化資料**模組，您會想要用於評分 」 階段期間的訓練中的 z 分數值所計算出。 在 Azure Machine Learning，您可以將正規化方法儲存為轉換，接著再使用**套用轉換**評分前套用至輸入資料的 z 分數。
  
Azure Machine Learning 可讓您建立並將許多不同種類的自訂轉換。 例如，您可能要儲存，然後重複使用的轉換：  
  
- 移除或取代遺漏值，使用**清除遺漏的資料**
- 標準化資料，使用**標準化資料**
  

## <a name="how-to-use-apply-transformation"></a>如何使用 套用轉換  
  
1. 新增**套用轉換**模組至您的實驗。 您可以找到此模組下的**Machine Learning**，請在**分數**類別目錄。 
  
2. 找出要當做輸入使用現有的轉換。  先前儲存的轉換可在**轉換**左側的導覽窗格中的群組。  
  
   
  
3. 連接您想要轉換的資料集。 資料集應該有完全相同的結構描述 （資料行、 資料行名稱、 資料類型的數字），轉換的第一次設計的資料集。  
  
4. 任何其他參數不必須設定，因為所有自訂都會在定義轉換時都完成。  
  
5. 若要將轉換套用至新的資料集，執行實驗。  

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 