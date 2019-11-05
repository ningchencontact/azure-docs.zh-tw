---
title: 使用應用程式閘道透過 HTTP 或 HTTPS 公開 AKS 服務
description: 本文提供如何使用應用程式閘道透過 HTTP 或 HTTPS 公開 AKS 服務的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/22/2019
ms.author: caya
ms.openlocfilehash: b04c381cbe5ad9199f182f39e7311ae01fe2b8ba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513610"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>使用應用程式閘道透過 HTTP 或 HTTPS 公開 AKS 服務 

這些教學課程可協助說明如何使用[Kubernetes 輸入資源](https://kubernetes.io/docs/concepts/services-networking/ingress/)，透過 HTTP 或 HTTPS 透過[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/)公開範例 Kubernetes 服務。

## <a name="prerequisites"></a>必要條件

- 已安裝 `ingress-azure` helm 圖表。
  - [**Greenfield 部署**](ingress-controller-install-new.md)：如果您從頭開始，請參閱這些安裝指示，其中概述部署具有應用程式閘道之 AKS 叢集的步驟，並在 AKS 叢集上安裝應用程式閘道輸入控制器。
  - [**Brownfield 部署**](ingress-controller-install-existing.md)：如果您有現有的 AKS 叢集並應用程式閘道，請參閱這些指示，在 AKS 叢集上安裝應用程式閘道輸入控制器。
- 如果您想要在此應用程式上使用 HTTPS，您將需要 x509 憑證和其私密金鑰。

## <a name="deploy-guestbook-application"></a>部署 `guestbook` 應用程式

留言簿應用程式是標準的 Kubernetes 應用程式，可組成 Web UI 前端、後端和 Redis 資料庫。 根據預設，`guestbook` 會透過埠 `80`上名為 `frontend` 的服務來公開其應用程式。 如果沒有 Kubernetes 輸入資源，服務就無法從 AKS 叢集外部存取。 我們將使用應用程式並設定輸入資源，以透過 HTTP 和 HTTPS 存取應用程式。

請依照下列指示來部署留言簿應用程式。

1. 從[這裡](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)下載 `guestbook-all-in-one.yaml`
1. 執行，將 `guestbook-all-in-one.yaml` 部署到您的 AKS 叢集中

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

現在，已部署 `guestbook` 應用程式。

## <a name="expose-services-over-http"></a>透過 HTTP 公開服務

為了公開留言簿應用程式，我們將使用下列輸入資源：

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

這項輸入將會公開 `guestbook-all-in-one` 部署的 `frontend` 服務，做為應用程式閘道的預設後端。

將上述輸入資源儲存為 `ing-guestbook.yaml`。

1. 執行下列程式來部署 `ing-guestbook.yaml`：

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 檢查輸入控制器的記錄檔中是否有部署狀態。

現在應該可以使用 `guestbook` 應用程式。 您可以造訪應用程式閘道的公用位址來進行檢查。

## <a name="expose-services-over-https"></a>透過 HTTPS 公開服務

### <a name="without-specified-hostname"></a>沒有指定的主機名稱

若未指定主機名稱，則會在所有指向應用程式閘道的主機名稱上使用「留言簿」服務。

1. 在部署輸入之前，您必須建立 kubernetes 秘密來裝載憑證和私密金鑰。 您可以藉由執行來建立 kubernetes 秘密

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 定義下列輸入。 在 [輸入] 中，指定 [`secretName`] 區段中的密碼名稱。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > 將上述輸入資源中的 `<guestbook-secret-name>` 取代為您的密碼名稱。 將上述輸入資源儲存 `ing-guestbook-tls.yaml`的檔案名。

1. 藉由執行來部署 yaml 的

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 檢查輸入控制器的記錄檔中是否有部署狀態。

現在，`guestbook` 的應用程式都可以在 HTTP 和 HTTPS 上使用。

### <a name="with-specified-hostname"></a>具有指定的主機名稱

您也可以在輸入上指定主機名稱，以便多工 TLS 設定和服務。
藉由指定主機名稱，將只能在指定的主機上使用「留言簿」服務。

1. 定義下列輸入。
    在 [輸入] 中，于 [`secretName`] 區段中指定密碼的名稱，並據以取代 [`hosts`] 區段中的 [主機名稱]。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. 藉由執行來部署 `ing-guestbook-tls-sni.yaml`

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 檢查輸入控制器的記錄檔中是否有部署狀態。

現在，`guestbook` 應用程式只會在指定的主機（在此範例中為`<guestbook.contoso.com>`）上，于 HTTP 和 HTTPS 上提供使用。

## <a name="integrate-with-other-services"></a>與其他服務整合

下列輸入可讓您將其他路徑新增至此輸入，並將這些路徑重新導向至其他服務：

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
