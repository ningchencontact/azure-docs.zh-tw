---
title: 什麼是自動化 ML / automl
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務如何自動為您挑選演算法，以及如何藉由使用您提供來為模型選取最佳演算法的參數與準則，從其中產生模型來節省您的時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: 137ef1ad78548053f3c5b8f30b7d83f2370f62da
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442425"
---
# <a name="what-is-automated-machine-learning"></a>什麼是自動化機器學習服務？

自動化的機器學習服務，也稱為 autoML，是機器的自動化學習服務模型開發耗時、 重複工作的程序。 它可讓資料科學家、 分析師和開發人員建置具有高擴充能力、 效率和產能的 ML 模型，同時維持模型品質。

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

您也可以檢查所記錄的執行的資訊，其中[包含計量](how-to-understand-accuracy-metrics.md)執行期間所收集。 定型執行會產生序列化的 Python 物件 (`.pkl`檔案)，其中包含模型和資料前置處理。

雖然已自動化模型建置，您也可以[了解如何重要或相關的功能是](how-to-configure-auto-train.md#explain)產生模型。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

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


## <a name="time-series-forecasting"></a>時間序列預測
建置預測是所有企業不可或缺的一部分，不論是平均營收、 庫存、 銷售或客戶的需求。 您可以使用自動化的 ML 結合技術和方法，並取得建議的高品質時間序列預測。 

自動化的時間序列實驗會被視為多變量迴歸問題。 過去的時間序列值 「 樞紐 」 以及其他預測值的迴歸輸入變數成為更多的維度。 這個方法時，不同於傳統的時間序列方法，好處是自然併入多個內容變數和其在訓練期間之間的關聯性。 自動化的 ML 學習單一的但通常在內部分支模型的資料集和預測的視野中的所有項目。 詳細資料可因此估計模型參數，並看不見的系列的一般化可能實現。 

進一步了解，並查看範例[自動化時間序列預測的機器學習服務](how-to-auto-train-forecast.md)。

## <a name="ensemble-models"></a>集團模型

您可以訓練集團模型，使用自動化的機器學習服務[Caruana 集團選取演算法已排序的集團初始化](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)。 集團學習藉由合併多個模型，而不是使用單一模型，改善機器學習結果和預測的效能。 集團反覆項目會顯示為您執行的最後一個反覆項目。

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

請參閱範例，以及了解如何建置使用自動化的機器學習服務模型：

+ 請依照下列[教學課程：自動定型迴歸模型使用 Azure 自動化的 Machine Learning](tutorial-auto-train-models.md)

+ 設定自動的訓練實驗的設定：
  + 在 Azure 入口網站介面中，[使用下列步驟](how-to-create-portal-experiments.md)。
  + 使用 Python SDK 中，[使用下列步驟](how-to-configure-auto-train.md)。

+ 了解如何自動使用時間序列資料，定型[使用下列步驟](how-to-auto-train-forecast.md)。

+ 試試看[自動化的機器學習服務範例的 Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
