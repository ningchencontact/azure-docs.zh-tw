---
title: 模型可解釋性
titleSuffix: Azure Machine Learning service
description: 了解如何說明您的模型執行預測使用 Azure 機器學習服務 SDK 的原因。 它可以用於定型和推斷期間了解您的模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/29/2019
ms.openlocfilehash: 4261e869fe17283886d7d8ea8101e03110d6dad4
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851984"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務的模型 interpretability

在本文中，您將了解如何解釋為什麼您的模型進行預測一樣 interpretability 套件的 Azure Machine Learning Python SDK。

此套件中使用的類別和方法，您可以取得：
+ Interpretability 大規模的實際資料集上定型和推斷期間。 
+ 若要協助您在訓練階段探索的資料與說明中的模式的互動式視覺效果
+ 功能重要性的值： 原始和工程設計的功能

在開發週期的訓練階段中，模型設計師和評估工具可以使用來建置信任的專案關係人說明模型的輸出。  他們也使用模型的深入解析進行偵錯、 驗證模型的行為符合其目標，並且檢查的偏差。

推斷，或模型計分，是階段已部署的模型用於預測，最常在實際執行資料的位置。 在這個階段，資料科學家可以解釋結果的預測，來使用您的模型的人員。 比方說，為什麼沒有模型拒絕抵押貸款，或預測投資組合會帶來更高的風險嗎？

使用這些供應項目，您可以說明機器學習服務模型**上的所有資料的全域**，或**在本機上的特定資料點**使用最新技術，以方便使用且可調整的方式。

Interpretability 類別都可以透過兩個 Python 套件。 了解如何[適用於 Azure Machine Learning 中安裝 SDK 封裝](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)主要封裝，其中包含 Microsoft 支援的功能。 

* `azureml.contrib.explain.model`預覽及您可以嘗試的實驗性功能。

> [!IMPORTANT]
> 未完全支援 contrib 中的項目。 隨著成熟的實驗性功能，它們會逐漸移到主要封裝。

## <a name="how-to-interpret-your-model"></a>如何解譯您的模型

您可以套用的 interpretability 類別和方法，以了解模型的通用行為或特定的預測。 前者會呼叫全域的說明，後者會呼叫本機說明。

可以根據此方法是否為模型無關或特定的模型也已分類方法。 某些方法的目標特定類型的模型。 比方說，SHAP 的樹狀結構說明只適用於樹狀結構為基礎的模型。 某些方法會將模型視為黑箱，例如模仿說明或 SHAP 的核心的說明。 `explain`套件利用這些不同的方法，根據資料集、 模型類型和使用案例。 

輸出是一份有關給定的模型如何進行其預測，例如：
* 全域/本機相對功能重要性

* 全域/本機功能，並預測關聯性

### <a name="explainers"></a>Explainers

有兩組 explainers:直接 Explainers 和中繼 Explainers SDK 中。

__直接 explainers__來自整合式的程式庫。 SDK 會包裝所有 explainers，使其公開的通用的 API 和輸出格式。 如果您可以更輕鬆地直接使用這些 explainers，您可以直接啟動它們而不是使用通用的 API，以及輸出格式。 以下是直接的 explainers SDK 中提供的清單：

* **樹狀結構說明**:SHAP 的樹狀結構說明，著重於多項式時間快速 SHAP 值估計演算法特定樹狀結構與整體的樹狀結構。
* **深入說明**:根據從 SHAP，深入說明的說明 」 是使用 DeepLIFT SHAP NIPS 篇文章所述，建置在連線上的高速的近似值演算法 SHAP 深度學習模型中的值。 TensorFlow 模型和使用 TensorFlow 後端的 Keras 模型支援 （另外還有 PyTorch 的初步支援） 」。
* **核心說明**:SHAP 的核心說明使用特別加權的本機線性迴歸，來估計 SHAP 值的任何模型。
* **模擬說明**:模擬的說明是以全域 surrogate 模型的概念為基礎。 全域 surrogate 模型是本質上可解譯的模型，經過定型，可盡量精確地預估黑色方塊模型的預測。 資料科學家可以解譯結論黑色方塊模型的相關的 surrogate 模型。
* **淺綠色說明**(`contrib`):根據淡黃綠色，淡黃綠色說明可以使用的新狀態本機解譯模型無關的說明 （淺綠色） 演算法來建立本機的 surrogate 模型。 不像全域 surrogate 模型中，淺綠色著重於定型本機 surrogate 模型說明個別的預測。
* **HAN 文字說明**(`contrib`):HAN 文字說明可用於階層式注意力網路從文字資料的模型說明取得在指定的黑色方塊文字模型。 我們訓練 HAN surrogate 模型，在給定的老師模型的預測的輸出。 全域在文字語料庫的定型之後, 我們已新增特定文件的 fine-tune 步驟，以改善說明的精確度。 HAN 用於雙向 RNN 其兩個注意層為句子和單字的注意。 一旦 DNN 是於教師模型定型並微調特定文件，我們可以擷取 word importances 從注意圖層。 我們發現 HAN 是更正確地比淺綠色或 SHAP 文字資料，但更昂貴的定型時間也在的條款。 不過，我們已改進定型時間藉由為使用者提供的選項初始化手套字組內嵌項目，與網路雖然仍然很慢。 在遠端 Azure GPU VM 上執行 HAN，並可以大幅提升定型時間。 HAN 實作述 「 階層式注意力網路文件分類 （Yang 要是，2016年） ' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf))。

