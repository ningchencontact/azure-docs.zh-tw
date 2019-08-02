---
title: 搭配 Azure Machine Learning 服務使用 MLflow
titleSuffix: Azure Machine Learning service
description: 使用 MLflow 搭配 Azure Machine Learning 服務, 記錄計量和成品, 並將模型部署至生產環境。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 55722c35dddcbf8a20f4f51958170938225e87e5
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668396"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-service-preview"></a>使用 MLflow 和 Azure Machine Learning 服務追蹤計量和部署模型 (預覽)

本文示範如何使用 Azure Machine Learning 服務來啟用 MLflow 的追蹤 URI 和記錄 API, 統稱為[MLflow 追蹤](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)。 這麼做可讓您:

+ 在您的[Azure Machine Learning 服務工作區](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces)中追蹤並記錄實驗計量和成品。 如果您已針對實驗使用 MLflow 追蹤, 工作區會提供集中式、安全且可擴充的位置, 以儲存您的定型計量和模型。

+ 將您的 MLflow 實驗部署為 Azure Machine Learning web 服務。 藉由部署為 web 服務, 您可以將 Azure Machine Learning 監視和資料漂移偵測功能套用至生產環境模型。 

[MLflow](https://www.mlflow.org)是一個開放原始碼程式庫, 可用於管理機器學習實驗的生命週期。 MLFlow 追蹤是 MLflow 的元件, 可記錄及追蹤您的定型執行計量和模型成品, 無論您的實驗環境是在本機、在虛擬機器、遠端計算叢集上, 甚至 Azure Databricks。

![使用 azure machine learning mlflow 的圖表](media/how-to-use-mlflow/mlflow-diagram-track.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比較 MLflow 和 Azure Machine Learning 用戶端

 下表摘要說明可以使用 Azure Machine Learning 服務的不同用戶端, 以及其各自的函數功能。

 MLflow 追蹤提供僅透過[Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)提供的計量記錄和成品儲存功能。


| | MLflow 追蹤 & 部署 | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure 入口網站|
|---|---|---|---|---|
| 管理工作區 |   | ✓ | ✓ | ✓ |
| 使用資料存放區  |   | ✓ | ✓ | |
| 記錄計量      | ✓ | ✓ |   | |
| 上傳構件 | ✓ | ✓ |   | |
| 檢視計量     | ✓ | ✓ | ✓ | ✓ |
| 管理計算   |   | ✓ | ✓ | ✓ |
| 部署模型    | ✓ | ✓ | ✓ | ✓ |
|監視模型效能||✓|  |   |
| 偵測資料漂移 |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>先決條件

* [安裝 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* [在您的本機電腦上安裝 Azure Machine Learning PYTHON SDK, 並建立 Azure Machine Learning 工作區](setup-create-workspace.md#sdk)。 SDK 會提供 MLflow 的連線能力, 以存取您的工作區。

## <a name="track-experiment-runs"></a>追蹤實驗執行

具有 Azure Machine Learning 服務的 MLflow 追蹤可讓您將記錄的計量和成品從本機和遠端執行儲存至 Azure Machine Learning 工作區。

### <a name="local-runs"></a>本機執行

`azureml-contrib-run`安裝套件, 以在本機執行于 Jupyter Notebook 或程式碼編輯器的實驗中, 使用 MLflow 追蹤搭配 Azure Machine Learning。

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Contrib 命名空間經常變更, 因為我們會進行改善服務的工作。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

匯入[`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)和類別以存取 MLflow 的追蹤 URI, 並設定您的工作區。 `mlflow`

在下列程式碼中, `get_mlflow_tracking_uri()`方法會將唯一的追蹤 uri 位址指派給工作`ws`區, `set_tracking_uri()`並將 MLflow 追蹤 uri 指向該位址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>追蹤 URI 的有效時間上限為一小時或更短。 如果您在某些閒置時間之後重新開機腳本, 請使用 get_mlflow_tracking_uri API 來取得新的 URI。

使用`set_experiment()`設定 MLflow 實驗名稱, 並開始執行您的`start_run()`定型。 然後使用`log_metric()`來啟用 MLflow 記錄 API, 並開始記錄您的定型執行計量。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

### <a name="remote-runs"></a>遠端執行

遠端執行可讓您以更強大的計算 (例如啟用 GPU 的虛擬機器, 或 Machine Learning Compute 叢集) 來定型模型。 如需瞭解不同的計算選項, 請參閱[設定模型定型的計算目標](how-to-set-up-training-targets.md)。

使用[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)類別來設定您的計算和訓練執行環境。 在`mlflow`環境`azure-contrib-run` [的`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)區段中包含和 pip 套件。 然後使用[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)您的遠端計算來進行結構為計算目標。

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

在定型腳本中, 匯`mlflow`入以使用 MLflow 記錄 api, 並開始記錄執行計量。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

使用此計算和定型回合設定時, 請`Experiment.submit('train.py')`使用方法來提交執行。 這會自動設定 MLflow 追蹤 URI, 並將記錄從 MLflow 導向至您的工作區。

```Python
run = exp.submit(src)
```

### <a name="mlflow-with-azure-databricks-runs"></a>Azure Databricks 執行的 MLflow

若要使用 Azure Databricks 執行您的 Mlflow 實驗, 您必須先建立[Azure Databricks 工作區和](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)叢集。 在您的叢集中, 請務必從 PyPi 安裝*azureml mlflow*程式庫, 以確保您的叢集能夠存取必要的函式和類別。

#### <a name="install-libraries"></a>安裝程式庫

若要在叢集上安裝程式庫, 請流覽至 連結**庫** 索引標籤, 然後按一下 **安裝新**

 ![使用 azure machine learning mlflow 的圖表](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

在 [**封裝**] 欄位中, 輸入 azureml-mlflow, 然後按一下 [安裝]。 視需要重複此步驟, 將其他套件安裝到您的叢集以進行實驗。

 ![使用 azure machine learning mlflow 的圖表](media/how-to-use-mlflow/install-libraries.png)

#### <a name="notebook-and-workspace-set-up"></a>筆記本和工作區設定

一旦您的叢集設定完成, 請匯入您的實驗筆記本, 開啟它, 並將您的叢集附加到其中。

下列程式碼應該位於您的實驗筆記本中。 這會取得您的 Azure 訂用帳戶的詳細資料來具現化您的工作區。 這假設您有現有的資源群組和 Azure Machine Learning 工作區, 否則您可以[建立它們](setup-create-workspace.md#portal)。 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

```
#### <a name="set-mlflow-tracking-uri"></a>設定 MLflow 追蹤 URI
在您具現化工作區之後, 請設定 MLflow 追蹤 URI。 如此一來, 您就可以將 MLflow 追蹤連結到 Azure Machine Learning 工作區。 在此之後, 您的所有實驗都會進入受控 Azure Machine Learning 追蹤服務。

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在定型腳本中, 匯入 mlflow 以使用 MLflow 記錄 Api, 並開始記錄執行計量。 下列範例會記錄 epoch 遺失度量。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>在您的工作區中查看計量和成品

MLflow 記錄中的計量和成品會保留在您的工作區中。 若要隨時查看, 請流覽至您的工作區, 然後在[Azure 入口網站](https://portal.azure.com)上依名稱尋找實驗, 或執行下列程式碼。 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>將 MLflow 模型部署為 web 服務

將您的 MLflow 實驗部署為 Azure Machine Learning web 服務, 可讓您利用 Azure Machine Learning 模型管理和資料漂移偵測功能, 並將其套用至您的生產環境模型。

![使用 azure machine learning mlflow 的圖表](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>記錄您的模型
在我們可以部署之前, 請確定您的模型已儲存, 讓您可以參考它及其路徑位置以進行部署。 在定型腳本中, 應該會有類似下列 mlflow 的程式碼: [sklearn _model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html)方法, 將您的模型儲存至指定的輸出目錄。 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> `conda_env`包含參數, 以傳遞要在其中執行此模型之相依性和環境的字典標記法。

### <a name="retrieve-model-from-previous-run"></a>從上一次執行取出模型

若要取得所需的執行, 我們需要在儲存模型之位置的執行歷程記錄中的執行識別碼和路徑。 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>建立 Docker 映射

`mlflow.azureml.build_image()`函式會以架構感知的方式, 從儲存的模型建立 Docker 映射。 它會自動建立架構特定的推斷包裝函式程式碼, 並為您指定套件相依性。 指定模型路徑、您的工作區、執行識別碼和其他參數。

在下列程式碼中, 我們會使用執行: */< 執行 >/model*來建立 docker 映射, 做為 scikit-learn 學習實驗的 model_uri 路徑。

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
建立 Docker 映射可能需要幾分鐘的時間。 

### <a name="deploy-the-docker-image"></a>部署 Docker 映射 

建立映射之後, 請使用 Azure Machine Learning SDK 將映射部署為 web 服務。

首先, 指定部署設定。 Azure 容器實例 (ACI) 是適用于快速開發/測試部署的理想選擇, 而 Azure Kubernetes Service (AKS) 適用于可調整的生產環境部署。

#### <a name="deploy-to-aci"></a>部署到 ACI

使用[deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)方法來設定您的部署設定。 您也可以新增標記和描述, 以協助追蹤您的 web 服務。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

然後, 使用 Azure Machine Learning SDK 的[deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-)方法部署映射。 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>部署到 AKS

若要部署至 AKS, 您需要建立 AKS 叢集, 並將您要部署的 Docker 映射帶入其中。 在此範例中, 我們會從 ACI 部署引進先前建立的映射。

為了從先前的 ACI 部署取得映射, 我們使用[image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py)類別。 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

建立 AKS compute 可能需要20-25 分鐘的時間來建立新的叢集

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
使用[deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)方法來設定您的部署設定。 您也可以新增標記和描述, 以協助追蹤您的 web 服務。

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

然後, 使用 Azure Machine Learning SDK 的[deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-)方法部署映射。 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

服務部署可能需要幾分鐘的時間。

## <a name="clean-up-resources"></a>清除資源

如果您不打算在工作區中使用已記錄的計量和成品, 則無法個別刪除它們。 相反地, 請刪除包含儲存體帳戶和工作區的資源群組, 這樣您就不會產生任何費用:

1. 在 Azure 入口網站中，選取最左邊的 [資源群組]。

   ![在 Azure 入口網站中刪除](media/how-to-use-mlflow/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 選取 [刪除資源群組]。

1. 輸入資源群組名稱。 然後選取 [刪除]。


## <a name="example-notebooks"></a>Notebook 範例

[使用 AZURE ML 筆記本的 MLflow](https://aka.ms/azureml-mlflow-examples)會示範並擴充本文中顯示的概念。

## <a name="next-steps"></a>後續步驟
* [管理您的模型](concept-model-management-and-deployment.md)。
* 監視您的生產模型, 以進行[資料漂移](how-to-monitor-data-drift.md)。
