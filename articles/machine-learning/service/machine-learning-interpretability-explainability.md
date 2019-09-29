---
title: 模型可解釋性
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SDK 來說明您的模型為何會進行預測。 它可以在定型和推斷期間用來瞭解模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 703416d2df103c1ac8db06cbef43600058c250ef
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350462"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>具有 Azure Machine Learning 的模型 interpretability

在本文中，您將瞭解如何使用 Azure Machine Learning Python SDK 的各種 interpretability 套件，來說明您的模型為何進行預測。

使用 SDK 中的類別和方法，您可以取得：
+ 原始和工程功能的功能重要性值
+ 在定型和推斷期間，大規模 Interpretability 真實世界的資料集。
+ 互動式視覺效果可協助您探索資料中的模式，以及定型時間的說明

在開發週期的定型階段中，模型設計師和評估人員可以使用模型的 interpretability 輸出來驗證假設，並與專案關係人建立信任關係。  它們也會使用模型中的深入解析來進行偵錯工具、驗證模型行為是否符合其目標，以及檢查偏差。

在機器學習中，**功能**是用來預測目標資料點的資料欄位。 例如，若要預測信用風險，可能會使用年齡、帳戶大小和帳戶存留期的資料欄位。 在此情況下，年齡、帳戶大小和帳戶存留期都是**功能**。 功能重要性告訴您每個資料欄位如何影響模型的預測。 例如，當帳戶大小和年齡不會大幅影響預測精確度時，在預測中可能會耗用大量時間。 此程式可讓資料科學家說明產生的預測，讓專案關係人能夠看到模型中最重要的資料點。

使用這些工具，您可以使用簡單易用且可調整的方式，在**所有資料上**或在**特定資料點上**，以最先進的技術來說明機器學習模型。

Interpretability 類別可透過多個 SDK 套件取得。 瞭解如何[安裝適用于 Azure Machine Learning 的 SDK 套件](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)，主要封裝包含 Microsoft 支援的功能。

* `azureml.contrib.explain.model`，您可以嘗試的、預覽和實驗性功能。

* `azureml.train.automl.automlexplainer`用來解讀自動化機器學習模型的封裝。

> [!IMPORTANT]
> 未完全支援`contrib`命名空間中的內容。 當實驗性功能變得成熟時，它們會逐漸移至主要命名空間。

## <a name="how-to-interpret-your-model"></a>如何解讀您的模型

您可以套用 interpretability 類別和方法，以瞭解模型的全域行為或特定的預測。 前者稱為「全域說明」，後者稱為「本機說明」。

方法也可以根據該方法是模型中立或模型特定來分類。 某些方法會以特定類型的模型為目標。 例如，SHAP 的樹狀說明僅適用于以樹狀結構為基礎的模型。 某些方法會將模型視為黑色方塊，例如模擬說明或 SHAP 的核心說明。 此`explain`套件會根據資料集、模型類型和使用案例來運用這些不同的方法。

輸出是一組有關給定模型如何進行預測的資訊，例如：
* 全域/本機相對功能重要性

* 全域/本機功能和預測關聯性

### <a name="explainers"></a>Explainers

有兩組 explainers：SDK 中的直接 Explainers 和中繼 Explainers。

__Direct explainers__來自整合式程式庫。 SDK 會包裝所有 explainers，使其公開通用 API 和輸出格式。 如果您比較習慣直接使用這些 explainers，您可以直接叫用它們，而不是使用常見的 API 和輸出格式。 以下是 SDK 中可用的直接 explainers 清單：

* **SHAP 樹狀說明**：SHAP 的樹狀說明，著重于多項式時間快速 SHAP 價值估計演算法，適用于樹狀結構和整體的樹系。
* **SHAP Deep 說明**：根據 SHAP 的說明，深度說明」是一種高速的近似值演算法，可在深度學習模型中，以 SHAP NIPS 檔中所述的 DeepLIFT 連接為基礎來 SHAP 值。 支援使用 TensorFlow 後端的 TensorFlow 模型和 Keras 模型（也有 PyTorch 的初步支援）」。
* **SHAP 核心說明**：SHAP 的核心說明會使用特殊加權的區域線性回歸來估計任何模型的 SHAP 值。
* 模擬**說明**：模擬說明是以全域代理模型的概念為基礎。 全域代理模型是一個本質上的可解譯模型，它會定型以盡可能精確地大致估計黑色方塊模型的預測。 資料科學家可以解讀代理模型，以繪製有關黑色箱模型的結論。 您可以使用下列其中一個可解譯模型做為您的代理模型：LightGBM （LinearExplainableModel）、線性回歸（LinearExplainableModel）、隨機梯度下降 explainable 模型（SGDExplainableModel）和決策樹（DecisionTreeExplainableModel）。


