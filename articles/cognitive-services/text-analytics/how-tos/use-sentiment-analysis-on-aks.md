---
title: 執行 Azure Kubernetes 服務
titleSuffix: Text Analytics - Azure Cognitive Services
description: 文字分析將容器部署與情感分析映像到 Azure Kubernetes 服務，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a419ed3b9c0d2c4db9c552642dc5c662786f6730
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561242"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>將情感分析容器部署至 Azure Kubernetes Service (AKS)

了解如何部署認知服務[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)使用情感分析映像到 Azure Kubernetes Service (AKS) 的容器。 建立文字分析的資源，建立相關聯的情感分析映像，以及能夠練習從瀏覽器兩個此協調流程，舉例說明此程序。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

## <a name="prerequisites"></a>必要條件

此程序需要必須安裝並在本機執行的多個工具。 請勿使用 Azure Cloud Shell。

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* 文字編輯器，例如：[Visual Studio Code](https://code.visualstudio.com/download)。
* 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 安裝[Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
    * 只有 F0 或標準定價層的**文字分析**資源。
    * 有 S0 定價層的**認知服務**資源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>將文字 Analytics 容器部署至 AKS 叢集

1. 開啟 Azure CLI 並登入至 Azure

    ```azurecli
    az login
    ```

1. 登入 AKS 叢集 (取代`your-cluster-name`和`your-resource-group`以適當的值)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    此命令執行之後，它會報告類似以下的訊息：

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果您有多個訂用帳戶可供您在您的 Azure 帳戶和`az aks get-credentials`命令傳回錯誤，常見的問題是，您使用錯誤的訂用帳戶。 只要設定您的 Azure CLI 工作階段，使用您所建立的資源使用的相同訂用帳戶的內容，並再試一次。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 開啟所選的文字編輯器 (此範例會使用__Visual Studio Code__):

    ```azurecli
    code .
    ```

1. 在文字編輯器中，建立新的檔案，名為_sentiment.yaml_然後貼上下列 YAML。 請務必取代`billing/value`和`apikey/value`以您自己。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. 儲存檔案並關閉文字編輯器。
1. 執行的 Kubernetes`apply`命令搭配_sentiment.yaml_當做其目標：

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    之後的命令已成功地會套用部署組態，類似下列輸出的訊息：

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 請確認已部署的 POD:

    ```console
    kubectl get pods
    ```

    這會輸出 POD 的執行狀態：

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 請確認服務可供使用，並取得 IP 位址：

    ```console
    kubectl get services
    ```

    這會輸出的執行狀態_情感_服務中的 POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>後續步驟

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)
* 使用[文字分析已連線服務](../vs-text-connected-service.md)
