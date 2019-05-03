---
title: 演算法和模組參考
titleSuffix: Azure Machine Learning service
description: 深入了解在 Azure Machine Learning 的視覺化介面中可用的模組
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029336"
---
# <a name="algorithm--module-reference-overview"></a>演算法和模組參考概觀

此參考內容會提供有關每個機器學習演算法和模組可供使用 Azure Machine Learning 服務的視覺化介面 （預覽） 中的技術背景。 

每個模組都代表一組可獨立執行，並執行機器學習服務工作，提供必要的輸入程式碼。 模組可能包含特定的演算法，或執行在機器學習服務，例如遺漏值取代或統計分析中很重要的工作。 

> [!TIP]
> 中的視覺介面的任何實驗，您可以取得特定模組的相關資訊。 選取的模組，然後選取**更多協助**連結**快速說明**窗格。

模組會依功能：

**資料格式轉換**

  + [轉換為 CSV ](convert-to-csv.md)

**資料輸入和輸出模組**執行到您的實驗，從雲端來源移動資料的工作。 您可以在 Azure 儲存體、 SQL database，或 Hive，撰寫您的結果或中繼資料，同時執行實驗，或使用雲端儲存體來實驗之間交換資料。  

  + [匯入資料](import-data.md)

  + [匯出資料](export-data.md)

  + [手動輸入資料](enter-data-manually.md)


**資料轉換單元**支援都是唯一的機器學習服務，例如正規化或分類收納資料、 特徵選取和縮減維度的資料上的作業。

  + [選取資料集中的資料行](select-columns-in-dataset.md)

  + [編輯中繼資料](edit-metadata.md)

  + [清除遺漏的資料](clean-missing-data.md)

  + [加入資料行](add-columns.md)

  + [Add Rows](add-rows.md)

  + [移除重複的資料列](remove-duplicate-rows.md)

  + [將資料分割](split-data.md)

  + [將資料標準化，](normalize-data.md)

  + [資料分割和取樣](partition-and-sample.md)


**機器學習服務演算法**例如叢集、 支援向量機器或類神經網路，都可以在個別的模組，可讓您自訂機器學習服務工作使用適當的參數。  
  + [評分模型](score-model.md)

  + [將資料指派給群集 ](assign-data-to-clusters.md)

  + [定型模型](train-model.md)

  + [定型群集模型](train-clustering-model.md)

  + [評估模型](evaluate-model.md)

  + [套用轉換](apply-transformation.md)

  + [線性迴歸](linear-regression.md)

  + [類神經網路迴歸](neural-network-regression.md)

  + [決策樹系迴歸](decision-forest-regression.md)

  + [促進式的決策樹迴歸](boosted-decision-tree-regression.md)

  + [二元推進式的決策樹](two-class-boosted-decision-tree.md)

  + [二級羅吉斯迴歸](two-class-logistic-regression.md)

  + [多級羅吉斯迴歸](multiclass-logistic-regression.md)

  + [多級類神經網路](multiclass-neural-network.md)

  + [多元決策樹系](multiclass-decision-forest.md)

  + [二級平均感知器](two-class-averaged-perceptron.md)

  + [二級決策樹系](two-class-decision-forest.md)

  + [二級類神經網路](two-class-neural-network.md)

  + [二級支援向量機器](two-class-support-vector-machine.md)
  
  + [K-means 群集](k-means-clustering.md)


**Python 模組**可讓您更輕鬆地執行自訂函式。 您撰寫的程式碼，並將它內嵌在模組中，將 Python 與實驗的服務。
  + [執行 Python 指令碼](execute-python-script.md)

  + [建立 Python 模型](create-python-model.md)