* **排列功能重要性說明**：排列功能重要性是一種技術，用來說明[Breiman 的隨機](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf)樹系紙張所能啟發的分類和回歸模型（請參閱第10節）。 概括而言，它的運作方式是針對整個資料集，一次隨機亂數據一項功能，並計算感利率的效能度量量會降低。 變更愈大，此功能的重要性就愈高。

* **酸橙說明**(`contrib`):以酸橙色的說明為依據，會使用最新的當地可解譯模型說明（酸）演算法來建立本機代理模型。 不同于全域代理模型，「酸」會著重于訓練本機代理模型來說明個別的預測。
* **漢字文字說明**(`contrib`):漢字文字說明會使用階層式的注意網路，從文字資料取得給定黑色方塊文字模型的模型說明。 我們會在指定的老師模型預測輸出上訓練漢字代理模型。 在文字主體上全域定型之後，我們為特定檔新增了微調步驟，以改善說明的正確性。 漢字使用雙向 RNN 和兩個注意層，以進行句子和單字注意。 一旦 DNN 在老師模型上定型，並在特定檔上進行微調，我們就可以從注意層將 importances 一詞摘錄出來。 我們發現漢字比文字資料的酸綠色或 SHAP 更精確，但是在定型時間也更昂貴。 不過，我們已改善定型時間，方法是讓使用者選擇使用手套 word 內嵌來初始化網路，雖然它仍然緩慢。 藉由在遠端 Azure GPU VM 上執行漢字，可以大幅改善定型時間。 漢字的執行會在「[檔分類的階層式注意網路（最強烈 et al，2016）](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)」中說明。


__Meta explainers__會自動選取適當的直接說明，並根據給定的模型和資料集產生最佳的說明資訊。 Meta explainers 會運用我們已整合或開發的所有程式庫（SHAP、酸橙、模擬等等）。 下列是 SDK 中可用的中繼 explainers：

* **表格式說明**：與表格式資料集搭配使用。
* **文字說明**：與文字資料集搭配使用。
* **映射說明**：與影像資料集搭配使用。

除了直接 explainers 的中繼選擇外，中繼 explainers 也會在基礎程式庫之上開發額外的功能，並透過直接 explainers 改善速度和擴充性。

目前`TabularExplainer`採用下列邏輯來叫用 Direct SHAP Explainers：

1. 如果它是以樹狀結構為基礎的模型， `TreeExplainer`請套用 SHAP，否則
2. 如果它是 DNN 模型，請套用 SHAP `DeepExplainer`，否則
3. 將它視為黑色箱模型並套用 SHAP`KernelExplainer`

內建`TabularExplainer`的智慧功能會變得更複雜，因為更多程式庫會整合到 SDK 中，我們將瞭解每個說明的優缺點。

`TabularExplainer`也透過直接 Explainers 進行了重大的功能和效能增強：

* **初始化資料集的摘要**。 在說明速度最重要的情況下，我們會摘要初始設定資料集，並產生一小組代表性的範例，以加速全域和本機的說明。
* **取樣評估資料集**。 如果使用者傳入大量的評估範例，但實際上並不需要進行評估，則可以將取樣參數設定為 true，以加速全域說明。

下圖顯示直接和中繼 explainers 的目前結構。

[![Machine Learning Interpretability 架構](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支援的模型

SDK `numpy.array`的 interpretability `iml.datatypes.DenseData` `scipy.sparse.csr_matrix` `pandas.DataFrame`套件`explain`支援任何在 Python、、或格式的資料集上定型的模型。

說明函式接受模型和管線做為輸入。 如果提供模型，則模型必須實作用或`predict` `predict_proba`符合 scikit-learn 慣例的預測函數。 如果提供管線（管線腳本的名稱），說明函式會假設執行中的管線腳本傳回預測。 我們支援透過 PyTorch、TensorFlow 和 Keras 深度學習架構訓練的模型。

### <a name="local-and-remote-compute-target"></a>本機和遠端計算目標

`explain`封裝是設計來搭配本機和遠端計算目標使用。 如果在本機執行，SDK 函式將不會與任何 Azure 服務連線。 您可以在 Azure Machine Learning 計算中從遠端執行說明，並將說明資訊記錄到 Azure Machine Learning 執行歷程記錄服務。 記錄這項資訊之後，說明中的報告和視覺效果就會立即在 Azure Machine Learning 工作區入口網站上提供，供使用者分析之用。

## <a name="interpretability-in-training"></a>訓練中的 Interpretability

### <a name="train-and-explain-locally"></a>在本機定型和說明

1. 在本機 Jupyter 筆記本中訓練您的模型。

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 呼叫說明：若要初始化說明物件，您必須將您的模型和一些定型資料傳遞至說明的函式。 您也可以選擇性地傳入功能名稱和輸出類別名稱（如果執行分類），這將會用來讓您的說明和視覺效果更具資訊。 以下是在本機使用[TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)、 [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)和[PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py)來具現化說明物件的方式。 `TabularExplainer`呼叫（`TreeExplainer`、 `DeepExplainer`或`KernelExplainer`）底下三個 SHAP explainers 的其中一個，並自動為您的使用案例選取最適合的一個。 不過，您可以直接呼叫它的三個基礎 explainers。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    或

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel,SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   或

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. 取得全域功能重要性值。

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. 取得本機功能重要性值：使用下列函式呼叫來說明個別實例或實例群組。 請注意，PFIExplainer 不支援本機說明。

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    或

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:5])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>遠端訓練和說明

