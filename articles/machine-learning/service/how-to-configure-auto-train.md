---
title: 建立自動化機器學習實驗
titleSuffix: Azure Machine Learning service
description: 自動化機器學習服務可為您挑選演算法，並產生馬上可進行部署的模型。 了解可用來設定自動化機器學習實驗的選項。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 64ba7096f181371a378708e024f46bce17449e98
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510578"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中設定自動化的 ML 實驗

在本指南中，了解如何定義您的自動化機器學習服務實驗使用的各種組態設定[Azure 機器學習服務 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。 自動化機器學習服務會為您挑選演算法和超參數，並產生馬上可進行部署的模型。 有數個選項可用來設定自動化機器學習實驗。

若要檢視自動化機器學習實驗的範例，請參閱[教學課程：使用自動化機器學習訓練分類模型](tutorial-auto-train-models.md)或[使用雲端中的自動化機器學習來訓練模型](how-to-auto-train-remote.md)。

自動化機器學習服務中的可用設定選項：

* 選取您的實驗類型：分類、 迴歸或時間序列預測
* 資料來源、格式和擷取資料
* 選擇您的計算目標：本機或遠端
* 自動化機器學習實驗設定
* 執行自動化機器學習實驗
* 探索模型計量
* 註冊和部署模型

如果您不偏好的任何程式碼的體驗，您也可以[建立自動化的機器學習服務實驗，在 Azure 入口網站中的](how-to-create-portal-experiments.md)。

## <a name="select-your-experiment-type"></a>選取您的實驗類型

在開始實驗之前，您應先決定所要解決的機器學習問題類型。 自動化機器學習支援分類、迴歸和預測等工作類型。

在自動化和調整程序期間，自動化機器學習支援下列演算法。 身為使用者，您不需要指定演算法。 在定型期間，DNN 演算法可供使用，而自動化的 ML 就不會建置 DNN 模型。

分類 | 迴歸 | 時間序列預測
|-- |-- |--
[羅吉斯迴歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C 支援向量分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線性分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線性迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[線性迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


## <a name="data-source-and-format"></a>資料來源和格式
自動化機器學習支援位於本機桌面或雲端中 (例如 Azure Blob 儲存體) 所包含的資料。 資料可讀取到支援 scikit-learn 的資料格式中。 您可以將資料讀取到：
* Numpy 陣列 X (特徵) 和 y (目標變數，或者也稱為標籤)
* Pandas 資料框架

範例：

*   Numpy 陣列

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas 資料框架

    ```python
    import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>擷取用來對遠端計算執行實驗的資料

如果您使用遠端計算來執行實驗，則必須將資料擷取包覆在個別的 python 指令碼 `get_data()` 中。 此指令碼會在自動化機器學習實驗執行所在的遠端計算上執行。 若使用 `get_data`，您即無須在每次反覆運算時透過連線擷取資料。 若未使用 `get_data`，您在遠端計算上執行的實驗將會失敗。

以下是 `get_data` 的範例：

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

在您的 `AutoMLConfig` 物件中，您會指定 `data_script` 參數，並提供如下的 `get_data` 指令碼檔案路徑：

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` 指令碼可傳回：

Key | Type | 互斥項目    | 說明
---|---|---|---
X | Pandas 資料框架或 Numpy 陣列 | data_train、標籤、資料行 |  所有要用於訓練的特徵
y | Pandas 資料框架或 Numpy 陣列 |   標籤   | 要用於訓練的標籤資料。 就分類而言，應為整數的陣列。
X_valid | Pandas 資料框架或 Numpy 陣列   | data_train、標籤 | _選擇性_ 所有要用於驗證的特徵。 如果未指定，則 X 會分割至訓練和驗證之間
y_valid |   Pandas 資料框架或 Numpy 陣列 | data_train、標籤 | _選擇性_ 要用於驗證的標籤資料。 如果未指定，則 y 會分割至訓練和驗證之間
sample_weight | Pandas 資料框架或 Numpy 陣列 |   data_train、標籤、資料行| _選擇性_ 每個範例的加權值。 如果您想要為資料點指派不同的加權，則應使用
sample_weight_valid | Pandas 資料框架或 Numpy 陣列 | data_train、標籤、資料行 |    _選擇性_ 每個驗證範例的加權值。 如果未指定，則 sample_weight 會分割至訓練和驗證之間
data_train |    Pandas 資料框架 |  X、y、X_valid、y_valid |    所有要用於訓練的資料 (特徵+標籤)
標籤 | string  | X、y、X_valid、y_valid |  data_train 中的哪個資料行代表標籤
columns | 字串的陣列  ||  _選擇性_ 要用於特徵的資料行白名單
cv_splits_indices   | 一連串整數 ||  _選擇性_ 用來分割交叉驗證資料的索引清單

### <a name="load-and-prepare-data-using-data-prep-sdk"></a>載入並準備資料使用資料準備 SDK
自動化的機器學習服務實驗支援載入資料，並將轉換使用資料準備 SDK。 使用 SDK 能夠

>* 使用剖析參數推斷 (編碼、分隔符號、標頭)，從許多檔案類型載入
>* 在檔案載入期間使用推斷進行類型轉換
>* MS SQL Server 和 Azure Data Lake Storage 的連線支援
>* 加入使用運算式的資料行
>* 插補遺漏值
>* 依範例衍生資料行
>* 正在篩選
>* 自訂 Python 轉換

若要深入了解 data prep sdk，請參閱[如何準備資料以進行模型化文件](how-to-load-data.md)。
以下是使用 data prep sdk 載入資料的 sdk 範例。
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>訓練和驗證資料

您可以透過 get_data() 個別的訓練和驗證集，或直接在 `AutoMLConfig` 方法中指定。

## <a name="cross-validation-split-options"></a>交叉驗證分割選項

### <a name="k-folds-cross-validation"></a>k 疊交叉驗證

使用 `n_cross_validations` 設定來指定交叉驗證的數目。 訓練資料集會隨機分割為相同大小的 `n_cross_validations` 疊。 在每個交叉驗證回合中，將使用其中一個疊來驗證以其餘幾疊進行訓練的模型。 此程序會重複執行 `n_cross_validations` 回合，直到每一疊都已作為驗證集執行一次。 將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。 

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo 交叉驗證 （重複隨機子取樣）

使用 `validation_size` 指定應該用於驗證的訓練資料集百分比，並使用 `n_cross_validations` 指定交叉驗證的數目。 在每個交叉驗證回合中，將隨機選取 `validation_size` 大小的子集來驗證以其餘資料進行訓練的模型。 最後將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。 Monte Carlo 不支援時間序列預測。

### <a name="custom-validation-dataset"></a>自訂驗證資料集

使用自訂的驗證資料集不接受隨機劃分的資料時，通常是時間序列資料或不平衡的資料。 您可以指定自己的驗證資料集。 系統會根據指定的驗證資料集來評估模型，而不是隨機的資料集。

## <a name="compute-to-run-experiment"></a>要執行實驗的計算

接下來，請決定要在何處訓練模型。 自動化機器學習訓練實驗可在下列計算選項上執行：
*   您的本機電腦，例如本機桌上型電腦或膝上型電腦 – 通常適用於資料集並不大，且您尚在探索階段時。
*   雲端中的遠端機器 – [Azure Machine Learning 受控計算](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets)是一項受控服務，能夠在 Azure 虛擬機器叢集上定型機器學習模型。

如需具有本機與遠端計算目標的範例筆記本，請參閱 [GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

*   在您的 Azure 訂用帳戶中 Azure Databricks 叢集。 您可以找到更多詳細資料-[安裝 Azure Databricks 叢集自動化 ml](how-to-configure-environment.md#azure-databricks)

請參閱[GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)例如與 Azure Databricks notebook。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>設定您的實驗設定

有數個選項可用來設定自動化機器學習實驗。 將 `AutoMLConfig` 物件具現化即可設定這些參數。 如需完整的參數清單，請參閱 [AutoMLConfig 類別](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)。  

部分範例包括：

1.  使用加權的 AUC 作為主要計量的分類實驗，每次反覆運算的時間上限為 12,000 秒，並且在 50 次反覆運算和 2 疊的交叉驗證之後結束實驗。

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  以下範例說明設定在 100 次反覆運算之後結束的迴歸實驗，每次反覆運算的持續時間上限為 600 秒，並執行 5 疊的交叉驗證。

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

三個不同`task`參數值會決定要套用的演算法的清單。  使用 `whitelist` 或 `blacklist` 參數，以進一步修改可用的演算法來包含或排除反覆項目。 支援模型的清單可於[SupportedAlgorithms 類別](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)。

## <a name="primary-metric"></a>主要計量
主要的計量;如所示，在上述範例中會決定要用於模型定型期間，進行最佳化的計量。 您可以選取主要度量取決於您所選擇的工作類型。 以下是可用計量清單。

|分類 | 迴歸 | 時間序列預測
|-- |-- |--
|精確度| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-preprocessing--featurization"></a>資料前置處理和特徵化

在每個自動化的機器學習服務實驗，是您的資料[自動調整和正規化](concept-automated-ml.md#preprocess)為了妥善執行的演算法。  不過，您也可以啟用其他前置處理/功能，例如遺失值插補、 編碼和轉換。 [深入了解哪些功能就會包含](how-to-create-portal-experiments.md#preprocess)。 

若要啟用此功能，請指定`"preprocess": True`for [ `AutoMLConfig`類別](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)。

## <a name="time-series-forecasting"></a>時間序列預測
時間序列預測的工作類型，您會有額外的參數定義。
1. time_column_name-這是必要的參數定義的定型資料包含日期/時間序列的資料行的名稱。 
1. max_horizon-這會定義您想要預測出根據定型資料的週期性的時間長度。 比方說如果您有與每日的時間粒紋的定型資料，則您幅度縮小定義天後您想要定型的模型中。
1. grain_column_names-這會定義包含定型資料中的個別的時間序列資料的資料行的名稱。 比方說，如果您預測的存放區的特定品牌的銷售，您會將存放區和品牌的資料行定義為您的資料粒度資料行。

這些範例，請參閱下面所使用的設定，notebook 範例是可用[此處](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)。

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="run-experiment"></a>執行實驗

提交實驗以執行與產生模型。 將 `AutoMLConfig` 傳至 `submit` 方法以產生模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>相依性會先安裝在新的機器上。  最多可能需要 10 分鐘才會顯示輸出。
>將 `show_output` 設定為 `True`，會使輸出顯示在主控台上。

## <a name="exit-criteria"></a>允出準則 
那里幾個選項您可以定義完成您的實驗。
1. 沒有準則-如果您沒有定義任何結束實驗將會繼續進行任何進一步的處理對您主要的度量的參數。 
1. 數字的反覆項目-您可以定義執行測試的反覆項目數目。 您可以選擇性新增 iteration_timeout_minutes 定義在每個反覆項目每分鐘的時間限制。
1. 一段時間-在您設定，您可以定義以分鐘為單位的時間長度應該繼續執行中的實驗中使用的 experiment_timeout_minutes 後結束。
1. 結束後的得分達到-使用的 experiment_exit_score 完成實驗，一旦達到您主要的計量為基礎的分數時，您可以選擇。

## <a name="explore-model-metrics"></a>探索模型計量
如果您使用 Notebook，您可以在小工具中或以內嵌方式檢視結果。 請參閱[追蹤和評估模型](how-to-track-experiments.md#view-run-details)以取得更多詳細資料。


### <a name="classification-metrics"></a>分類計量
在每次反覆運算分類工作時會儲存下列計量。

|計量|說明|計算|額外的參數
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

### <a name="regression-and-time-series-forecasting-metrics"></a>迴歸 」 和 「 時間序列預測的計量
下列計量儲存在迴歸或預測工作的每個反覆項目中。

|計量|說明|計算|額外的參數
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


## <a name="understand-automated-ml-models"></a>了解自動化的 ML 模型

使用自動化的 ML 所產生的任何模型包含下列步驟：
+ 自動化功能工程 (如果前置處理 = True)
+ 調整/正規化和以 hypermeter 值的演算法

我們讓透明從自動化的 ML fitted_model 輸出中取得這項資訊。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自動化的功能工程

請參閱份前置處理和[自動化特性工程設計](concept-automated-ml.md#preprocess)發生這種情況時前置處理 = True。  

請思考此範例：
+ 有 4 個輸入的功能：（數字） 的 B （數值） （數值） 的 C、 D (DateTime)
+ 數值特徵 C 會卸除，因為它是所有的唯一值的識別碼資料行
+ A 和 B 的數值特徵有遺漏值，並因此會計算平均數
+ DateTime 功能 D 是 11 不同的工程設計功能特徵化

使用這些 2 上適合模型的第一個步驟以了解更多的 Api。  請參閱[此 notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1:`get_engineered_feature_names()`傳回一份經過工程設計的功能名稱。

  Usage : 
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此清單包含所有經過工程設計的功能名稱。

  >[!Note]
  >用來執行工作的 'timeseriestransformer' = '預測' 使用 'datatransformer'，'迴歸' 或 '分類' 的工作。 

+ API 2:`get_featurization_summary()`傳回特徵化的所有輸入的功能摘要。

  Usage : 
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >用來執行工作的 'timeseriestransformer' = '預測' 使用 'datatransformer'，'迴歸' 或 '分類' 的工作。

  輸出：
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```
  
   其中：
   
   |輸出|定義|
   |----|--------|
   |RawFeatureName|從提供的資料集的輸入的功能/資料行名稱。| 
   |TypeDetected|偵測到的資料類型的輸入功能。|
   |卸除|表示是否卸除或使用 「 輸入 」 功能。|
   |EngineeringFeatureCount|透過自動化的功能工程轉換所產生的特徵數目。|
   |轉換|轉換套用至輸入功能，可產生經過工程設計的功能清單。|  

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>調整/正規化和以 hypermeter 值的演算法：

若要了解管線的正規化調整和演算法/超參數值，請使用 fitted_model.steps。 [深入了解調整/正規化](concept-automated-ml.md#preprocess)。 以下是範例輸出：

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

若要取得更多詳細資料，請使用 顯示此 helper 函式[此 notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)。

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()
                
print_model(fitted_model)
```

以下是範例輸出：

+ 使用特定的演算法 (在此情況下 RobustScalar 與 LogisticRegression) 的管線：

  ```
  RobustScaler
  {'copy': True,
   'quantile_range': [10, 90],
   'with_centering': True,
   'with_scaling': True}
  
  LogisticRegression
  {'C': 0.18420699693267145,
   'class_weight': 'balanced',
   'dual': False,
   'fit_intercept': True,
   'intercept_scaling': 1,
   'max_iter': 100,
   'multi_class': 'multinomial',
   'n_jobs': 1,
   'penalty': 'l2',
   'random_state': None,
   'solver': 'newton-cg',
   'tol': 0.0001,
   'verbose': 0,
   'warm_start': False}
  ```
  
+ 使用整體方法中的管線：在此情況下，它是一整團的 2 個不同的管線

  ```
  prefittedsoftvotingclassifier
  {'estimators': ['1', '18'],
  'weights': [0.6666666666666667,
              0.3333333333333333]}

  1 - RobustScaler
  {'copy': True,
   'quantile_range': [25, 75],
   'with_centering': True,
   'with_scaling': False}
  
  1 - LightGBMClassifier
  {'boosting_type': 'gbdt',
   'class_weight': None,
   'colsample_bytree': 0.2977777777777778,
   'importance_type': 'split',
   'learning_rate': 0.1,
   'max_bin': 30,
   'max_depth': 5,
   'min_child_samples': 6,
   'min_child_weight': 5,
   'min_split_gain': 0.05263157894736842,
   'n_estimators': 200,
   'n_jobs': 1,
   'num_leaves': 176,
   'objective': None,
   'random_state': None,
   'reg_alpha': 0.2631578947368421,
   'reg_lambda': 0,
   'silent': True,
   'subsample': 0.8415789473684211,
   'subsample_for_bin': 200000,
   'subsample_freq': 0,
   'verbose': -10}
  
  18 - StandardScalerWrapper
  {'class_name': 'StandardScaler',
   'copy': True,
   'module_name': 'sklearn.preprocessing.data',
   'with_mean': True,
   'with_std': True}
  
  18 - LightGBMClassifier
  {'boosting_type': 'goss',
   'class_weight': None,
   'colsample_bytree': 0.2977777777777778,
   'importance_type': 'split',
   'learning_rate': 0.07894947368421053,
   'max_bin': 30,
   'max_depth': 6,
   'min_child_samples': 47,
   'min_child_weight': 0,
   'min_split_gain': 0.2631578947368421,
   'n_estimators': 400,
   'n_jobs': 1,
   'num_leaves': 14,
   'objective': None,
   'random_state': None,
   'reg_alpha': 0.5789473684210527,
   'reg_lambda': 0.7894736842105263,
   'silent': True,
   'subsample': 1,
   'subsample_for_bin': 200000,
   'subsample_freq': 0,
   'verbose': -10}
  ```
  
<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>說明模型 (interpretability)

自動化機器學習服務可讓您了解特徵重要性。  在定型過程中，您可以取得模型的全域特徵重要性。  針對分類的情況，您也可以取得類別層級的特徵重要性。  您必須提供驗證資料集 (X_valid)，才能取得特徵重要性。

有兩種方式可產生特徵重要性。

*   一旦完成實驗，您可以在任何反覆項目上使用 `explain_model` 方法。

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   若要檢視所有反覆項目的特徵重要性，請在 AutoMLConfig 中將 `model_explainability` 旗標設為`True`。

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    完成後，您可以使用 retrieve_model_explanation 方法來擷取特定反覆項目的特徵重要性。

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

在 Azure 入口網站中，您可以視覺化工作區中的特徵重要性圖表。 在筆記本中使用 Jupyter 小工具時，此會顯示此圖表。 若要深入了解圖表，請參閱[範例 Azure Machine Learning 服務筆記本文章](samples-notebooks.md)。

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![特徵重要性圖形](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>後續步驟

深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

深入了解[如何使用自動化 machine learning 迴歸模型定型](tutorial-auto-train-models.md)或是[如何定型使用自動存取遠端資源上的機器學習服務](how-to-auto-train-remote.md)。
