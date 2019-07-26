---
title: 什麼是自動化 ML/automl
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
ms.openlocfilehash: c563278a9d23810a5e6f0adc8082c8cfc5a0510c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358863"
---
# <a name="what-is-automated-machine-learning"></a>什麼是自動化機器學習服務？

自動化機器學習 (也稱為「autoML」) 是將機器學習模型開發的耗時、反復作業自動化的程式。 它可讓資料科學家、分析師和開發人員以高擴充性、效率和生產力來建立 ML 模型, 同時維持模型品質。

傳統機器學習模型的開發會耗用大量資源, 因此需要大量的領域知識和時間來產生和比較數十種模型。 當您想要 Azure Machine Learning 使用您指定的目標度量來為您定型和調整模型時, 請套用自動化 ML。 然後, 服務會逐一查看與特徵選取配對的 ML 演算法, 其中每個反復專案都會產生具有定型分數的模型。 分數越高, 模型就越能「配合」您的資料。

透過自動化機器學習, 您可以加快取得生產環境就緒的 ML 模型所需的時間, 並提供絕佳的輕鬆和效率。

## <a name="when-to-use-automated-ml"></a>使用自動化 ML 的時機

自動化 ML 會促進大眾化機器學習模型開發程式, 並讓其使用者無論其資料科學專業知識, 都能找出任何問題的端對端機器學習管線。

跨產業的資料科學家、分析師和開發人員可以使用自動化 ML 來執行下列動作:

+ 實現機器學習解決方案, 而不需要廣泛的程式設計知識
+ 節省時間和資源
+ 運用資料科學最佳做法
+ 提供敏捷式問題解決

## <a name="how-automated-ml-works"></a>自動化 ML 的運作方式

使用**Azure Machine Learning 服務**, 您可以使用下列步驟來設計和執行自動化 ML 訓練實驗:

1. **找出要解決的 ML 問題**: 分類、預測或回歸

1. 指定已加上**標籤之定型資料的來源和格式**:Numpy 陣列或 Pandas 資料框架

