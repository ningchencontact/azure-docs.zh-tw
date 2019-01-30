---
title: 建立自動化機器學習實驗
titleSuffix: Azure Machine Learning service
description: 自動化機器學習服務可為您挑選演算法，並產生馬上可進行部署的模型。 了解可用來設定自動化機器學習實驗的選項。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 865d00d4a6608e422fdfca1297962913ee205827
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823431"
---
# <a name="configure-automated-machine-learning-experiments"></a>設定自動化機器學習實驗

自動化機器學習服務會為您挑選演算法和超參數，並產生馬上可進行部署的模型。 有數個選項可用來設定自動化機器學習實驗。 在本指南中，您將了解如何定義各種不同的組態設定。

若要檢視自動化機器學習實驗的範例，請參閱[教學課程：使用自動化機器學習訓練分類模型](tutorial-auto-train-models.md)或[使用雲端中的自動化機器學習來訓練模型](how-to-auto-train-remote.md)。

自動化機器學習服務中的可用設定選項：

* 選取您的實驗類型：分類、迴歸或預測
* 資料來源、格式和擷取資料
* 選擇您的計算目標：本機或遠端
* 自動化機器學習實驗設定
* 執行自動化機器學習實驗
* 探索模型計量
* 註冊和部署模型

## <a name="select-your-experiment-type"></a>選取您的實驗類型
在開始實驗之前，您應先決定所要解決的機器學習問題類型。 自動化機器學習支援分類、迴歸和預測等工作類型。 

雖然自動化機器學習功能一般可供使用，但**預測仍處於公開預覽狀態。**

在自動化和調整程序期間，自動化機器學習支援下列演算法。 身為使用者，您不需要指定演算法。

