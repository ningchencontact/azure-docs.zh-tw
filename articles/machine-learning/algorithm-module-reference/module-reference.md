---
title: 演算法和模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解 Azure Machine Learning 視覺化介面中可用的模組
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 065931140894478caee9d4ea49dac49f2415716b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128645"
---
# <a name="algorithm--module-reference-overview"></a>演算法 & 模組參考總覽

此參考內容會在 Azure Machine Learning 服務的視覺化介面 (預覽) 中提供每個機器學習服務演算法和模組的技術背景。

每個模組都代表一組程式碼, 可以獨立執行並執行機器學習工作 (指定必要的輸入)。 模組可能包含特定的演算法, 或執行在機器學習中很重要的工作, 例如遺漏值取代或統計分析。

> [!TIP]
> 在視覺介面的任何實驗中, 您都可以取得特定模組的相關資訊。 選取模組, 然後在 [**快速協助**] 窗格中選取 [**更多**說明] 連結。

## <a name="modules"></a>模組

模組會依功能組織:

| 功能 | 描述 | 模組 |
| --- |--- | ---- |
| 資料格式轉換 | 在機器學習中使用的各種檔案格式之間轉換資料, | [轉換成 CSV](convert-to-csv.md) |
| 資料輸入和輸出 | 將資料從雲端來源移至您的實驗。 在執行實驗時, 將您的結果或中繼資料寫入 Azure 儲存體、SQL 資料庫或 Hive, 或使用雲端儲存體在實驗之間交換資料。  | [匯入資料](import-data.md)<br/>[匯出資料](export-data.md)<br/>[手動輸入資料](enter-data-manually.md) |
| 資料轉換 | 機器學習服務特有資料的作業, 例如正規化或分類收納資料、特徵選取, 以及維度縮減。| [選取資料集中的資料行](select-columns-in-dataset.md) <br/> [編輯中繼資料](edit-metadata.md) <br/> [清除遺漏的資料](clean-missing-data.md) <br/> [新增資料行](add-columns.md) <br/> [加入資料列](add-rows.md) <br/> [移除重複的資料列](remove-duplicate-rows.md) <br/> [聯結資料](join-data.md) <br/> [分割資料](split-data.md) <br/> [標準化資料](normalize-data.md) <br/> [資料分割和範例](partition-and-sample.md) |
| Python 和 R 模組 | 撰寫程式碼並將它內嵌在模組中, 以整合 Python 和 R 與您的實驗。 | [執行 Python 腳本](execute-python-script.md)   <br/> [建立 Python 模型](create-python-model.md) <br/> [執行 R 腳本](execute-r-script.md)
|  | **機器學習演算法**: | |
| 分類 | 預測類別。  選擇 [從二進位 (雙類別)] 或 [多元] 演算法。| [多元決策樹系](multiclass-decision-forest.md) <br/> [多元促進式決策樹](multiclass-boosted-decision-tree.md) <br/> [多元羅吉斯回歸](multiclass-logistic-regression.md)  <br/> [多元類神經網路](multiclass-neural-network.md)  <br/>  [雙類別羅吉斯回歸](two-class-logistic-regression.md)  <br/>[雙類別平均認知](two-class-averaged-perceptron.md) <br/> [二元推進式&nbsp; &nbsp;判定樹&nbsp;](two-class-boosted-decision-tree.md)  <br/> [雙類別決策樹系](two-class-decision-forest.md)  <br/> [雙類別類神經網路](two-class-neural-network.md)  <br/> [兩&#8209;個&nbsp;類別&nbsp;支援向量&nbsp;機器](two-class-support-vector-machine.md) 
| 叢集 | 將資料群組在一起。| [K-表示群集](k-means-clustering.md)
| 迴歸 | 預測值。 | [線性回歸](linear-regression.md)  <br/> [類神經網路回歸](neural-network-regression.md)  <br/> [決策樹系回歸](decision-forest-regression.md)  <br/> [促進式&nbsp;決策樹&nbsp;回歸&nbsp;](boosted-decision-tree-regression.md)
|  | **建立和評估模型**: | |
| 火車   | 透過演算法執行資料。 | [定型模型](train-model.md)  <br/> [定型群集模型](train-clustering-model.md)    |
| 評估模型 | 測量定型模型的精確度。 |  [評估模型](evaluate-model.md)
| 分數 | 從您剛定型的模型取得預測。 | [套用轉換](apply-transformation.md)<br/>[將&nbsp;資料&nbsp;指派給&nbsp;群集](assign-data-to-clusters.md) <br/>[評分模型](score-model.md)

## <a name="error-messages"></a>錯誤訊息

瞭解在 Azure Machine Learning 服務的視覺化介面中使用模組時, 可能會遇到的[錯誤訊息和例外狀況代碼](machine-learning-module-error-codes.md)。
