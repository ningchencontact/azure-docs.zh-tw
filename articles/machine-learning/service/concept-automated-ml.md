---
title: 選取與微調自動化 ML 演算法
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務如何自動為您挑選演算法，以及如何藉由使用您提供來為模型選取最佳演算法的參數與準則，從其中產生模型來節省您的時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f00a530cb4e62ae83dea349c949e8f2ddc43c0c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203021"
---
# <a name="what-is-automated-machine-learning"></a>什麼是自動化機器學習服務？

自動化的機器學習服務，也稱為自動化 ML，是機器的自動化學習服務模型開發耗時、 重複工作的程序。 它可讓資料科學家、 分析師和開發人員建置具有高擴充能力、 效率和產能的 ML 模型，同時維持模型品質。

傳統的機器學習服務模型開發是耗費資源，需要大量的領域知識和產生，並比較數十個模型的時間。 當您想要 Azure Machine Learning 來訓練及調整模型，讓您使用您指定的目標計量時，適用於自動化的 ML。 服務然後逐一查看與 功能選取項目，其中每個反覆項目會產生的模型訓練分數的配對的 ML 演算法。 較高分數越好模型會被視為 「 符合 」 您的資料。

使用自動化的機器學習，您將會加速取得絕佳的簡便又有效率的生產就緒 ML 模型所花費的時間。

## <a name="when-to-use-automated-ml"></a>使用自動化的 ML 的時機

自動化的 ML 來普及化的機器學習服務模型開發程序，並讓其使用者，無論其資料科學專業知識，來識別問題的端對端機器學習管線。

資料科學家、 分析師和產業的開發人員可以使用自動化的 ML 以：

+ 實作廣泛的程式設計不知情的情況下的機器學習解決方案
+ 節省時間和資源
+ 運用資料科學的最佳作法
+ 提供敏捷式軟體開發解決問題

## <a name="how-automated-ml-works"></a>如何自動化的 ML 運作方式

使用**Azure Machine Learning 服務**，您可以設計和執行自動化的 ML 訓練實驗進行下列步驟：

1. **找出 ML 問題**解決： 分類、 預測，或迴歸

1. **指定的來源和格式，加上標籤的定型資料的**:Numpy 陣列或 Pandas 資料框架

