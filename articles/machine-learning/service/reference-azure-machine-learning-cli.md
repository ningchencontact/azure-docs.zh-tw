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
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 2992ec9f43aac9e0d80c5e42873d26ac3a9c3fd1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916983"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務的 CLI 擴充功能

Azure Machine Learning CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此擴充功能會提供一些命令，以便從命令列使用 Azure Machine Learning 服務。 它可讓您自動化您的機器學習工作流程。 例如，您可以執行下列動作：

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 這是已最佳化，可處理高參數化的工作自動化以符合本身的互補工具。

## <a name="prerequisites"></a>必要條件

* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安裝擴充功能

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 可以找到範例檔案，您可以使用下列命令搭配[此處](http://aka.ms/azml-deploy-cloud)。

出現提示時，請選取 `y` 來安裝擴充功能。

若要確認已安裝擴充功能，請使用下列命令來顯示 ML 特有的子命令清單：

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>移除擴充功能

若要移除 CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>資源管理

下列命令示範如何使用 CLI 來管理 Azure Machine Learning 所使用的資源。

+ 如果您還沒有一個建立的資源群組：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 建立 Azure Machine Learning 服務工作區：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ 附加到資料夾，以啟用 CLI 內容感知的工作區設定。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ 將 Azure blob 容器中附加的資料存放區。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

+ 附加的 AKS 叢集作為計算目標。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

+ 建立新的 AMLcompute 目標

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```
    
## <a id="experiments"></a>執行實驗

+ 附加到資料夾，以啟用 CLI 內容感知的工作區設定。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

* 開始您的實驗回合。 使用此命令時，指定的 runconfig 檔案名稱 (在之前的文字\*.runconfig，如果您正在查看您的檔案系統) 對-c 參數。

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* 檢視實驗的清單：

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling-deployment"></a>模型註冊、 程式碼剖析，部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ 將模型部署到 AKS

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
