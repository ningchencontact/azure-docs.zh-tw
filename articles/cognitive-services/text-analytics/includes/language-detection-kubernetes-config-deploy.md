---
title: 語言偵測 Kubernetes config 和 deploy 步驟
titleSuffix: Azure Cognitive Services
description: 語言偵測 Kubernetes config 和 deploy 步驟
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 2593f07ac30df77936c56785956b9e906ef683be
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052122"
---
## <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>將語言偵測容器部署至 AKS 叢集

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

1. 在文字編輯器中, 建立名為*language. yaml*的新檔案, 並將下列 yaml 貼入其中。 請務必將和`billing/value` `apikey/value`取代為您自己的資訊。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. 儲存檔案, 然後關閉文字編輯器。
1. 以 yaml 檔案`apply`作為目標來執行 Kubernetes 命令:

    ```console
    kuberctl apply -f language.yaml
    ```

    命令成功套用部署設定後, 會出現類似下列輸出的訊息:

    ```console
    deployment.apps "language" created
    service "language" created
    ```
1. 確認已部署 pod:

    ```console
    kubectl get pods
    ```

    Pod 執行狀態的輸出:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 確認服務可供使用, 並取得 IP 位址。

    ```console
    kubectl get services
    ```

    Pod 中*語言*服務執行狀態的輸出:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
