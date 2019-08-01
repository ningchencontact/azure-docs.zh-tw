---
title: 執行 Azure Kubernetes Service-文字分析
titleSuffix: Azure Cognitive Services
description: 部署具有情感分析影像的文字分析容器以 Azure Kubernetes Service, 並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552267"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>將情感分析容器部署到 Azure Kubernetes Service

瞭解如何將具有情感分析影像的 Azure 認知服務[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器部署至 AZURE KUBERNETES SERVICE (AKS)。 此程式會示範如何建立文字分析資源、如何建立相關聯的情感分析影像, 以及如何從瀏覽器執行這兩個協調流程。 使用容器可以將您的注意力轉移到管理基礎結構之外, 改為專注于應用程式開發。

## <a name="prerequisites"></a>先決條件

此程序需要必須安裝並在本機執行的多個工具。 請勿使用 Azure Cloud Shell。 您需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* 文字編輯器, 例如[Visual Studio Code](https://code.visualstudio.com/download)。
* 已安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 。
* 已安裝[KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
    * 只有 F0 或標準定價層的**Azure 文字分析**資源。
    * 具有 S0 定價層的**Azure 認知服務**資源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>將文字分析容器部署至 AKS 叢集

1. 開啟 Azure CLI, 然後登入 Azure。

    ```azurecli
    az login
    ```

1. 登入 AKS 叢集。 以`your-cluster-name`適當`your-resource-group`的值取代和。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    執行此命令之後, 它會報告類似下列的訊息:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果您的 Azure 帳戶有多個可用的訂用帳戶, `az aks get-credentials`且該命令傳回錯誤, 則常見的問題是您使用了錯誤的訂閱。 將 Azure CLI 會話的內容設定為使用您用來建立資源的相同訂用帳戶, 然後再試一次。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 開啟選擇的文字編輯器。 這個範例會使用 Visual Studio Code。

    ```azurecli
    code .
    ```

1. 在文字編輯器中, 建立名為_情感_的新檔案, 並在其中貼上下列 yaml。 請務必將和`billing/value` `apikey/value`取代為您自己的資訊。

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

1. 儲存檔案, 然後關閉文字編輯器。
1. 以`apply` _情感. yaml_作為目標來執行 Kubernetes 命令:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    命令成功套用部署設定後, 會出現類似下列輸出的訊息:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 確認已部署 pod:

    ```console
    kubectl get pods
    ```

    Pod 執行狀態的輸出:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 確認服務可供使用, 並取得 IP 位址。

    ```console
    kubectl get services
    ```

    Pod 中_情感_服務執行狀態的輸出:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>後續步驟

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)
* 使用[文字分析聯機服務](../vs-text-connected-service.md)
