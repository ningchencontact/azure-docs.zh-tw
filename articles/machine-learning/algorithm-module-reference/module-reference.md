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
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145143"
---
# <a name="algorithm--module-reference-overview"></a>演算法和模組參考概觀

此參考內容會提供有關每個機器學習演算法和模組可供使用 Azure Machine Learning 服務的視覺化介面 （預覽） 中的技術背景。

每個模組都代表一組可獨立執行，並執行機器學習服務工作，提供必要的輸入程式碼。 模組可能包含特定的演算法，或執行在機器學習服務，例如遺漏值取代或統計分析中很重要的工作。

> [!TIP]
> 中的視覺介面的任何實驗，您可以取得特定模組的相關資訊。 選取的模組，然後選取**更多協助**連結**快速說明**窗格。

## <a name="modules"></a>模組

模組會依功能：

| 功能 | 描述 | 模組 |
| --- |--- | ---- |
| 資料格式轉換 | 在機器學習中使用的各種檔案格式的資料轉換 | [轉換為 CSV](convert-to-csv.md) |
| 資料輸入和輸出 | 將資料從雲端來源移到您的實驗。 Azure 儲存體、 SQL database，或 Hive，撰寫您的結果或中繼資料，同時執行實驗，或使用雲端儲存體來實驗之間交換資料。  | [匯入資料](import-data.md)<br/>[匯出資料](export-data.md)<br/>[手動輸入資料](enter-data-manually.md) |
| 資料轉換 | 是唯一的機器學習服務，例如正規化或分類收納資料、 特徵選取和維度縮減的資料作業。| [選取資料集中的資料行](select-columns-in-dataset.md) <br/> [編輯中繼資料](edit-metadata.md) <br/> [清除遺漏的資料](clean-missing-data.md) <br/> [加入資料行](add-columns.md) <br/> [Add Rows](add-rows.md) <br/> [移除重複的資料列](remove-duplicate-rows.md) <br/> [將資料分割](split-data.md) <br/> [將資料標準化，](normalize-data.md) <br/> [資料分割和取樣](partition-and-sample.md) |
| Python 模組 | 撰寫程式碼，並將它內嵌至整合您的實驗中的 Python 模組中。 | [執行 Python 指令碼](execute-python-script.md)   <br/> [建立 Python 模型](create-python-model.md)
|  | **機器學習服務演算法**: | |
| 分類 | 預測的類別。  選擇從 （雙類別） 的二進位或多級的演算法。| [多元決策樹系](multiclass-decision-forest.md) <br/> [多級羅吉斯迴歸](multiclass-logistic-regression.md)  <br/> [多級類神經網路](multiclass-neural-network.md)  <br/>  [二級羅吉斯迴歸](two-class-logistic-regression.md)  <br/>[二級平均感知器](two-class-averaged-perceptron.md) <br/> [二級&nbsp;促進&nbsp;決策&nbsp;樹狀結構](two-class-boosted-decision-tree.md)  <br/> [二級決策樹系](two-class-decision-forest.md)  <br/> [二級類神經網路](two-class-neural-network.md)  <br/> [兩個&#8209;類別&nbsp;支援&nbsp;向量&nbsp;機器](two-class-support-vector-machine.md) 
| 叢集 | 群組在一起的資料。| [K-means 群集](k-means-clustering.md)
| 迴歸 | 預測的值。 | [線性迴歸](linear-regression.md)  <br/> [類神經網路迴歸](neural-network-regression.md)  <br/> [決策樹系迴歸](decision-forest-regression.md)  <br/> [促進&nbsp;決策&nbsp;樹狀目錄&nbsp;迴歸](boosted-decision-tree-regression.md)
|  | **建置及評估模型**: | |
| 定型   | 透過演算法執行資料。 | [定型模型](train-model.md)  <br/> [定型群集模型](train-clustering-model.md)    |
| 評估模型 | 測量已訓練模型的精確度。 |  [評估模型](evaluate-model.md)
| 分數 | 從剛定型的模型取得預測。 | [套用轉換](apply-transformation.md)<br/>[指派&nbsp;資料&nbsp;到&nbsp;叢集](assign-data-to-clusters.md) <br/>[評分模型](score-model.md)

## <a name="error-messages"></a>錯誤訊息

深入了解[錯誤訊息和例外狀況代碼](machine-learning-module-error-codes.md)您可能會遇到 Azure Machine Learning 服務的視覺化介面中使用模組。
