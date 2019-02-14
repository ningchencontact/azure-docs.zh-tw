---
title: 機器學習 CLI 擴充功能
titleSuffix: Azure Machine Learning service
description: 了解 Azure CLI 的 Azure Machine Learning CLI 擴充功能。 Azure CLI 是一個跨平台命令列公用工具，可讓您使用 Azure 雲端的資源。 Machine Learning 擴充功能可讓您使用 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 65936348dcb40c6ceb71ebf735da8bb2120af654
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694511"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務的 CLI 擴充功能

Azure Machine Learning CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此擴充功能會提供一些命令，以便從命令列使用 Azure Machine Learning 服務。 它可讓您建立一些指令碼，讓您的機器學習工作流程自動化。 例如，您可以建立可執行下列動作的指令碼︰

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 它是已經過最佳化處理的補充工具，可處理高度參數化的工作，例如：

* 建立計算資源

* 參數化實驗提交

* 註冊模型

* 映像建立

* 服務部署

## <a name="prerequisites"></a>必要條件


* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](http://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安裝擴充功能

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

出現提示時，請選取 `y` 來安裝擴充功能。

若要確認已安裝擴充功能，請使用下列命令來顯示 ML 特有的子命令清單：

```azurecli-interactive
az ml -h
```

> [!TIP]
> 若要更新擴充功能，您必須將它__移除__，然後加以__安裝__。 這會安裝最新版本。

## <a name="remove-the-extension"></a>移除擴充功能

若要移除 CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>資源管理

下列命令示範如何使用 CLI 來管理 Azure Machine Learning 所使用的資源。


+ 建立 Azure Machine Learning 服務工作區：

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ 設定預設工作區：

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
* 連結 AKS 叢集

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>實驗

下列命令示範如何使用 CLI 來使用實驗：

* 在提交實驗前附加專案 (回合組態)：

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* 開始您的實驗回合。 使用此命令時，請指定包含回合組態的 `.runconfig` 檔案名稱。 計算目標會使用回合組態來建立模型的訓練環境。 在此範例中，回合組態會從 `./aml_config/myrunconfig.runconfig` 檔案載入。

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    當您使用 `az ml project attach` 命令來附加專案時，系統就會建立名為 `docker.runconfig` 和 `local.runconfig` 的預設 `.runconfig`。 這些檔案可能需要先修改，才能用來順練模型。 

    您也可以使用 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 類別，以程式設計方式建立回合組態。 一旦建立後，您就可以使用 `save()` 方法來建立 `.runconfig` 檔案。

* 檢視已提交的實驗清單：

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>模型註冊、映像建立和部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 建立映像，其中包含您的機器學習模型和相依性： 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ 將映像部署到計算目標：

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