1. **設定計算目標，以進行模型定型**，例如您[本機電腦、 Azure Machine Learning 計算、 遠端 Vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  深入了解自動化訓練[上的遠端資源](how-to-auto-train-remote.md)。

1. **設定自動化的 machine learning 參數**，以決定多少反覆項目，透過不同的模型，超參數設定進階前置處理/功能，以及要判斷最佳的模型時，看看哪些計量。  您可以設定為自動的訓練實驗[在 Azure 入口網站](how-to-create-portal-experiments.md)或是[sdk](how-to-configure-auto-train.md)。

1. **提交定型執行。**

  ![自動化的機器學習服務](./media/how-to-automated-ml/automl-concept-diagram2.png)

在訓練期間，Azure Machine Learning 服務會建立數個平行的管線，請嘗試不同的演算法和參數中。 一旦達到在實驗中定義的允出準則，它會停止。

您也可以檢查所記錄的執行的資訊，其中包含的執行期間所收集的計量。 定型執行會產生序列化的 Python 物件 (`.pkl`檔案)，其中包含模型和資料前置處理。

雖然已自動化模型建置，您也可以[了解如何重要或相關的功能是](how-to-configure-auto-train.md#explain)產生模型。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="time-series-forecasting"></a>時間序列預測
建置預測是所有企業不可或缺的一部分，不論是平均營收、 庫存、 銷售或客戶的需求。 自動化 ml 使用結合的技術和方法，建議您高品質時間序列預測的數目。 自動化 ml 中的時間序列實驗會被視為多變量迴歸問題。 過去的時間序列值 「 樞紐 」 成為其他預測值與迴歸輸入變數的更多的維度。 

這個方法時，不同於傳統的時間序列方法，好處是自然併入多個內容變數和其在訓練期間之間的關聯性。 在真實世界預測應用程式中，多個因素會影響預測。 例如，當預測銷售，互動的歷史趨勢、 貨幣匯率和價格所有共同磁碟機的銷售結果。 進一步的優點是迴歸模型中的所有最新創新立即套用至預測。

如何遙遠的未來預測應該擴充 （預測時間範圍） 是基本的預測規格的一部分。 設定的必要的參數`max_horizon`在實驗中會定義幾個單位期間，（根據定型資料，例如每月、 每週 forecaster microsoft frx 應該預測出的時間間隔。 

自動化的 ML 學習單一的但通常在內部分支模型的資料集和預測的視野中的所有項目。 詳細資料可因此估計模型參數，並看不見的系列的一般化可能實現。 

從定型資料中擷取功能扮演重要角色。 自動化的 ML 執行標準處理前的步驟，並產生時間序列的其他功能 （例如年、 月、 日一週等等） 來擷取季節性效果，並最大化的預測精確度。 

如果適用於您的案例，您可以直接使用自動化來建立延隔的 ML (`target_lags`) 或輪流視窗彙總的資料 (`target_rolling_window_size`) 從您的目標 (`y_value`) 過去的值。 

## <a name="preprocessing"></a>前置處理

在每個自動化的機器學習實驗，您的資料會經過前置處理使用的預設方法，並選擇性地透過進階前置處理。

### <a name="automatic-preprocessing-standard"></a>自動前置處理 （標準）

在每個自動化的機器學習實驗，您的資料在自動調整或正規化為協助佳的演算法。  在模型定型期間調整或正規化的下列技術的其中一個會套用至每個模型。

|縮放比例&nbsp;&&nbsp;正規化| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 標準化功能藉由移除平均數及調整至單位變異數  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 調整每個功能，由該資料行的最小值和最大值來轉換功能  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |調整每項功能，其最大值的絕對值 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |這個的 Scaler 功能由其分量範圍 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |投射至較低維度空間中使用之資料的奇異值分解的線性維度縮減 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |這個轉換器會透過已截斷的奇異值分解 (SVD) 執行線性維度縮減。 PCA，與此估計工具不會計算奇異值分解之前置資料。 這表示它可以有效率地使用 scipy.sparse 矩陣 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 使用至少一個非零元件的每個範例 （亦即，資料矩陣的每個資料列） 是獨立於其他樣本重新調整，以便其範數 （l1 或 l2） 等於 1 |

### <a name="advanced-preprocessing-optional-featurization"></a>進階前置處理： 選擇性的功能

其他進階前置處理和功能也會是可用的資源，例如遺失值插補、 編碼及轉換項目。 [深入了解哪些功能就會包含](how-to-create-portal-experiments.md#preprocess)。 啟用此設定必須搭配：

+ Azure 入口網站：選取**Preprocess**核取方塊**進階設定**[進行這些步驟](how-to-create-portal-experiments.md)。

+ Python SDK︰指定`"preprocess": True`for [ `AutoMLConfig`類別](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)。

## <a name="ensemble-models"></a>集團模型

您可以訓練集團模型，使用自動化的機器學習服務[Caruana 集團選取演算法已排序的集團初始化](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)。 集團學習藉由合併多個模型，而不是使用單一模型，改善機器學習結果和預測的效能。 集團反覆項目會顯示為您執行的最後一個反覆項目。

## <a name="training-metric-output"></a>訓練計量輸出

有多種方式可以檢視針對每個執行的反覆項目訓練精確度標準。

* 使用 Jupyter 小工具。
* 使用`get_metrics()`函式，任何`Run`物件。
* 在您的實驗中的 Azure 入口網站中檢視的計量。

### <a name="classification-metrics"></a>分類計量

下列度量資訊會儲存在分類工作的每個執行反覆項目。

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

### <a name="regression-and-forecasting-metrics"></a>迴歸和預測計量

下列度量資訊會儲存在迴歸或預測工作的每個執行反覆項目。

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


## <a name="use-with-onnx-in-c-apps"></a>與 ONNX 中搭配使用C#應用程式

使用 Azure Machine Learning，您可以使用自動化的 ML 來建置 Python 模型，並將它轉換成 ONNX 格式。 ONNX 執行階段會支援C#，因此您可以使用模型中自動建置您C#應用程式，而不需要重新編碼或任何 REST 端點導入的網路延遲。 請嘗試此流程的範例[此 Jupyter notebook 中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)。

## <a name="automated-ml-across-microsoft"></a>Microsoft 自動化的 ML

自動化的 ML 中也會提供其他 Microsoft 解決方案這類：

|整合|描述|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|自動的模型選擇和定型 ML.NET 中使用 Visual Studio 和 Visual Studio Code 的.NET 應用程式中自動化 ML （預覽）。|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|相應放大您在 HDInsight 叢集中，以平行方式在 Spark 上自動化 ML 訓練作業。|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|叫用直接在 Power BI （預覽） 中的機器學習服務模型。|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|建立新的機器學習服務模型，對您在 SQL Server 2019 巨量資料叢集中的資料。|

## <a name="next-steps"></a>後續步驟

查看範例並了解如何使用自動化機器學習服務來建置模型：

+ 請依照下列[教學課程：使用自動化的 Azure Machine Learning 自動將分類模型定型](tutorial-auto-train-models.md)

+ 設定自動的訓練實驗的設定：
  + 在 Azure 入口網站介面中，[使用下列步驟](how-to-create-portal-experiments.md)。
  + 使用 Python SDK 中，[使用下列步驟](how-to-configure-auto-train.md)。

+ 了解如何自動使用時間序列資料，定型[使用下列步驟](how-to-auto-train-forecast.md)。

+ 試試看[Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
