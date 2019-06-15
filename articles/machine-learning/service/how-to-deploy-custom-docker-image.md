---
title: 如何部署模型使用自訂的 Docker 映像
titleSuffix: Azure Machine Learning service
description: 了解如何部署 Azure 機器學習服務模型時，使用自訂的 Docker 映像。 當部署定型的模型，Docker 映像會建立主應用程式的映像、 web 伺服器和執行服務所需的其他元件。 雖然 Azure Machine Learning 服務為您提供的預設映像，您也可以使用自己的映像。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 02bc8835ddb163d81f389e13b21b88adca55cb2f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082621"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>部署模型使用自訂的 Docker 映像

了解如何部署與 Azure Machine Learning 服務的定型的模型時，使用自訂的 Docker 映像。

當您將定型的模型部署至 web 服務或 IoT Edge 裝置時，會建立 Docker 映像。 此映像包含模型、 conda 環境和使用模型所需的資產。 它也包含網頁伺服器來處理部署為 web 服務，以及使用 Azure IoT 中樞所需的元件時的連入要求。

Azure Machine Learning 服務會提供預設的 Docker 映像，讓您不必擔心建立一個。 您也可以使用您建立自訂映像_基底映像_。 映像建立部署時，基底映像可做為起點。 它提供的基礎作業系統和元件。 然後在部署程序會將額外的元件，例如您的模型、 conda 環境和其他資產，將再將它部署的映像中。

一般而言，您建立自訂映像，當您想要控制元件版本，或儲存在部署期間的時間。 比方說，您可能要在特定版本的 Python、 Conda、 或其他元件上標準化。 您也可以安裝您的模型，其中的安裝程序會很長的時間所需的軟體。 建立基底映像時，安裝此軟體表示您不必安裝每個部署。

> [!IMPORTANT]
> 在部署模型時，您無法覆寫核心元件，例如網頁伺服器或 IoT Edge 的元件。 這些元件提供已知的工作環境是測試與 Microsoft 支援。

> [!WARNING]
> Microsoft 可能無法協助疑難排解問題的自訂映像所造成。 如果您遇到問題，您可能會要求使用的預設映像或其中一個 Microsoft 提供給您的映像的特定問題時，請參閱映像。

這份文件會分成兩個區段：

* 建立自訂映像：提供建立自訂映像和設定 Azure Container Registry 使用 Azure CLI 和 Machine Learning CLI 驗證的相關資訊給管理員和 DevOps。
* 使用自訂映像：使用自訂映像，部署定型的模型，從 Python SDK 或 ML CLI 時資料科學家和 DevOps/MLOps 提供相關資訊。

## <a name="prerequisites"></a>必要條件

