---
title: 如何使用 Azure Machine Learning CLI 擴充功能
description: 了解 Azure CLI 的 Azure Machine Learning CLI 擴充功能。 Azure CLI 是一個跨平台命令列公用工具，可讓您使用 Azure 雲端的資源。 Machine Learning 擴充功能可讓您使用 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 13d09471191deed670db97a9f18e15bc9577dd1a
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713413"
---
# <a name="use-the-azure-machine-learning-cli-extension"></a>使用 Azure Machine Learning CLI 擴充功能

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

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

> [!NOTE]
> 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLfree) 。

## <a name="install-the-extension"></a>安裝擴充功能

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
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

+ 建立 DSVM (資料科學 VM)。 您也可以建立 BatchAI 叢集來用於分散式訓練，或建立 AKS 叢集來用於部署。


  ```azurecli-interactive
  az ml computetarget setup dsvm -n mydsvm
  ```

## <a name="experiments"></a>實驗

下列命令示範如何使用 CLI 來使用實驗：

* 在提交實驗前附加專案 (回合組態)：

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* 開始您的實驗回合。 使用此命令時，指定計算目標。 在此範例中，`local` 會使用本機電腦，利用 `train.py` 指令碼來定型模型：

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

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