1. **設定用於模型定型的計算目標**, 例如[本機電腦、Azure Machine Learning 計算、遠端 vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  瞭解[遠端資源](how-to-auto-train-remote.md)的自動化訓練。

1. **設定自動化機器學習參數**, 以決定在決定最佳模型時, 要在不同模型、超參數設定、先進前置處理/特徵化, 以及要查看的度量有多少次。  您可以[在 Azure 入口網站](how-to-create-portal-experiments.md)或[使用 SDK](how-to-configure-auto-train.md)來設定自動訓練實驗的設定。

1. **提交定型回合。**

  ![自動化機器學習服務](./media/how-to-automated-ml/automl-concept-diagram2.png)

在定型期間, Azure Machine Learning 服務會在並行管線中建立數個, 以嘗試不同的演算法和參數。 一旦達到實驗中定義的結束準則, 它就會停止。

您也可以檢查已記錄的執行資訊, 其中包含執行期間所收集的[計量](how-to-understand-automated-ml.md)。 定型回合會產生 Python 序列化物件 (`.pkl` file), 其中包含模型和資料前置處理。

雖然模型建立是自動化的, 您也可以瞭解產生之模型的[重要或相關功能](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>預處理

在每個自動化機器學習實驗中, 您的資料會使用預設方法進行前置處理, 並選擇性地透過 advanced 前置處理。

### <a name="automatic-preprocessing-standard"></a>自動前置處理 (標準)

在每個自動化機器學習實驗中, 您的資料都會自動調整或正規化, 以協助演算法執行得很好。  在模型定型期間, 下列其中一個調整或正規化技術將會套用至每個模型。

|調整&nbsp;正規化& &nbsp;| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 藉由移除平均值並調整為單位變異數, 將功能標準化  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 藉由依該資料行的最小值和最大值調整每項功能來轉換功能  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |依最大絕對值來調整每個功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此 Scaler 功能的分量範圍 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |線性維度會使用資料的單一值分解, 將其投影至較低的維度空間 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |這個轉換器會藉由截斷的單值分解 (SVD) 來執行線性維度縮減。 相對於 PCA, 此估計工具不會在計算單數值分解之前, 先將資料置中。 這表示它可以有效率地使用 scipy. sparse 矩陣 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每個範例 (也就是資料矩陣的每個資料列), 至少有一個非零的元件會與其他樣本分開調整, 使其標準 (l1 或 l2) 等於一 |

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced 前置處理: 選擇性特徵化

也可以使用其他的先進前置處理和特徵化, 例如遺漏值插補、編碼和轉換。 [深入瞭解包含的特徵化](how-to-create-portal-experiments.md#preprocess)。 啟用此設定的方式:

+ Azure 入口網站：[使用這些步驟](how-to-create-portal-experiments.md)選取 [ **Advanced] 設定**中的 [前置處理] 核取方塊。

+ Python SDK:指定`"preprocess": True`類別的。 [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)


## <a name="time-series-forecasting"></a>時間序列預測
建立預測是任何企業不可或缺的一部分, 不論是收入、庫存、銷售或客戶需求。 您可以使用自動化 ML 結合技術和方法, 並取得建議的高品質時間序列預測。 

自動化的時間序列實驗會被視為多變數回歸問題。 過去的時間序列值會「切換」為回歸輸入變數的其他維度與其他預測指標。 與傳統時間序列方法不同的是, 這種方法的優點是在定型期間自然結合多個內容變數及其關聯性。 自動化 ML 會針對資料集和預測視野中的所有專案學習單一但通常會在內部分支的模型。 因此, 有更多的資料可用於估計模型參數, 而一般化的數列則會變成可行的。 

深入瞭解並查看[適用于時間序列預測的自動化機器學習](how-to-auto-train-forecast.md)範例。

## <a name="ensemble-models"></a>集團模型

您可以使用自動化機器學習來定型集團模型, 並搭配已[排序集團初始化的 Caruana 集團選取演算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)。 集團 learning 藉由合併許多模型來改善機器學習結果和預測效能, 而不是使用單一模型。 集團反復專案會顯示為執行的最後一個反復專案。

## <a name="use-with-onnx-in-c-apps"></a>在應用程式中C#使用 ONNX

使用 Azure Machine Learning, 您可以使用自動化 ML 來建立 Python 模型, 並將它轉換成 ONNX 格式。 ONNX 執行時間支援C#, 因此您可以使用在C#應用程式中自動建立的模型, 而不需要任何預先編碼或 REST 端點引進的任何網路延遲。 請[在此 Jupyter 筆記本中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)嘗試此流程的範例。

## <a name="automated-ml-across-microsoft"></a>跨 Microsoft 的自動化 ML

自動化 ML 也適用于其他 Microsoft 解決方案, 例如:

|整合|描述|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|使用 Visual Studio 和 Visual Studio Code 搭配 ML.NET 自動化 ML (預覽), 在 .NET 應用程式中自動選取和定型模型。|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|以平行方式在 HDInsight 叢集中的 Spark 上相應放大您的自動化 ML 訓練作業。|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|直接在 Power BI (預覽) 中叫用機器學習模型。|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|在 SQL Server 2019 big data 叢集內, 透過您的資料建立新的機器學習模型。|

## <a name="next-steps"></a>後續步驟

請參閱範例, 並瞭解如何使用自動化機器學習來建立模型:

+ [遵循教學課程:使用 Azure 自動化 Machine Learning 自動定型回歸模型](tutorial-auto-train-models.md)

+ 設定自動訓練實驗的設定:
  + 在 Azure 入口網站介面中,[使用下列步驟](how-to-create-portal-experiments.md)。
  + 透過 Python SDK, 請[使用下列步驟](how-to-configure-auto-train.md)。

+ 若要瞭解如何使用時間序列資料來自動定型, 請[使用下列步驟](how-to-auto-train-forecast.md)。

+ 試用[自動化機器學習 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
