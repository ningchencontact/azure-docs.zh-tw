---
title: 如何使用現有的模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務的服務外部已定型的模型。 您可以註冊 Azure Machine Learning 服務，外部所建立的模型，並再將它們部署為 web 服務或 Azure IoT Edge 模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453680"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>如何使用 Azure Machine Learning 服務中的現有模型

了解如何使用現有的機器學習服務與 Azure Machine Learning 服務的模型。

如果您有機器學習 Azure Machine Learning 服務外部定型的模型，您仍然可以將模型部署為 web 服務或 IoT Edge 裝置中使用服務。 

> [!TIP]
> 這篇文章會提供基本資訊註冊及部署現有的模型。 一旦部署之後，Azure Machine Learning 服務會提供監視您的模型。 它也可讓您儲存輸入的資料傳送至部署，也可以用於資料漂移分析或培訓新模型的版本。
>
> 如需有關此處所使用的詞彙與概念的詳細資訊，請參閱 <<c0> [ 管理、 部署及監視機器學習服務模型](concept-model-management-and-deployment.md)。
>
> 如需部署程序的一般資訊，請參閱[部署與 Azure Machine Learning 服務的模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務工作區。 如需詳細資訊，請參閱 <<c0> [ 建立工作區](setup-create-workspace.md)。

    > [!TIP]
    > 這篇文章中的 Python 範例假設`ws`變數會設為您的 Azure 機器學習服務工作區。
    >
    > CLI 範例使用的預留位置`myworkspace`和`myresourcegroup`。 取代這些項目與您的工作區和包含它的資源群組的名稱。

* Azure Machine Learning SDK。 如需詳細資訊，請參閱 > 一節 Python SDK[建立工作區](setup-create-workspace.md#sdk)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)並[Machine Learning CLI 延伸模組](reference-azure-machine-learning-cli.md)。

* 已定型的模型。 模型必須保存在開發環境中的一個或多個檔案。

    > [!NOTE]
    > 若要示範 Azure Machine Learning 服務外部註冊模型定型，這篇文章中的範例程式碼片段會使用 Paolo Ripamonti Twitter 情感分析專案所建立的模型： [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)。

## <a name="register-the-models"></a>註冊模型

註冊模型，可讓您儲存的版本，與追蹤有關您工作區中的模型中繼資料。 在下列 Python 和 CLI 範例`models`目錄中包含`model.h5`， `model.w2v`， `encoder.pkl`，和`tokenizer.pkl`檔案。 此範例中所包含的檔案上傳`models`目錄，為新的模型註冊，名為`sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

如需詳細資訊，請參閱 < [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-)參考。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

如需詳細資訊，請參閱 < [az ml 模型註冊](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)參考。


如需有關註冊模型在一般情況下，請參閱[管理、 部署及監視機器學習服務模型](concept-model-management-and-deployment.md)。


## <a name="define-inference-configuration"></a>定義推斷組態

推斷組態會定義用來執行已部署的模型的環境。 推斷組態會參考執行模型，部署時所用的下列檔案：

* 執行階段。 執行階段的唯一有效的值目前是 Python。
* 項目指令碼。 這個檔案 (名為`score.py`) 會在已部署的服務啟動時載入模型。 它也會負責接收資料，將它傳遞到模型，然後傳回回應。
* Conda 環境檔案。 此檔案會定義執行模型和項目指令碼所需的 Python 套件。 

下列範例顯示使用 Python SDK 的基本推斷組態：

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

如需詳細資訊，請參閱 < [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)參考。

CLI 從 YAML 檔案載入推斷組態：

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

如需有關推斷組態的詳細資訊，請參閱[部署與 Azure Machine Learning 服務的模型](how-to-deploy-and-where.md)。

### <a name="entry-script"></a>項目指令碼

項目指令碼具有只有兩個必要的功能`init()`和`run(data)`。 這些函式用來初始化在啟動服務，並執行使用傳入的用戶端的要求資料的模型。 載入和執行模型，則會處理指令碼的其餘部分。

> [!IMPORTANT]
> 沒有適用於所有模型的一般項目指令碼。 您總是模型特定的使用。 它必須了解如何載入模型中，模型預期，資料格式，以及如何使用模型的資料進行計分。

下列 Python 程式碼是範例項目指令碼 (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

如需有關項目指令碼的詳細資訊，請參閱[部署與 Azure Machine Learning 服務的模型](how-to-deploy-and-where.md)。

### <a name="conda-environment"></a>Conda 環境

下列 YAML 會描述執行模型和項目指令碼所需的 conda 環境：

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
```

如需詳細資訊，請參閱 <<c0> [ 部署與 Azure Machine Learning 服務的模型](how-to-deploy-and-where.md)。

## <a name="define-deployment"></a>定義部署

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py)套件包含部署所用的類別。 您使用的類別會決定部署模型的位置。 例如，若要部署為 web 服務在 Azure Kubernetes Service 上，使用[AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-)以建立部署組態。

下列 Python 程式碼定義的本機部署的部署組態。 這項設定會將模型部署為 web 服務，以您的本機電腦。

> [!IMPORTANT]
> 本機部署需要安裝的[Docker](https://www.docker.com/)本機電腦上：

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

如需詳細資訊，請參閱 < [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)參考。

CLI 從 YAML 檔案載入部署組態：

```YAML
{
    "computeType": "LOCAL"
}
```

部署至不同的計算目標，例如在 Azure 雲端中，Azure Kubernetes Service 是簡單，只要變更部署組態。 如需詳細資訊，請參閱 <<c0> [ 方式及如何將模型部署](how-to-deploy-and-where.md)。

## <a name="deploy-the-model"></a>部署模型

下列範例會載入名為 「 已註冊模型的詳細資訊`sentiment`，然後將其部署為服務，名為`sentiment`。 在部署期間，推斷設定] 和 [部署設定會用來建立和設定服務環境：

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

如需詳細資訊，請參閱 < [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)參考。

若要部署的模型，CLI，使用下列命令。 此命令會部署第 1 版的已註冊的模型 (`sentiment:1`) 使用推斷和部署組態儲存在`inferenceConfig.json`和`deploymentConfig.json`檔案：

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

如需詳細資訊，請參閱 < [az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)參考。

如需有關部署的詳細資訊，請參閱 <<c0> [ 方式及如何將模型部署](how-to-deploy-and-where.md)。

## <a name="request-response-consumption"></a>要求-回應耗用量

在部署後，會顯示評分的 URI。 此 URI 可供用戶端提交要求給服務。 下列範例是基本的 Python 用戶端，將資料提交至服務，並顯示回應：

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

如需有關如何使用已部署的服務的詳細資訊，請參閱 <<c0> [ 建立的用戶端](how-to-consume-web-service.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [如何及在何處部署模型](how-to-deploy-and-where.md)
* [如何建立已部署模型的用戶端](how-to-consume-web-service.md)