__中繼 explainers__自動選取適當的直接說明，並產生根據給定的模型和資料集的最佳說明資訊。 中繼 explainers 運用所有的程式庫 （SHAP、 淺綠色、 模擬等），我們有整合式或開發。 SDK 中提供的中繼 explainers 如下：

* **表格式說明**:搭配使用表格式資料集。
* **文字說明**:搭配使用的文字資料集。

除了為中繼選取的直接 explainers、 中繼 explainers 開發其他功能的基礎程式庫及直接 explainers 中改善的速度和延展性。

目前`TabularExplainer`會採用下列邏輯來叫用直接 Explainers:

1. 如果它是樹狀結構為基礎的模型時，套用`TreeExplainer`、 其他
2. 如果它是 DNN 模型時，套用`DeepExplainer`、 其他
3. 將它視為黑箱模型，並套用 `KernelExplainer`

內建的智慧`TabularExplainer`會變成更複雜，因為多個程式庫都已整合至 SDK，而且我們了解每個說明的優缺點。

`TabularExplainer` 透過直接 Explainers 也將重要的功能和效能增強功能：

* **初始化資料集的摘要**。 在其中說明的速度是最重要的情況下，我們彙總初始化資料集，並產生較少的代表性的範例，以加快全域和本機說明。
* **評估資料集取樣**。 如果使用者會將一組大型評估範例中，但實際上不需要所有這些評估，就可以設定取樣參數為 true，以加速全球的說明。

下圖顯示兩組直接與中繼 explainers 之間的關聯性。