雖然您可以在 Azure Machine Learning 支援的各種計算目標上進行定型，但本節中的範例會示範如何使用 Azure Machine Learning 計算目標來執行這項操作。

1. 在本機 Jupyter 筆記本中建立定型腳本（例如，run_explainer. .py）。

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 遵循[設定模型定型的計算目標](how-to-set-up-training-targets.md#amlcompute)中的指示，以瞭解如何設定 Azure Machine Learning 計算作為計算目標，並提交定型回合。

3. 在您的本機 Jupyter 筆記本中下載說明。

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>視覺效果

使用 [視覺效果儀表板] 來瞭解並解讀您的模型：

### <a name="global-visualizations"></a>全域視覺效果

下圖提供已定型模型的全域觀點，以及其預測和說明。

|繪圖|描述|
|----|-----------|
|資料探索| 資料集和預測值的總覽。|
|全球重要性|顯示全域的前 K 個（可設定的 K）重要功能。 此圖表適合用來瞭解基礎模型的全域行為。|
|說明探索|示範功能如何負責在模型的預測值（或預測值的機率）中進行變更。 |
|總結| 在所有資料點上使用帶正負號的區域功能重要性值，以顯示每項功能對預測值的影響分佈。|

[![視覺效果儀表板全域](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本機視覺效果

您可以在上一個繪圖的任何時間按一下任何個別的資料點，以載入指定資料點的區域功能重要性圖。

|繪圖|描述|
|----|-----------|
|本機重要性|顯示全域的前 K 個（可設定的 K）重要功能。 此圖表有助於瞭解特定資料點上基礎模型的本機行為。|
|Perturbation 探索|可讓您變更所選資料點的功能值，並觀察這些變更將如何影響預測值。|
|個別條件式預期（ICE）| 可讓您將最小值的功能值變更為最大值，以查看當功能變更時，資料點的預測如何變更。|

[![視覺效果儀表板區域功能重要性](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![視覺效果儀表板功能 Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![視覺效果儀表板 ICE 繪圖](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

注意：啟動 Jupyter 核心之前，您必須先啟用視覺效果儀表板的 widget 延伸模組。

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
若要載入視覺效果儀表板，請使用下列程式碼：

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>原始功能轉換

（選擇性）您可以將功能轉換管線傳遞至說明，以在轉換之前（而非設計的功能）接收原始功能的說明。 如果您略過這種情況，說明會提供工程化功能的說明。

支援的轉換格式與[sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)中所述相同。 一般而言，只要作業在單一資料行上運作，就可以支援任何轉換，因此很明顯地會有一個。 

我們可以使用`sklearn.compose.ColumnTransformer`或合適的轉換器元組清單來說明原始功能。 下面的儲存格`sklearn.compose.ColumnTransformer`使用。 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

如果您想要以合適的轉換器元組清單來執行範例，請使用下列程式碼： 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>在推斷階段 Interpretability

說明可以與原始模型一起部署，並可在計分時間用來提供本機說明資訊。 我們也提供較輕量的評分 explainers，讓 interpretability 在推斷時間更具效能。 部署較輕量計分說明的程式類似于部署模型，並包含下列步驟：




1. 建立說明物件（例如，使用 TabularExplainer）：

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用說明物件建立評分說明：

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 設定並註冊使用評分說明模型的影像。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 選擇性從雲端取出評分說明並測試說明

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 將映射部署到計算目標：

   1. 建立評分檔案（在此步驟之前，請遵循[使用 Azure Machine Learning 部署模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)中的步驟來註冊您的原始預測模型）

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. 定義部署設定（此設定取決於模型的需求而定）。 下列範例會定義使用一個 CPU 核心和 1 GB 記憶體的設定

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. 建立具有環境相依性的檔案

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. 建立已安裝 g + + 的自訂 dockerfile

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 部署已建立的映射（時間估計：5分鐘）

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. 測試部署

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 清除：若要刪除已部署的 Web 服務，請使用 `service.delete()`。




## <a name="next-steps"></a>後續步驟

若要查看示範上述指示的 Jupyter 筆記本集合，請參閱[Azure Machine Learning Interpretability 範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
