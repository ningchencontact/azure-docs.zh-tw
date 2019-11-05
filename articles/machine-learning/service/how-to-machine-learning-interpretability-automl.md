---
title: 自動化 ML 中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SDK 來說明自動化 ML 模型為何進行預測。 它可以在定型和推斷期間使用，以瞭解您的模型如何判斷特徵重要性並做出預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515326"
---
# <a name="model-interpretability-for-automated-ml-models"></a>自動化 ML 模型的模型 interpretability

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在此操作說明中，您將瞭解如何使用 Azure Machine Learning 服務在自動化機器學習服務中啟用 interpretability 功能。 自動化 ML 可讓您瞭解原始和工程功能的重要性。 若要使用模型 interpretability，請在 `AutoMLConfig` 物件中設定 `model_explainability=True`。  

在本文中，您將瞭解下列工作：

* 在最佳模型或任何模型的定型期間 Interpretability
* 啟用視覺效果以協助您探索資料和說明中的模式
* 在推斷或評分期間 Interpretability

## <a name="prerequisites"></a>必要條件

* 執行 `pip install azureml-interpret azureml-contrib-interpret` 以取得 interpretability 功能所需的套件。
* 本文假設您已瞭解如何建立自動化 ML 實驗。 請參閱[教學](tutorial-auto-train-models.md)課程或[如何](how-to-configure-auto-train.md)瞭解如何在 SDK 中使用自動化 ML。
 
## <a name="interpretability-during-training-for-the-best-model"></a>在訓練期間 Interpretability 最佳模型 

從 `best_run`取出說明，其中包含工程化功能和原始功能的說明。 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>從成品存放區下載設計的功能重要性

您可以使用 `ExplanationClient` 從 best_run 的成品存放區下載工程化的功能說明。 若要取得原始功能集 `raw=True`的說明。 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>在定型期間 Interpretability 任何模型 

在本節中，您將瞭解如何計算模型說明，並以視覺化方式呈現說明。 除了針對自動化 ML 模型取得現有的模型說明之外，您也可以使用不同的測試資料來說明您的模型。 下列步驟可讓您根據測試資料，計算並視覺化工程功能重要性和原始功能的重要性。

### <a name="retrieve-any-other-automl-model-from-training"></a>從定型取得任何其他 AutoML 模型

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>設定模型說明

Fitted_model 可以產生下列專案，這將用來取得使用 automl_setup_model_explanations 的工程化和原始功能說明：

* 從定型範例/測試範例特徵化資料
* 收集工程設計和原始功能名稱清單
* 在分類案例的標記資料行中尋找類別

Automl_explainer_setup_obj 包含上述清單中的所有結構。

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>初始化功能重要性的模擬說明

如需說明 AutoML 模型，請使用 `MimicWrapper` 類別。 MimicWrapper 可以使用說明安裝物件的參數、您的工作區，以及做為代理模型來說明自動化 ML 模型（fitted_model 這裡）的 LightGBM 模型來初始化。 MimicWrapper 也會採用將上傳原始和工程說明的 automl_run 物件。

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>使用 MimicExplainer 進行運算，並以視覺化方式設計功能重要性

您可以使用已轉換的測試範例來呼叫 MimicWrapper 中的說明（）方法，以取得所產生之工程化功能的功能重要性。 您也可以使用 ExplanationDashboard 來查看自動 ML 有所產生之工程化功能的功能重要性值的虛線面板視覺效果。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>使用模擬說明來計算和視覺化原始功能重要性

MimicWrapper 中的說明（）方法可以再次使用已轉換的測試範例來呼叫，並將 `get_raw` 設定為 True，以取得原始功能的功能重要性。 您也可以使用 ExplanationDashboard 來查看原始功能的功能重要性值的儀表板視覺效果。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>推斷期間的 Interpretability

在本節中，您將瞭解如何使用說明來讓自動化 ML 模型，這是用來計算上一節中的說明。

### <a name="register-the-model-and-the-scoring-explainer"></a>註冊模型和評分說明

使用 `TreeScoringExplainer` 來建立評分說明，這將用來在推斷階段計算原始和工程化功能的重要性值。 請注意，您會使用先前所計算的 feature_map 來初始化評分說明。 評分說明會使用 feature_map 來傳回原始功能重要性。

在下列程式碼中，您會儲存評分說明，並向模型管理服務註冊模型和評分說明。

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>建立用於設定服務的 conda 相依性

接下來，您會在容器中建立所部署模型所需的環境相依性。

```python
from azureml.core.conda_dependencies import CondaDependencies 

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>部署服務

使用 conda 檔案和先前步驟中的評分檔案來部署服務。

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>使用測試資料的推斷

使用一些測試資料來推斷，以查看自動化 ML 模型中的預測值，並查看預測值的工程功能重要性和預測值的原始特徵重要性。

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>視覺效果，可協助您探索資料中的模式，以及定型時間的說明

您也可以在[Azure Machine Learning studio](https://ml.azure.com)中，將工作區中的功能重要性圖表視覺化。 當您的自動化 ML 執行完成之後，您必須按一下 [視圖模型詳細資料]，這將會帶您前往特定執行。 在這裡，您可以按一下 [說明] 索引標籤，以查看說明視覺效果儀表板。 

[![Machine Learning Interpretability 架構](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>後續步驟

如需如何在自動化機器學習以外的 SDK 其他區域中啟用模型說明和功能重要性的詳細資訊，請參閱 interpretability 上的[概念](how-to-machine-learning-interpretability.md)文章。