[![Machine Learning Interpretability 架構](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>支援的模型

任何在 Python 中的資料集定型的模型`numpy.array`， `pandas.DataFrame`， `iml.datatypes.DenseData`，或`scipy.sparse.csr_matrix`格式都受到 interpretability`explain`的 sdk 封裝。

說明函式會接受模型和管線做為輸入。 如果提供的模型，模型必須實作的預測函數`predict`或`predict_proba`符合 Scikit-learn 慣例。 如果提供的管線 （管線指令碼的名稱），則說明函式會假設執行的管線指令碼傳回的預測。

### <a name="local-and-remote-compute-target"></a>本機和遠端計算目標

`explain`套件設計來搭配兩個本機和遠端計算目標。 如果在本機執行 SDK 函式將不會連絡任何 Azure 服務。 您可以從遠端執行說明，在 Azure Machine Learning 計算，並登入 Azure Machine Learning 執行歷程記錄服務的說明資訊。 之後會記錄這些資訊，將可供使用，使用者分析的 Azure Machine Learning 工作區入口網站上報表和視覺效果的說明。

## <a name="interpretability-in-training"></a>在訓練 interpretability

### <a name="train-and-explain-locally"></a>定型，並在本機說明

1. 訓練您的模型，在本機的 Jupyter notebook。 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 呼叫的說明：若要初始化的說明物件，您需要將您的模型和一些訓練資料傳遞給說明的建構函式。 您可以選擇性地傳遞功能名稱和輸出類別名稱 （如果進行分類） 將用來將您的說明和視覺效果的更具參考價值。 以下是如何具現化並說明物件使用[TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)並[MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)在本機。 `TabularExplainer` 呼叫其中一個之下的三個 explainers (`TreeExplainer`， `DeepExplainer`，或`KernelExplainer`)，並會自動選取最適合您的使用案例。 不過，您可以直接呼叫每個其三個基礎 explainers。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    或
    
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. 取得通用的功能重要性的值。

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. 本機的功能重要性的值： 使用下列函式呼叫來說明個別的執行個體或執行個體群組。

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
    local_explanation = explainer.explain_local(x_test[0:4])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>定型，並說明遠端

雖然您可以在 Azure Machine Learning 服務支援的各種計算目標上進行訓練，這一節中的範例會示範如何使用 Azure Machine Learning 計算目標。

1. 在本機的 Jupyter notebook (比方說，run_explainer.py) 中的定型指令碼。

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine   
    # "features" and "classes" fields are optional 
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 遵循上的指示[設定用於定型模型的計算目標](how-to-set-up-training-targets.md#amlcompute)若要了解如何設定 Azure Machine Learning 計算作為計算目標並提交您的訓練執行。

3. 下載您的本機 Jupyter notebook 中的說明。 


    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>視覺效果

您可以使用視覺效果的儀表板來了解及詮釋您的模型：

### <a name="global-visualizations"></a>全域的視覺效果

下圖提供定型的模型，以及其預測和說明的全域檢視。

|繪圖|說明|
|----|-----------|
|資料探索| 資料集，以及預測值的概觀。|
|全域的重要性|全域顯示的最上層的 K (可設定 K) 很重要的功能。 此圖表適合用來了解通用行為之基礎模型。|
|說明探勘|示範如何一項功能負責在模型的預測值 （或預測值的機率） 進行變更。 |
|總結| 使用的所有資料點的帶正負號的局部特徵重要性值，以顯示預測值對每個功能影響的分佈。|

[![視覺效果儀表板全域](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本機的視覺效果
在上述的繪圖，載入指定的資料點的局部特徵重要性任何的繪圖時間，您可以按一下任何個別的資料點。

|繪圖|說明|
|----|-----------|
|本機的重要性|全域顯示的最上層的 K (可設定 K) 很重要的功能。 此圖表適合用來了解基礎模型的特定資料點的本機行為。|

[![視覺效果儀表板本機](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

若要載入的視覺效果儀表板，請使用下列程式碼：

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
``` 

## <a name="raw-feature-transformations"></a>未經處理的功能轉換

（選擇性） 您可以傳遞功能轉換管線來接收之前轉換 （而非經過工程設計的功能） 的未經處理的功能方面的說明說明。 如果您略過這個，說明會提供經過工程設計的功能方面的說明。 

中所述的其中一個支援的轉換的格式是相同[sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)。 一般情況下，只要它們在單一資料行上運作，因此清楚一對多，會支援任何轉換。

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>在 推斷的 interpretability

說明可以部署以及原始的模型，而且可以用在評分時間，來提供本機說明的資訊。 部署評分說明的程序類似於部署模型，並包含下列步驟：

1. 建立一個說明物件：
   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ``` 

1. 建立計分說明，使用說明物件：
   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ``` 

1. 設定和註冊映像，以使用評分說明模型。
   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ``` 

1. [選用]從雲端擷取評分的說明，並測試的說明
   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retreive the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 將映像部署到計算目標：

   1. 建立計分的檔案 (之前此步驟中，請依照下列中的步驟[部署與 Azure Machine Learning 服務的模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)註冊您的原始預測模型)
        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ``` 
    1. 定義部署組態 （此設定取決於您模型的需求。 下列範例會定義的設定，會使用一個 CPU 核心和 1 GB 記憶體）
        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                       memory_gb=1, 
                                                       tags={"data": "breastcancer",  
                                                             "method" : "local_explanation"}, 
                                                       description='Get local explanations for breast cancer data')
        ``` 

    1. 建立環境相依性的檔案

        ```python
        from azureml.core.conda_dependencies import CondaDependencies 

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"], 
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())
            
        with open("myenv.yml","r") as f:
            print(f.read())
        ``` 
    1. 使用 g + + 安裝建立自訂的 dockerfile

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++  
        ``` 
    1. 部署建立的映像 (估計時間：5 分鐘）
        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile", 
                                                        runtime="python", 
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
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

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ``` 

1. 清除：若要刪除已部署的 Web 服務，請使用 `service.delete()`。

## <a name="next-steps"></a>後續步驟

若要查看集合的 Jupyter notebook 會示範上述的指示，請參閱[Azure Machine Learning Interpretability 範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
