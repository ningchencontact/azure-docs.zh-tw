---
title: 本機和遠端執行的模型 interpretability
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SDK 來說明您的模型為何會進行預測。 它可以在定型和推斷期間使用，以瞭解您的模型如何判斷特徵重要性並做出預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515300"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>本機和遠端執行的模型 interpretability

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用 Azure Machine Learning Python SDK 的 interpretability 套件，來說明您的模型為何進行預測。 您將瞭解下列工作：

* 解讀在本機和遠端計算資源上定型的機器學習模型
* 在 Azure 執行歷程記錄上儲存本機和全域說明
* 在[Azure Machine Learning studio](https://ml.azure.com)中觀看 interpretability 視覺效果
* 使用您的模型部署評分說明

若要深入瞭解模型 interpretability，請參閱[概念文章](how-to-machine-learning-interpretability.md)。

## <a name="local-interpretability"></a>本機 interpretability

下列範例會示範如何在本機使用解讀套件，而不需要聯絡 Azure 服務。 執行 `pip install azureml-interpret` 以取得 interpretability 套件。

1. 在本機 Jupyter 筆記本中訓練範例模型。

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

2. 在本機呼叫說明：若要初始化說明物件，您必須將您的模型和一些定型資料傳遞至說明的函式。 您也可以選擇性地傳入功能名稱和輸出類別名稱（如果執行分類），這將會用來讓您的說明和視覺效果更具資訊。 以下說明如何使用 `TabularExplainer`、`MimicExplainer`和在本機 `PFIExplainer`，將說明物件具現化。 `TabularExplainer` 會呼叫底下三個 SHAP explainers 的其中一個（`TreeExplainer`、`DeepExplainer`或 `KernelExplainer`），並自動為您的使用案例選取最適合的一個。 不過，您可以直接呼叫它的三個基礎 explainers。

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    或

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

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
    from interpret.ext.blackbox import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>整體（全域）功能重要性值

取得全域功能重要性值。
    
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

### <a name="instance-level-local-feature-importance-values"></a>實例層級（本機）功能重要性值

取得本機功能重要性值：使用下列函式呼叫來說明個別實例或實例群組。 請注意，PFIExplainer 不支援本機說明。

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>遠端執行的 Interpretability

這個範例示範如何使用 `ExplanationClient` 類別來啟用遠端執行的模型 interpretability。 概念與上一節類似，但您使用遠端執行中的 `ExplanationClient` 來上傳 interpretability 內容，然後您可以稍後在本機環境中下載內容。 使用 `pip install azureml-contrib-interpret` 來取得必要的套件。

1. 在本機 Jupyter 筆記本中建立定型腳本（例如，train_explain. .py）。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

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

1. 請遵循[設定模型定型的計算目標](how-to-set-up-training-targets.md#amlcompute)的指示，以瞭解如何設定 Azure Machine Learning 計算作為計算目標並提交定型回合。 您也可以查看[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)。

1. 在您的本機 Jupyter 筆記本中下載說明。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
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

## <a name="raw-feature-transformations"></a>原始功能轉換

（選擇性）您可以將功能轉換管線傳遞至說明（在 train_explain. .py 中），以在轉換之前（而非設計的功能）接收原始功能的說明。 如果您略過這種情況，說明會提供工程化功能的說明。

支援的轉換格式與[sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)中所述相同。 一般而言，只要作業在單一資料行上運作，就可以支援任何轉換，因此很明顯地會有一個。 

使用 `sklearn.compose.ColumnTransformer` 或合適的轉換器元組清單來說明原始功能。 下列程式碼使用 `sklearn.compose.ColumnTransformer`。 


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


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

如果您想要以合適的轉換器元組清單來執行範例，請使用下列程式碼。

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

## <a name="visualizations"></a>視覺效果

在您的本機 Jupyter 筆記本中下載說明之後，您可以使用 [視覺效果儀表板] 來瞭解並解讀您的模型。

### <a name="global-visualizations"></a>全域視覺效果

下圖提供已定型模型的全域觀點，以及其預測和說明。

|標|說明|
|----|-----------|
|資料探索| 資料集和預測值的總覽。|
|全球重要性|顯示全域的前 K 個（可設定的 K）重要功能。 此圖表適合用來瞭解基礎模型的全域行為。|
|說明探索|示範功能如何負責在模型的預測值（或預測值的機率）中進行變更。 它也會示範兩個功能如何互動，以影響預測。|
|摘要重要性| 在所有資料點上使用帶正負號的區域功能重要性值，以顯示每項功能對預測值的影響分佈。|

[![視覺效果儀表板全域](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本機視覺效果

在上圖中隨時按一下任何個別的資料點，以載入指定資料點的區域功能重要性圖。

|標|說明|
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
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

若要載入視覺效果儀表板，請使用下列程式碼。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning studio 中的視覺效果

藉由完成[遠端 interpretability](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs)一節中的步驟，您可以在[Azure Machine Learning studio](https://ml.azure.com)中檢查視覺效果儀表板。 Azure Machine Learning studio 中所顯示的儀表板是較簡單的視覺效果儀表板版本，並僅支援下列兩個索引標籤。

|標|說明|
|----|-----------|
|全球重要性|顯示全域的前 K 個（可設定的 K）重要功能。 此圖表適合用來瞭解基礎模型的全域行為。|
|摘要重要性| 在所有資料點上使用帶正負號的區域功能重要性值，以顯示每項功能對預測值的影響分佈。|

如果全域和本機說明都可以使用，這兩個索引標籤都會填入資料。 如果只有全域說明可供使用，則會停用第二個索引標籤。

若要存取 Azure Machine Learning studio 中的視覺效果儀表板，您可以流覽下列其中一個路徑：

1. 實驗索引標籤（預覽）：按一下 [實驗] 索引標籤，您會看到您在 Azure Machine Learning 服務上執行的實驗清單。 從該清單中，您可以選取要重新導向至具有所選實驗名稱下所有執行之頁面的特定實驗。 藉由按一下每個回合和其 [說明] 索引標籤，您會看到說明視覺效果儀表板。


[![視覺效果儀表板區域功能重要性](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. 模型索引標籤：如果您已使用 [使用[Azure Machine Learning 部署模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)] 中的步驟來註冊原始模型，您的模型就會顯示在 [模型] 索引標籤的清單中。藉由按一下每個模型和其 [說明] 索引標籤，您會看到說明視覺效果儀表板。

## <a name="interpretability-at-inference-time"></a>在推斷階段 Interpretability

說明可以與原始模型一起部署，並可在推斷時用來提供本機說明資訊。 我們也提供較輕量的評分 explainers，以在推斷階段改善 interpretability 的效能。 部署較輕量計分說明的程式類似于部署模型，並包含下列步驟：


1. 建立說明物件（例如，使用 TabularExplainer）：

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用說明物件建立評分說明：

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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
   from azureml.contrib.interpret.scoring.scoring_explainer import load

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

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

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

1. 清理：若要刪除已部署的 web 服務，請使用 `service.delete()`。

## <a name="next-steps"></a>後續步驟

若要深入瞭解模型 interpretability，請參閱[概念性文章](how-to-machine-learning-interpretability.md)。