分類 | 迴歸 | 預測
|-- |-- |--
[羅吉斯迴歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[C 支援向量分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


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

Key | 類型 |    互斥項目 | 說明
---|---|---|---
X | Pandas 資料框架或 Numpy 陣列 | data_train、標籤、資料行 |  所有要用於訓練的特徵
y | Pandas 資料框架或 Numpy 陣列 |   標籤   | 要用於訓練的標籤資料。 就分類而言，應為整數的陣列。
X_valid | Pandas 資料框架或 Numpy 陣列   | data_train、標籤 | _選擇性_ 所有要用於驗證的特徵。 如果未指定，則 X 會分割至訓練和驗證之間
y_valid |   Pandas 資料框架或 Numpy 陣列 | data_train、標籤 | _選擇性_ 要用於驗證的標籤資料。 如果未指定，則 y 會分割至訓練和驗證之間
sample_weight | Pandas 資料框架或 Numpy 陣列 |   data_train、標籤、資料行| _選擇性_ 每個範例的加權值。 如果您想要為資料點指派不同的加權，則應使用 
sample_weight_valid | Pandas 資料框架或 Numpy 陣列 | data_train、標籤、資料行 |    _選擇性_ 每個驗證範例的加權值。 如果未指定，則 sample_weight 會分割至訓練和驗證之間
data_train |    Pandas 資料框架 |  X、y、X_valid、y_valid |    所有要用於訓練的資料 (特徵+標籤)
標籤 | 字串  | X、y、X_valid、y_valid |  data_train 中的哪個資料行代表標籤
columns | 字串的陣列  ||  _選擇性_ 要用於特徵的資料行白名單
cv_splits_indices   | 一連串整數 ||  _選擇性_ 用來分割交叉驗證資料的索引清單

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>使用 DataPrep SDK 載入並準備資料
自動化機器學習實驗支援載入資料，並使用 dataprep SDK 轉換。 使用 SDK 能夠

>* 使用剖析參數推斷 (編碼、分隔符號、標頭)，從許多檔案類型載入
>* 在檔案載入期間使用推斷進行類型轉換
>* MS SQL Server 和 Azure Data Lake Storage 的連線支援
>* 加入使用運算式的資料行
>* 插補遺漏值
>* 依範例衍生資料行
>* 篩選
>* 自訂 Python 轉換

若要深入了解 data prep sdk，請參閱[如何準備資料以進行模型化文件](how-to-load-data.md)。 以下是使用 data prep sdk 載入資料的 sdk 範例。 
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

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>蒙地卡羅交叉驗證 (也稱為 重複隨機子取樣)

使用 `validation_size` 指定應該用於驗證的訓練資料集百分比，並使用 `n_cross_validations` 指定交叉驗證的數目。 在每個交叉驗證回合中，將隨機選取 `validation_size` 大小的子集來驗證以其餘資料進行訓練的模型。 最後將會報告 `n_cross_validations` 個回合的總平均分數，並以整個訓練資料集重新訓練對應的模型。

### <a name="custom-validation-dataset"></a>自訂驗證資料集

如果隨機分割不可行，請使用自訂驗證資料集 (通常是時間序列資料或不平衡的資料)。 您可以指定自己的驗證資料集。 系統會根據指定的驗證資料集來評估模型，而不是隨機的資料集。

## <a name="compute-to-run-experiment"></a>要執行實驗的計算

接下來，請決定要在何處訓練模型。 自動化機器學習訓練實驗可在下列計算選項上執行：
*   您的本機電腦，例如本機桌上型電腦或膝上型電腦 – 通常適用於資料集並不大，且您尚在探索階段時。
*   雲端中的遠端機器 – [Azure Machine Learning 受控計算](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets)是一項受控服務，能夠在 Azure 虛擬機器叢集上定型機器學習模型。

如需具有本機與遠端計算目標的範例筆記本，請參閱 [GitHub 網站](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl)。

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>設定您的實驗設定

有數個選項可用來設定自動化機器學習實驗。 將 `AutoMLConfig` 物件具現化即可設定這些參數。

部分範例包括：

1.  使用加權的 AUC 作為主要計量的分類實驗，每次反覆運算的時間上限為 12,000 秒，並且在 50 次反覆運算和 2 疊的交叉驗證之後結束實驗。

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  以下範例說明設定在 100 次反覆運算之後結束的迴歸實驗，每次反覆運算的持續時間上限為 600 秒，並執行 5 疊的交叉驗證。

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

下表列出您的實驗適用的參數設定及其預設值。

屬性 |  說明 | 預設值
--|--|--
`task`  |指定機器學習問題的類型。 允許的值包括 <li>分類</li><li>迴歸</li><li>預測</li>    | None |
`primary_metric` |您在建置模型時想要最佳化的計量。 例如，如果您將精確度指定為 primary_metric，則自動化機器學習會尋找具有最大精確度的模型。 在每個實驗中只能指定一個 primary_metric。 允許的值包括 <br/>**分類**：<br/><li> 精確度  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**迴歸**： <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score  </li> | 分類：精確度 <br/>迴歸：spearman_correlation <br/> |
`experiment_exit_score` |   您可以設定 primary_metric 的目標值。 一旦發現模型符合 primary_metric 目標，自動化機器學習即會停止反覆運算，且實驗會終止。 如果未設定此值 (預設值)，自動化機器學習實驗將會繼續依據反覆運算中指定的次數執行反覆運算。 採用雙精度浮點數值。 如果尚未達到目標，則自動化機器學習將會繼續執行，直到達到反覆運算中指定的反覆運算次數。| None
`iterations` |反覆運算次數上限。 每次反覆運算相當於一個產生管線的訓練作業。 管線是資料前置處理和模型。 若要取得高品質的模型，請使用 250 或更高的值    | 100
`max_concurrent_iterations`|    要平行執行的反覆運算次數上限。 這項設定僅適用於遠端計算。|   1
`max_cores_per_iteration`   | 指出在計算目標上將使用多少個核心來訓練單一管線。 如果演算法可以使用多個核心，這將可提高多核心電腦的效能。 您可以將它設定為 -1，以使用電腦上所有可用的核心。|  1
`iteration_timeout_minutes` |   限制特定反覆運算所花費的時間量 (分鐘)。 如果反覆運算超過指定的時間量，則會取消該反覆運算。 如果未設定，則反覆運算會繼續執行直到完成。 |   None
`n_cross_validations`   |交叉驗證分割數目| None
`validation_size`   |驗證集的大小，以所有訓練範例的百分比表示。|  None
`preprocess` | True/False <br/>True 會啟用實驗，以對輸入執行前置處理。 以下是前置處理的子集<li>遺漏資料：插補遺漏資料 - 平均數值，最常出現的文字 </li><li>類別值：如果資料類型是數值，而唯一值的數目小於 5%，則會轉換為 one-hot 編碼 </li><li>如需完整清單，請查看 [GitHub 存放庫](https://aka.ms/aml-notebooks)</li><br/>注意：如果是疏鬆資料，則無法使用 preprocess = true |  False |
`enable_cache`  | True/False <br/>設定為 True 會預先處理完成一次，然後針對所有反覆項目重複使用相同的已處理資料。 | True |
`blacklist_models`  | 自動化機器學習實驗中有許多它會嘗試的不同演算法。 設定自動化機器學習以從實驗中排除特定演算法。 如果您知道特定演算法不適用於您的資料集，則有所幫助。 排除演算法可為您節省計算資源和訓練時間。<br/>允許的分類值<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>允許的迴歸值<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>允許的預測值<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|   None
`whitelist_models`  | 自動化機器學習實驗中有許多它會嘗試的不同演算法。 設定要為實驗包含的特定演算法。 如果您知道演算法不適用於您的資料集，這有所幫助。 <br/>允許的分類值<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>允許的迴歸值<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>允許的預測值<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|  None
`verbosity` |控制記錄層級，INFO 最詳細，CRITICAL 最簡要。 詳細資訊層級會使用與在 Python 記錄套件中所定義相同的值。 允許的值包括：<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | 所有要用於訓練的特徵 |  None
`y` |   要用於訓練的標籤資料。 就分類而言，應為整數的陣列。|  None
`X_valid`|_選擇性_ 所有要用於驗證的特徵。 如果未指定，則 X 會分割至訓練和驗證之間 |   None
`y_valid`   |_選擇性_ 要用於驗證的標籤資料。 如果未指定，則 y 會分割至訓練和驗證之間    | None
`sample_weight` |   _選擇性_ 每個範例的加權值。 如果您想要為資料點指派不同的加權，則應使用 |   None
`sample_weight_valid`   |   _選擇性_ 每個驗證範例的加權值。 如果未指定，則 sample_weight 會分割至訓練和驗證之間   | None
`run_configuration` |   RunConfiguration 物件。  用於遠端回合。 |None
`data_script`  |    包含 get_data 方法的檔案路徑。  遠端回合的必要項目。   |None
`model_explainability` | _選擇性_ True/False <br/>  True 允許實驗為每個反覆項目執行特徵重要性。 您也可以在特定的反覆項目上使用 explain_model() 方法，在實驗完成後視需要為該反覆項目啟用特徵重要性。 | False
`enable_ensembling`|加註旗標以在所有其他反覆項目完成後啟用集體反覆項目。| True 
`ensemble_iterations`|在我們選擇要加入最後集體的適合管線期間的反覆項目數目。| 15
`experiment_timeout_minutes`| 限制整個實驗執行所需的時間 (分鐘) | None

## <a name="data-pre-processing-and-featurization"></a>資料預先處理與特徵化

如果您使用 `preprocess=True`，則系統會自動替您執行下列的資料前置處理步驟：
1.  卸除高基數或無變異數特徵
    * 將無實用資訊的特徵從定型與驗證集合中卸除。 其中包括遺漏所有值、所有資料列之間的值相同，或具有極高基數 (例如雜湊、識別碼或 GUID) 的特徵。
1.  遺失值插補
    *   針對數值特徵，使用資料行中平均值插補遺漏值。
    *   針對類別特徵，使用最頻繁的值插補遺漏值。
1.  產生其他特徵
    * 針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。
    * 針對文字特徵：根據單字母組、雙字母組和三字母組文字及計數向量化工具所得的詞彙頻率。
1.  轉換與編碼
    * 具有極少唯一值的數值特徵轉換成類別特徵。
    * 視類別特徵的基數而定，執行標籤編碼或 (雜湊) 或 one-hot 編碼。

## <a name="run-experiment"></a>執行實驗

提交實驗以執行與產生模型。 將 `AutoMLConfig` 傳至 `submit` 方法以產生模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>相依性會先安裝在新的機器上。  最多可能需要 10 分鐘才會顯示輸出。
>將 `show_output` 設定為 `True`，會使輸出顯示在主控台上。


## <a name="explore-model-metrics"></a>探索模型計量
如果您使用 Notebook，您可以在小工具中或以內嵌方式檢視結果。 請參閱[追蹤和評估模型](how-to-track-experiments.md#view-run-details)以取得更多詳細資料。


### <a name="classification-metrics"></a>分類計量
在每次反覆運算分類工作時會儲存下列計量。

|主要計量|說明|計算|額外的參數
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
norm_macro_recall|Normalized Macro Recall 是正常化的 Macro Recall，因此隨機效能的分數為 0，完美效能的分數為 1。 做法是 norm_macro_recall := (recall_score_macro - R)/(1 - R)，其中 R 是隨機預測其 recall_score_macro 的預期值 (亦即 R=0.5 代表二進位分類，R=(1/C) 代表 C 類別的分類問題)|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro" and then (recall_score_macro - R)/(1 - R)，其中 R 是隨機預測其 recall_score_macro 的預期值 (亦即 R=0.5 代表二進位分類，R=(1/C) 代表 C 類別的分類問題)|
precision_score_macro|Precision 是標示為真正在該類別中特定類別的元素百分比。 Macro 是每個類別其精確度的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision 是標示為真正在該類別中特定類別的元素百分比。 Micro 是透過計算真肯定和誤判的總數全域計算而得|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision 是標示為真正在該類別中特定類別的元素百分比。 Weighted 是每個類別的精確度，以每個類別中 true 執行個體數目為權重加權的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall 是真正在已正確標示的特定類別中的元素百分比。 Macro 是每個類別其召回率的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall 是真正在已正確標示的特定類別中的元素百分比。 Micro 是透過計算真肯定和誤否定的總數全域計算而得|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall 是真正在已正確標示的特定類別中的元素百分比。 Weighted 是每個類別的召回率，以每個類別中 true 執行個體數目為權重加權的算術平均值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Weighted accuracy 是給予每個範例的權重等於範例其 true 類別中 true 執行個體比例時的精確度|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是一種向量，等於目標中每個元素為該類別的比例|

### <a name="regression-and-forecasting-metrics"></a>迴歸和預測計量
下列計量儲存在迴歸或預測工作的每個反覆項目中。

|主要計量|說明|計算|額外的參數
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

## <a name="explain-the-model"></a>說明模型

雖然自動化機器學習功能一般可供使用，但**模型說明特徵仍處於公開預覽狀態。**

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

在 Azure 入口網站中，您可以視覺化工作區中的特徵重要性圖表。 在筆記本中使用 Jupyter 小工具時，此會顯示此圖表。 若要深入了解圖表，請參閱[範例 Azure ML 筆記本文章](samples-notebooks.md)。

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![特徵重要性圖形](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>後續步驟

深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

深入了解[如何使用自動化機器學習訓練分類模型](tutorial-auto-train-models.md)或[如何使用自動化機器學習對遠端資源進行訓練](how-to-auto-train-remote.md)。 
