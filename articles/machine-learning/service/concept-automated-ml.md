---
title: 什麼是自動化 ML/automl
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 如何自動為您挑選演算法，並使用您提供的參數和條件來為您的模型產生模型，以節省您的時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: e9bc0cf3ab0d168a55ab04913bdc71eed5c5fced
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001224"
---
# <a name="what-is-automated-machine-learning"></a>什麼是自動化機器學習服務？

自動化機器學習服務（也稱為自動化 ML）是將機器學習模型開發的耗時、反復作業自動化的程式。 它可讓資料科學家、分析師和開發人員以高擴充性、效率和生產力來建立 ML 模型，同時維持模型品質。 自動化 ML 是以[Microsoft Research 部門](https://arxiv.org/abs/1705.05355)的突破性為基礎。

傳統機器學習模型的開發會耗用大量資源，因此需要大量的領域知識和時間來產生和比較數十種模型。 當您想要 Azure Machine Learning 使用您指定的目標度量來為您定型和調整模型時，請套用自動化 ML。 然後，服務會逐一查看與特徵選取配對的 ML 演算法，其中每個反復專案都會產生具有定型分數的模型。 分數越高，模型就越能「配合」您的資料。

透過自動化機器學習，您可以加快取得生產環境就緒的 ML 模型所需的時間，並提供絕佳的輕鬆和效率。

## <a name="when-to-use-automated-ml"></a>使用自動化 ML 的時機

自動化 ML 會促進大眾化機器學習模型開發程式，並讓其使用者無論其資料科學專業知識，都能找出任何問題的端對端機器學習管線。

跨產業的資料科學家、分析師和開發人員可以使用自動化 ML 來執行下列動作：

+ 實現機器學習解決方案，而不需要廣泛的程式設計知識
+ 節省時間和資源
+ 運用資料科學最佳做法
+ 提供敏捷式問題解決

常見的自動化 ML 使用案例包括：
+ [詐騙偵測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ [行銷預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing/auto-ml-classification-bank-marketing.ipynb)
+ [需求預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
+ [銷售預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)
+ [效能預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance/auto-ml-regression-hardware-performance.ipynb)
+ [材質耐久性預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)

## <a name="how-automated-ml-works"></a>自動化 ML 的運作方式

使用**Azure Machine Learning**，您可以使用下列步驟來設計和執行自動化 ML 訓練實驗：

1. **找出要解決的 ML 問題**：分類、預測或回歸

1. 指定已加上**標籤之定型資料的來源和格式**：Numpy 陣列或 Pandas 資料框架

1. **設定用於模型定型的計算目標**，例如[本機電腦、Azure Machine Learning 計算、遠端 vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  瞭解[遠端資源](how-to-auto-train-remote.md)的自動化訓練。

1. **設定自動化機器學習參數**，以決定在決定最佳模型時，要在不同模型、超參數設定、先進前置處理/特徵化，以及要查看的度量有多少次。  您可以在[Azure 入口網站](how-to-create-portal-experiments.md)、[工作區登陸頁面（預覽）](https://ml.azure.com)或[SDK](how-to-configure-auto-train.md)中，設定自動訓練實驗的設定。 

1. **提交定型回合。**

  ![自動化機器學習服務](./media/how-to-automated-ml/automl-concept-diagram2.png)

在定型期間，Azure Machine Learning 會在並行管線中建立數個，以嘗試不同的演算法和參數。 一旦達到實驗中定義的結束準則，它就會停止。

您也可以檢查已記錄的執行資訊，其中包含執行期間所收集的[計量](how-to-understand-automated-ml.md)。 定型回合會產生 Python 序列化物件（`.pkl` 檔案），其中包含模型和資料前置處理。

雖然模型建立是自動化的，您也可以瞭解產生之模型的[重要或相關功能](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前置處理

在每個自動化機器學習實驗中，您的資料會使用預設方法進行前置處理，並選擇性地透過 advanced 前置處理。

> [!NOTE]
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

### <a name="automatic-preprocessing-standard"></a>自動前置處理（標準）

在每個自動化機器學習實驗中，您的資料都會自動調整或正規化，以協助演算法執行得很好。  在模型定型期間，下列其中一個調整或正規化技術將會套用至每個模型。

|調整 @ no__t-0 @ no__t-1 @ no__t-2normalization| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 藉由移除平均值並調整為單位變異數，將功能標準化  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 藉由依該資料行的最小值和最大值調整每項功能來轉換功能  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |依最大絕對值來調整每個功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此 Scaler 功能的分量範圍 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |線性維度會使用資料的單一值分解，將其投影至較低的維度空間 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |這個轉換器會藉由截斷的單值分解（SVD）來執行線性維度縮減。 相對於 PCA，此估計工具不會在計算單數值分解之前，先將資料置中。 這表示它可以有效率地使用 scipy. sparse 矩陣 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每個範例（也就是資料矩陣的每個資料列），至少有一個非零的元件會與其他樣本分開調整，使其標準（l1 或 l2）等於一 |

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced 前置處理：選擇性特徵化

也可以使用其他的先進前置處理和特徵化，例如遺漏值插補、編碼和轉換。 [深入瞭解包含的特徵化](how-to-create-portal-experiments.md#preprocess)。 啟用此設定的方式：

+ Azure 入口網站：[使用這些步驟](how-to-create-portal-experiments.md)**選取 [** **Advanced] 設定**中的 [前置處理] 核取方塊。

+ Python SDK：指定[@no__t 2 類別](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)的 `"preprocess": True`。


## <a name="time-series-forecasting"></a>時間序列預測
建立預測是任何企業不可或缺的一部分，不論是收入、庫存、銷售或客戶需求。 您可以使用自動化 ML 結合技術和方法，並取得建議的高品質時間序列預測。

自動化的時間序列實驗會被視為多變數回歸問題。 過去的時間序列值會「切換」為回歸輸入變數的其他維度與其他預測指標。 與傳統時間序列方法不同的是，這種方法的優點是在定型期間自然結合多個內容變數及其關聯性。 自動化 ML 會針對資料集和預測視野中的所有專案學習單一但通常會在內部分支的模型。 因此，有更多的資料可用於估計模型參數，而一般化的數列則會變成可行的。

深入瞭解並查看[適用于時間序列預測的自動化機器學習](how-to-auto-train-forecast.md)範例。 或者，如需先進預測設定的詳細程式碼範例，請參閱[能源需求筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)，包括：

* 假日偵測和特徵化
* 復原原始的交叉驗證
* 可設定延遲
* 滾動視窗匯總功能

## <a name="ensemble"></a>集團模型

自動化機器學習支援預設會啟用的集團模型。 集團 learning 藉由結合多個模型來改善機器學習結果和預測效能，而不是使用單一模型。 集團反復專案會顯示為您執行的最後一次反覆運算。 自動化機器學習會使用投票和堆疊集團方法來結合模型：

* **投票**：根據預測類別機率（適用于分類工作）或預測回歸目標（用於回歸工作）的加權平均值進行預測。
* **堆疊**：堆疊結合了異類模型，並根據個別模型的輸出來訓練中繼模型。 目前的預設中繼模型是針對分類工作和回歸/預測工作的 ElasticNet 而 LogisticRegression 的。

具有已排序集團初始化的[Caruana 集團選取演算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)會用來決定要在集團中使用的模型。 就高層級而言，此演算法會使用最多5個具有最佳個別分數的模型來初始化集團，並確認這些模型是在最佳分數的 5% 臨界值內，以避免初始集團不佳。 然後針對每個集團反復專案，將新的模型加入至現有的集團，並計算產生的分數。 如果新模型已改善現有的集團分數，集團會更新以包含新的模型。

請參閱[如何](how-to-configure-auto-train.md#ensemble)變更自動化機器學習服務中的預設集團設定。

## <a name="imbalance"></a>不平衡資料

不平衡資料通常會在機器學習分類案例的資料中找到，而則是指在每個類別中包含不相稱比例的觀察資料。 此不平衡可能會導致不正確地發現模型精確度的正面副作用，因為輸入資料有偏差到一個類別，這會導致定型的模型模擬該偏差。 

自動化 ML 是在簡化機器學習工作流程的目標過程中，具備內建的功能，可協助處理不平衡資料，例如， 

- 加權資料**行**：自動化 ML 支援加權資料行做為輸入，導致資料中的資料列加權為相應增加或減少，這可能會讓類別變得更多或更少「重要」。 請參閱此[筆記本範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb) 

- 自動化 ML 所使用的演算法可以適當地處理最多20:1 的不平衡，這表示在資料中，最常見的類別可能會比最不常見的類別擁有20倍以上的資料列。

### <a name="identify-models-with-imbalanced-data"></a>識別具有不平衡資料的模型

因為分類演算法通常會以精確度進行評估，所以檢查模型的精確度分數是識別它是否受到不平衡資料影響的好方法。 對於某些類別而言，它的精確度很高，還是精確度很低？

此外，自動化 ML 執行會自動產生下列圖表，這可協助您瞭解模型分類的正確性，並識別可能受到不平衡資料影響的模型。

圖表| 描述
---|---
[混淆矩陣](how-to-understand-automated-ml.md#confusion-matrix)| 針對資料的實際標籤，評估正確分類的標籤。 
[精確度-召回](how-to-understand-automated-ml.md#precision-recall-chart)| 根據找到的標籤實例的比率，評估正確標籤的比率 
[ROC 曲線](how-to-understand-automated-ml.md#roc)| 根據誤報標籤的比例，評估正確標籤的比率。

### <a name="handle-imbalanced-data"></a>處理不平衡資料 

下列技術是在自動化 ML 外處理不平衡資料的其他選項。 

- 將較小的類別向上取樣或向下取樣較大的類別，即使類別不平衡，也會重新取樣。 這些方法需要專業知識來處理和分析。

- 使用效能標準，以更佳的方式處理不平衡資料。 例如，F1 分數是精確度和召回率的加權平均值。 精確度測量分類器的 exactness--低精確度表示大量的誤報--,，而召回量值則是測量分類器的完整性--低召回表示有大量的錯誤否定。 

## <a name="use-with-onnx-in-c-apps"></a>在應用程式中C#使用 ONNX

使用 Azure Machine Learning，您可以使用自動化 ML 來建立 Python 模型，並將它轉換成 ONNX 格式。 ONNX 執行時間支援C#，因此您可以使用在C#應用程式中自動建立的模型，而不需要任何預先編碼或 REST 端點引進的任何網路延遲。 請[在此 Jupyter 筆記本中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)嘗試此流程的範例。

## <a name="automated-ml-across-microsoft"></a>跨 Microsoft 的自動化 ML

自動化 ML 也適用于其他 Microsoft 解決方案，例如：

|整合|描述|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|使用 Visual Studio 和 Visual Studio Code 搭配 ML.NET 自動化 ML （預覽），在 .NET 應用程式中自動選取和定型模型。|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|以平行方式在 HDInsight 叢集中的 Spark 上相應放大您的自動化 ML 訓練作業。|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|直接在 Power BI （預覽）中叫用機器學習模型。|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|在 SQL Server 2019 big data 叢集內，透過您的資料建立新的機器學習模型。|

## <a name="next-steps"></a>後續步驟

請參閱範例，並瞭解如何使用自動化機器學習來建立模型：

+ 遵循 [Tutorial：使用 Azure 自動化將回歸模型自動定型 Machine Learning @ no__t-0

+ 設定自動訓練實驗的設定：
  + 在 Azure 入口網站介面或工作區登陸頁面（預覽）中，[使用下列步驟](how-to-create-portal-experiments.md)。
  + 透過 Python SDK，請[使用下列步驟](how-to-configure-auto-train.md)。

+ 若要瞭解如何使用時間序列資料來自動定型，請[使用下列步驟](how-to-auto-train-forecast.md)。

+ 試用[自動化機器學習 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
