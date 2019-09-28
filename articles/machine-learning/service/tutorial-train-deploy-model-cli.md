---
title: 從 CLI 訓練和部署模型
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用適用于 Azure CLI 的機器學習擴充功能，從命令列定型、註冊和部署模型。
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb46aaf04535c1b44cdd80810fbb6382dc727a67
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350416"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>教學課程：從 CLI 訓練和部署模型

在本教學課程中，您會使用適用于 Azure CLI 的機器學習服務延伸模組來定型、註冊和部署模型。

本教學課程中的 Python 訓練腳本使用[scikit-learn-學習](https://scikit-learn.org/)來訓練基本模型。 本教學課程的重點不在腳本或模型上，而是使用 CLI 來處理 Azure Machine Learning。

了解如何執行下列動作：

> [!div class="checklist"]
> * 安裝機器學習擴充功能
> * 建立 Azure Machine Learning 工作區
> * 建立用來定型模型的計算資源
> * 開始執行訓練
> * 註冊並下載模型
> * 將模型部署為 Web 服務
> * 使用 web 服務評分資料

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要在您的**本機環境**中使用本檔中的 CLI 命令，您需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果您使用[Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，CLI 就會透過瀏覽器存取，並存在於雲端中。

## <a name="download-the-example-project"></a>下載範例專案

在本教學課程中，請下載[@no__t 1](https://github.com/microsoft/MLOps)專案。 本教學課程中的步驟會使用 `model-training` 和 @no__t 1 目錄中的檔案。

若要取得檔案的本機複本，請[下載 .zip](https://github.com/microsoft/MLOps/archive/master.zip)封存，或使用下列 Git 命令來複製存放庫：

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>定型檔案

@No__t-0 目錄包含下列檔案，這些檔案會在定型模型時使用：

* `.azureml\sklearn.runconfig`:__執行配置__檔。 這個檔案會定義定型模型所需的執行時間環境。
* `train-sklearn.py`:定型腳本。 此檔案會訓練模型。
* `mylib.py`:由 `train-sklearn.py` 使用的 helper 模組。
* `training-env.yml`:定義執行定型腳本所需的軟體相依性。

定型腳本會使用 scikit-learn 所提供的糖尿病資料集-學習來定型模型。

### <a name="deployment-files"></a>部署檔案

@No__t-0 目錄包含下列檔案，這些檔案可用來將定型的模型部署為 web 服務：

* `aciDeploymentConfig.yml`:__部署配置__檔。 這個檔案會定義模型所需的主控環境。
* `inferenceConfig.yml`:推斷 configuration__ 檔。 此檔案會定義服務用來以模型評分資料的軟體環境。
* `score.py`:可接受傳入資料的 python 腳本，使用模型對其進行分數，然後傳迴響應。
* `scoring-env.yml`:執行模型和 `score.py` 腳本所需的 conda 相依性。

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶

有數種方式可讓您從 CLI 向您的 Azure 訂用帳戶進行驗證。 最基本的是以互動方式使用瀏覽器進行驗證。 若要以互動方式進行驗證，請開啟命令列或終端機，然後使用下列命令：

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示包含流覽[https://aka.ms/devicelogin](https://aka.ms/devicelogin)和輸入授權碼。

## <a name="install-the-machine-learning-extension"></a>安裝機器學習擴充功能

若要安裝機器學習擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

如果您收到訊息指出已安裝延伸模組，請使用下列命令來更新至最新版本：

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是 Azure 平臺上的基本資源容器。 使用 Azure Machine Learning 服務時，資源群組會包含您的 Azure Machine Learning 服務工作區。 它也會包含工作區所使用的其他 Azure 服務。 例如，如果您使用雲端式計算資源來定型您的模型，則會在資源群組中建立該資源。

若要__建立新的資源群組__，請使用下列命令。 取代`<resource-group-name>`為要用於此資源群組的名稱。 將`<location>`取代為要用於此資源群組的 Azure 區域：

> [!TIP]
> 您應該選取可使用 Azure Machine Learning 服務的區域。 如需相關資訊，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的回應類似下列 JSON：

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

如需使用資源群組的詳細資訊，請參閱[az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>建立工作區

若要建立新的工作區，請使用下列命令。 以您要用於此工作區的名稱取代 `<workspace-name>`。 以資源群組的名稱取代 `<resource-group-name>`：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

此命令的輸出類似下列 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>將本機專案連接到工作區

從終端機或命令提示字元中，使用下列命令將目錄變更為 `mlops` 目錄，然後連接到您的工作區：

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

此命令的輸出類似下列 JSON：

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

此命令會建立 `.azureml/config.json` 檔案，其中包含連接到您的工作區所需的資訊。 本教學課程中使用的其餘 `az ml` 命令將使用此檔案，因此您不需要將工作區和資源群組新增至所有命令。

## <a name="create-the-compute-target-for-training"></a>建立用於定型的計算目標

這個範例會使用 Azure Machine Learning 計算實例來定型模型。 若要建立新的計算實例，請使用下列命令：

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

此命令的輸出類似下列 JSON：

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

此命令會建立名為 `cpu` 的新計算目標，最多四個節點。 選取的 VM 大小會提供具有 GPU 資源的 VM。 如需 VM 大小的詳細資訊，請參閱 [VM 類型和大小]。

> [!IMPORTANT]
> 計算目標的名稱（在此案例中為 `cpu`）很重要;在下一節中使用的 `.azureml/sklearn.runconfig` 檔案會參考此檔案。

## <a name="submit-the-training-run"></a>提交定型回合

若要開始在 `cpu` 計算目標上執行訓練，請將目錄變更為 @no__t 1 目錄，然後使用下列命令：

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

此命令會指定實驗的名稱（`myexperiment`）。 實驗會將此執行的相關資訊儲存在工作區中。

@No__t-0 參數指定 @no__t 1 檔案。 如先前所述，此檔案包含用來設定定型執行所使用之環境的資訊。 如果您檢查此檔案，您會看到它參考您稍早建立的 @no__t 0 計算目標。 它也會列出定型（`"nodeCount": "4"`）時要使用的節點數目，並包含 @no__t 1 區段，其中列出執行訓練腳本所需的 Python 套件。

如需執行設定檔的詳細資訊，請參閱[設定及使用計算目標進行模型定型](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)。

@No__t-0 參數會將此執行的參考儲存在 JSON 檔案中，並將在後續步驟中用來註冊和下載模型。

在定型執行過程中，它會從遠端計算資源上的定型會話串流資訊。 部分資訊類似下列文字：

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

這段文字會從定型腳本（`train-sklearn.py`）記錄，並顯示此模型的兩個效能計量。 在此情況下，我們想要具有最高 Alpha 值的模型。 效能計量是您要定型的模型特有的。 其他模型會有不同的效能計量。

如果您檢查 `train-sklearn.py`，您會注意到它也會在將定型的模型儲存至檔案時，使用 Alpha 值。 在此情況下，它會訓練數個模型。 具有最高 Alpha 的人應該是最棒的一個。 查看上述輸出和程式碼，Alpha 為0.95 的模型會儲存為 `./outputs/ridge_0.95.pkl`

模型已儲存至已定型之計算目標上的 `./outputs` 目錄。 在此情況下，會在 Azure 雲端中 Azure Machine Learning 計算實例。 定型程式會自動將 `./outputs` 目錄的內容，從進行定型的計算目標上傳到您的 Azure Machine Learning 工作區。 它會儲存為實驗的一部分（在此範例中為 `myexperiment`）。

## <a name="register-the-model"></a>註冊模型

若要直接從您實驗中的預存版本註冊模型，請使用下列命令：

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

此命令會將定型執行所建立的 @no__t 0 檔案，註冊為名為 `mymodel` 的新模型註冊。 @No__t-0 會參考實驗中的路徑。 在此情況下，會從定型命令所建立的 @no__t 0 檔案載入實驗和執行資訊。 @No__t-0 會建立 JSON 檔案，此檔案會參考此命令所建立的新註冊模型，並供其他使用已註冊模型的 CLI 命令使用。

此命令的輸出類似下列 JSON：

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>模型版本設定

請記下針對模型傳回的版本號碼。 每次您使用此名稱來註冊新模型時，版本都會遞增。 例如，您可以使用下列命令，從本機檔案下載並註冊模型：

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

第一個命令會將已註冊的模型下載到目前的目錄。 檔案名為 `ridge_0.95.pkl`，這是您註冊模型時所參考的檔案。 第二個命令會使用與先前註冊相同的名稱（`mymodel`）來註冊本機模型（`-p "ridge_0.95.pkl"`）。 這次，傳回的 JSON 資料會將版本列為2。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，請將目錄變更為 `model-deployment` 目錄，然後使用下列命令：

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

您可能會收到「無法建立 Docker 用戶端」的訊息。 您可以忽略此訊息。 CLI 可以將 web 服務部署到本機 Docker 容器，並檢查 Docker。 在此情況下，我們不會使用本機部署。

此命令會使用您先前註冊的模型第1版，部署名為 `myservice` 的新服務。

@No__t 0 檔案提供如何執行推斷的相關資訊，例如輸入腳本（`score.py`）和軟體相依性。 如需此檔案結構的詳細資訊，請參閱[推斷設定架構](reference-azure-machine-learning-cli.md#inference-configuration-schema)。 如需有關輸入腳本的詳細資訊，請參閱[使用 Azure Machine Learning 服務來部署模型](how-to-deploy-and-where.md#prepare-to-deploy)。

@No__t-0 描述用來裝載服務的部署環境。 部署設定適用于用於部署的計算類型。 在此情況下，會使用 Azure 容器實例。 如需詳細資訊，請參閱[部署設定架構](reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

部署程式完成前需要幾分鐘的時間。

> [!TIP]
> 在此範例中，會使用 Azure 容器實例。 ACI 的部署會自動建立所需的 ACI 資源。 如果您改為部署到 Azure Kubernetes Service，您必須預先建立 AKS 叢集，並將它指定為 `az ml model deploy` 命令的一部分。 如需部署至 AKS 的範例，請參閱將[模型部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)叢集。

幾分鐘後，會傳回與下列 JSON 類似的資訊：

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>評分 URI

從部署傳回的 `scoringUri` 是部署為 web 服務之模型的 REST 端點。 您也可以使用下列命令來取得此 URI：

```azurecli-interactive
az ml service show -n myservice
```

此命令會傳回相同的 JSON 檔，包括 `scoringUri`。

REST 端點可以用來將資料傳送至服務。 如需建立將資料傳送至服務之用戶端應用程式的詳細資訊，請參閱[使用部署為 web 服務的 Azure Machine Learning 模型](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>將資料傳送至服務

雖然您可以建立用戶端應用程式來呼叫端點，但 machine learning CLI 會提供可做為測試用戶端的公用程式。 使用下列命令將測試資料傳送至服務：

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

命令的回應類似于 `[4684.920839774082]`。

## <a name="clean-up-resources"></a>清除資源

> [!IMPORTANT]
> 您所建立的資源可用來作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

### <a name="delete-deployed-service"></a>刪除已部署的服務

如果您打算繼續使用 Azure Machine Learning 工作區，但想要清除已部署的服務以降低成本，請使用下列命令：

```azurecli-interactive
az ml service delete -n myservice
```

此命令會傳回 JSON 檔，其中包含已刪除之服務的名稱。 刪除服務可能需要幾分鐘的時間。

### <a name="delete-the-training-compute"></a>刪除訓練計算

如果您打算繼續使用 Azure Machine Learning 工作區，但想要清除為定型建立的 @no__t 0 計算目標，請使用下列命令：

```azurecli-interactive
az ml computetarget delete -n cpu
```

此命令會傳回 JSON 檔，其中包含已刪除之計算目標的識別碼。 刪除計算目標可能需要幾分鐘的時間。

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用您所建立的資源，請刪除它們，以免產生額外費用。

若要刪除資源群組，以及在本檔中建立的所有 Azure 資源，請使用下列命令。 以您稍早建立的資源組名取代 `<resource-group-name>`：

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>後續步驟

在本 Azure Machine Learning 教學課程中，您已使用 Machine Learning CLI 來執行下列工作：

> [!div class="checklist"]
> * 安裝機器學習擴充功能
> * 建立 Azure Machine Learning 工作區
> * 建立用來定型模型的計算資源
> * 開始執行訓練
> * 註冊並下載模型
> * 將模型部署為 Web 服務
> * 使用 web 服務評分資料

如需使用 CLI 的詳細資訊，請參閱[使用適用于 Azure Machine Learning 服務的 CLI 擴充](reference-azure-machine-learning-cli.md)功能。