* Azure 機器學習服務工作群組中。 如需詳細資訊，請參閱 <<c0> [ 建立工作區](setup-create-workspace.md)文章。
* Azure Machine Learning SDK。 如需詳細資訊，請參閱 > 一節 Python SDK[建立工作區](setup-create-workspace.md#sdk)文章。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure Container Registry](/azure/container-registry)或可在網際網路存取其他 Docker 登錄。
* 這份文件中的步驟假設您熟悉建立和使用__推斷組態__物件做為模型部署的一部分。 如需詳細資訊，請參閱 「 準備部署 」 一節[部署位置和方式](how-to-deploy-and-where.md#prepare-to-deploy)。

## <a name="create-a-custom-image"></a>建立自訂映像

在本節中的資訊假設您使用 Azure Container Registry 來儲存 Docker 映像。 使用下列檢查清單規劃時，建立 Azure Machine Learning 服務的自訂映像：

* 您將使用 Azure 機器學習服務工作區中或獨立的 Azure Container Registry 建立 Azure Container Registry？

    當使用映像儲存在__工作區的容器登錄庫__，您不需要向登錄進行驗證。 驗證工作區所處理。

    > [!TIP]
    > 第一次訓練，或部署模型使用的工作區時，會建立容器登錄中，供您的工作區。 如果您已建立新的工作區中，但不是受過訓練或建立模型，任何的 Azure Container Registry 會不存在的工作區。

    如需有關擷取您的工作區的 Azure Container Registry 的名稱，請參閱[取得容器登錄名稱](#getname)一節。

    當使用映像儲存在__獨立容器登錄庫__，您必須設定至少具有讀取權限的服務主體。 您接著會提供的服務主體識別碼 （使用者名稱） 和密碼使用映像從登錄的任何人。 例外情況是如果您將容器登錄可公開存取。

    如需建立私人 Azure 容器登錄的詳細資訊，請參閱[建立私人容器登錄庫](/azure/container-registry/container-registery-get-started-azure-cli)。

    如需使用 Azure Container Registry 中的服務主體的資訊，請參閱[使用服務主體進行 Azure Container Registry 驗證](/azure/container-registry/container-registry-auth-service-principal)。

* Azure Container Registry 和映像的資訊：需要使用它的任何人提供的映像名稱。 例如，名為映像`myimage`、 在登錄中名為預存`myregistry`，參考為`myregistry.azurecr.io/myimage`時使用的模型部署的映像

* 映像需求：Azure Machine Learning 服務僅支援 Docker 映像，提供下列軟體：

    * Ubuntu 16.04 或更新版本。
    * Conda 4.5。 # 或更新版本。
    * Python 3.5。 # 或 3.6。 #。

<a id="getname"></a>

### <a name="get-container-registry-information"></a>取得容器登錄資訊

在本節中，了解如何取得您的 Azure 機器學習服務工作區中的 Azure Container Registry 的名稱。

> [!TIP]
> 第一次訓練，或部署模型使用的工作區時，會建立容器登錄中，供您的工作區。 如果您已建立新的工作區中，但不是受過訓練或建立模型，任何的 Azure Container Registry 會不存在的工作區。

如果您已經已受過訓練，或部署使用 Azure 機器學習服務模型，為您工作區建立容器登錄庫。 若要尋找此容器登錄的名稱，請使用下列步驟：

1. 開啟新的殼層或命令提示字元，並使用下列命令來驗證您的 Azure 訂用帳戶：

    ```azurecli-interactive
    az login
    ```

    遵循提示來驗證訂用帳戶。

2. 您可以使用下列命令，列出工作區的容器登錄。 取代`<myworkspace>`以您的 Azure 機器學習服務工作區名稱。 取代`<resourcegroup>`與 Azure 資源群組，其中包含您的工作區：

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    所傳回的資訊類似下列文字︰

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`值是您的工作區的 Azure Container Registry 的名稱。

### <a name="build-a-custom-image"></a>建置自訂映像

此節逐步解說中您的 Azure Container Registry 中建立自訂的 Docker 映像中的步驟。

1. 建立新的文字檔案，名為`Dockerfile`，並使用下列文字做為內容：

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 在殼層或命令提示字元中，使用下列來向 Azure Container Registry 進行驗證。 取代`<registry_name>`具有您想要儲存在映像的容器登錄的名稱：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 若要上傳 Dockerfile 中，並建置該檔案，使用下列命令。 取代`<registry_name>`具有您想要儲存在映像的容器登錄的名稱：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    在建置過程中，會將資訊串流至回到命令列。 如果建置成功，您會收到類似下列文字的訊息：

    ```text
    Run ID: cda was successful after 2m56s
    ```

如需有關如何建置使用 Azure Container Registry 的映像的詳細資訊，請參閱[建置並執行使用 Azure 容器登錄工作的容器映像](/docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli.md)

如需有關如何將現有的映像上傳至 Azure Container Registry 的詳細資訊，請參閱 <<c0> [ 您的第一個映像推送至私人 Docker 容器登錄](/azure/container-registry/container-registry-get-started-docker-cli.md)。

## <a name="use-a-custom-image"></a>使用自訂映像

若要使用自訂映像，您需要下列資訊：

* __映像名稱__。 比方說，`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`是基本的 Docker 映像，Microsoft 所提供的路徑。
* 如果映像位於__私人存放庫__，您需要下列資訊：

    * 登錄__地址__。 例如： `myregistry.azureecr.io`。
    * 服務主體__使用者名稱__並__密碼__登錄具有讀取權限。

    如果您沒有此資訊，請向系統管理員，Azure Container registry，其中包含您的映像。

### <a name="publicly-available-images"></a>公開可用映像

Microsoft 提供數個 docker 映像可公開存取的存放庫，可用來進行這一節中的步驟：

| Image | 描述 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning 服務的基本映像 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | 包含 ONNX 執行階段。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | 包含 ONNX 執行階段和 CUDA 元件。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | 包含 ONNX 執行階段和 TensorRT。 |

> [!TIP]
> 這些映像都可以公開使用，因為您不需要使用它們時提供的地址、 使用者名稱或密碼。

> [!IMPORTANT]
> 使用 CUDA 或 TensorRT 的 Microsoft 映像必須只使用 Microsoft Azure 服務上。

> [!TIP]
>__如果您的模型訓練 Azure Machine Learning 計算__，並使用__1.0.22 版或更新版本__Azure 機器學習服務 sdk，在定型期間建立的映像。 若要找到此映像的名稱，請使用`run.properties["AzureML.DerivedImageName"]`。 下列範例示範如何使用此映像：
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>使用 Azure 機器學習服務 SDK 中的映像

若要使用自訂映像，將`base_image`的屬性[推斷組態物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)位址的映像：

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

此格式適用於這兩個公開存取您的工作區和容器登錄的 Azure Container Registry 中儲存的映像。 例如，下列程式碼會使用 Microsoft 所提供的預設映像：

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

若要使用的映像__私人容器登錄庫__不在您的工作區中，您必須指定存放庫的使用者名稱和密碼的位址：

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>使用 Machine Learning CLI 中的映像

> [!IMPORTANT]
> 目前的 Machine Learning CLI 可以使用映像從 Azure Container Registry 為您的工作區或可公開存取的存放庫。 它不能從獨立的私人登錄使用映像。

在部署模型，使用 Machine Learning CLI，您會提供推斷組態檔參照自訂映像。 下列 JSON 文件會示範如何參考中的公用容器登錄的映像：

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

此檔案會搭配`az ml model deploy`命令。 `--ic`參數用來指定推斷組態檔。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

如需有關使用 ML CLI 部署模型的詳細資訊，請參閱 「 模型註冊、 程式碼剖析和部署 > 一節[Azure Machine Learning 服務的 CLI 擴充功能](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)文章。

## <a name="next-steps"></a>後續步驟

* 深入了解[部署位置和方式](how-to-deploy-and-where.md)。
* 了解如何[定型和部署機器學習服務模型，使用 Azure 管線](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)。