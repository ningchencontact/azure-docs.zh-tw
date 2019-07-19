---
title: 在自動化 ML 中定型精確度計量
titleSuffix: Azure Machine Learning service
description: 瞭解每個執行的自動化機器學習服務精確度計量。
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: dc147fd0252b2b5ec4ce334d6c1c464d9cde8ef5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297904"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>使用計量評估自動化 ML 中的定型準確度

在本文中, 您將瞭解 Azure Machine Learning 中可用於自動化 ml 模型的不同計量。 

有多種方式可查看每次執行反復專案的定型精確度度量。
* 使用[Jupyter widget](how-to-track-experiments.md#view-run-details)
* 在任何`Run`物件上使用[函數`get_metrics()` ](how-to-track-experiments.md#query-run-metrics)
* [在 Azure 入口網站中查看實驗計量](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="prerequisites"></a>先決條件
 
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。
 
* 使用 SDK 或 Azure 入口網站建立自動化機器學習實驗。
 
    * 使用 SDK 來建立[分類模型](how-to-auto-train-remote.md)或[回歸模型](tutorial-auto-train-models.md)
    * 藉由上傳適當的資料, 使用[Azure 入口網站](how-to-create-portal-experiments.md)來建立分類或回歸模型。

## <a name="classification-metrics"></a>分類計量

針對分類工作, 會在每次執行反復專案中儲存下列計量。

|計量|描述|計算|額外的參數
--|--|--|--|
AUC_Macro| AUC 是「接收者作業特性曲線」下方的面積。 Macro 是每個類別 AUC 的算術平均值。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC 是「接收者作業特性曲線」下方的面積。 Micro 是透過將每個類別的真肯定和誤判結合在一起全域計算而得| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC 是「接收者作業特性曲線」下方的面積。 Weighted 是每個類別的分數，以每個類別中 true 執行個體數目為權重加權的算術平均值| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
精確度|精確度是完全符合 true 標籤的預測標籤百分比。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 Macro 是每個類別其平均精確度分數的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 Micro 是透過將每個 cutoff 的真肯定與誤判結合在一起全域計算而得|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 Weighted 是每個類別其平均精確度分數，以每個類別中 true 執行個體的數目為權重加權的的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy 是每個類別其召回率的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 分數是精確度和召回率的調和平均數。 Macro 是每個類別其 F1分數的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 分數是精確度和召回率的調和平均數。 Micro 是透過計算真肯定、誤否定和誤判的總數全域計算而得|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 分數是精確度和召回率的調和平均數。 以每個類別的 F1 分數其類別頻率將平均值加權|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|這是損失函數，用於 (多維度) 羅吉斯迴歸與其擴充功能中，例如類神經網路，如果有機率分類器的預測時，則定義為 true 標籤的負數對數似然比。 對於在 {0,1} 中具有 true 標籤 yt，且預估機率 yp 中 yt = 1 的單一範例，log loss 為 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalized Macro Recall 是正常化的 Macro Recall，因此隨機效能的分數為 0，完美效能的分數為 1。 做法是 norm_macro_recall := (recall_score_macro - R)/(1 - R)，其中 R 是隨機預測其 recall_score_macro 的預期值 (亦即 R=0.5 代表二進位分類，R=(1/C) 代表 C 類別的分類問題)|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro" and then (recall_score_macro - R)/(1 - R)，其中 R 是隨機預測其 recall_score_macro 的預期值 (亦即 R=0.5 代表二進位分類，R=(1/C) 代表 C 類別的分類問題)|
precision_score_macro|Precision 是標示為真正在該類別中特定類別的元素百分比。 Macro 是每個類別其精確度的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision 是標示為真正在該類別中特定類別的元素百分比。 Micro 是透過計算真肯定和誤判的總數全域計算而得|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision 是標示為真正在該類別中特定類別的元素百分比。 Weighted 是每個類別的精確度，以每個類別中 true 執行個體數目為權重加權的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall 是真正在已正確標示的特定類別中的元素百分比。 Macro 是每個類別其召回率的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall 是真正在已正確標示的特定類別中的元素百分比。 Micro 是透過計算真肯定和誤否定的總數全域計算而得|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall 是真正在已正確標示的特定類別中的元素百分比。 Weighted 是每個類別的召回率，以每個類別中 true 執行個體數目為權重加權的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Weighted accuracy 是給予每個範例的權重等於範例其 true 類別中 true 執行個體比例時的精確度|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是一種向量，等於目標中每個元素為該類別的比例|

## <a name="regression-and-forecasting-metrics"></a>迴歸和預測計量

下列計量會儲存在回歸或預測工作的每次執行反復專案中。

|計量|描述|計算|額外的參數
--|--|--|--|
explained_variance|Explained variance 是所給予資料集其變化的數學模型帳戶的比例。 它是原始資料其變異數中減少至錯誤變異數的百分比。 當錯誤的平均值為 0 時，它會等於 Explained variance。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 是與輸出平均值的基線模型相比的確定係數，或平方誤差減少的百分比。 當錯誤的平均值為 0 時，它會等於 Explained variance。|[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Spearman correlation (斯皮爾曼相關性) 是兩個資料集之間關係其單調性的非參數量值。 不同於 Pearson correlation (皮耳森相關性)，Spearman correlation 不假設這兩個資料集為常態分佈。 如同其他的相關係數，此相關係數的變化在 -1 到 +1 之間，其中 0 代表不相關。 -1 或 + 1 的相互關聯表示真正單純的關聯性。 正相關是指隨著 x 增加，y 也會增加。 負相關是指隨著 x 增加，y 會減少。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Mean absolute error (平均絕對誤差) 是目標與預測值之間差異絕對值的預期值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalized mean absolute error (正規化平均絕對誤差) 是平均絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以資料範圍|
median_absolute_error|Median absolute error (中位數絕對誤差) 是目標與預測值之間所有絕對值差異的中位數。 此遺失是強固極端值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalized median absolute error (正規化中位數絕對誤差) 是中位數絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以資料範圍|
root_mean_squared_error|Root mean squared error (均方根誤差) 是目標與預測值之間預期平方差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalized root mean squared error (正規化均方根誤差) 是均方根誤差防以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以資料範圍|
root_mean_squared_log_error|Root mean squared log error (均方根對數誤差) 是預期平方對數誤差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error (正規化均方根對數誤差) 是均方根對數誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以資料範圍|

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Machine Learning 中的[自動化 ml](concept-automated-ml.md) 。