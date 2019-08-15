---
title: 使用自訂 traefik 輸入控制器並設定 HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: 瞭解如何設定 Azure Dev Spaces 以使用自訂 traefik 輸入控制器, 並使用該輸入控制器來設定 HTTPS
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: 50908bde65b69cb475391cd30bca758dd571f114
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036939"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>使用自訂 traefik 輸入控制器並設定 HTTPS

本文說明如何將 Azure Dev Spaces 設定為使用自訂 traefik 輸入控制器。 本文也會說明如何將該自訂輸入控制器設定為使用 HTTPS。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有帳戶，您可以建立[免費帳戶][azure-account-create]。
* [已安裝 Azure CLI][az-cli]。
* [已啟用 Azure Dev Spaces 的 Azure Kubernetes Service (AKS)][qs-cli]叢集。
* 已安裝[kubectl][kubectl] 。
* [已安裝 Helm 2.13 或更新版本][helm-installed] \(英文\)。
* 在與您的 AKS 叢集相同的資源群組中, 具有[DNS 區域][dns-zone]的[自訂網域][custom-domain]。

## <a name="configure-a-custom-traefik-ingress-controller"></a>設定自訂 traefik 輸入控制器

使用[kubectl][kubectl](Kubernetes 命令列用戶端) 連接到您的叢集。 若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

若要驗證叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

為 traefik 輸入控制器建立 Kubernetes 命名空間, 並使用`helm`安裝它。

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

使用[kubectl get][kubectl-get]取得 traefik 輸入控制器服務的 IP 位址。

```console
kubectl get svc -n traefik --watch
```

範例輸出會顯示*traefik*命名空間中所有服務的 IP 位址。

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

使用 az network DNS record, 將*a*記錄新增至您的 DNS 區域, 並在其中加入 traefik 服務的外部 IP 位址[-設定新增記錄][az-network-dns-record-set-a-add-record]。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

上述範例會將*A*記錄新增至*MY_CUSTOM_DOMAIN* DNS 區域。

在本文中，您會使用 [Azure Dev Spaces 單車共享範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) \(英文\) 來示範如何使用 Azure Dev Spaces。 從 GitHub 複製應用程式，並瀏覽至其目錄：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

開啟 [[值]。 yaml][values-yaml] , 並將 *< REPLACE_ME_WITH_HOST_SUFFIX >* 的所有實例取代為*traefik。* 使用您的網域進行*MY_CUSTOM_DOMAIN*的 MY_CUSTOM_DOMAIN。 此外, 使用*kubernetes.io/ingress.class: traefik # 自訂*輸入來取代*kubernetes.io/ingress.class: traefik-Azds # Dev Spaces 特有*。 以下是已更新`values.yaml`檔案的範例:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

儲存變更並關閉該檔案。

使用`helm install`部署範例應用程式。

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

上述範例會將範例應用程式部署至*dev*命名空間。

使用`azds space select`您的範例應用程式選取*開發人員*空間, 並顯示使用`azds list-uris`存取範例應用程式的 url。

```console
azds space select -n dev
azds list-uris
```

下列輸出顯示來自`azds list-uris`的範例 url。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

開啟來自 `azds list-uris` 命令的公用 URL，來瀏覽至 *bikesharingweb* 服務。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`。

使用命令在 dev 底下建立子空間, 並列出 url 以存取子開發人員空間。 `azds space select`

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

下列輸出顯示來自`azds list-uris`的範例 url, 以存取*azureuser1*子開發人員空間中的範例應用程式。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

從`azds list-uris`命令開啟公用 URL, 以流覽至*azureuser1*子開發人員空間中的*bikesharingweb*服務。 在上述範例中, *azureuser1*子開發人員空間中`http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` *bikesharingweb*服務的公用 URL 是。

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>將 traefik 輸入控制器設定為使用 HTTPS

`dev-spaces/samples/BikeSharingApp/traefik-values.yaml`建立類似下列範例的檔案。 使用您自己的電子郵件更新*電子郵件*值, 這是用來產生具有 Let 的加密的憑證。

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

使用`helm repo update`更新您的*traefik*服務, 並包含您所建立的*traefik-yaml*檔案。

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

上述命令會使用*traefik-values. yaml*中的值來執行 traefik 服務的新版本, 並移除先前的服務。 Traefik 服務也會使用 Let's Encrypt 來建立 TLS 憑證, 並開始將 web 流量重新導向至使用 HTTPS。

> [!NOTE]
> 可能需要幾分鐘的時間, 新的 traefik 服務版本才會啟動。 您可以使用`kubectl get pods --namespace traefik --watch`來檢查進度。

流覽至*dev/azureuser1*子空間中的範例應用程式, 並注意您會重新導向至使用 HTTPS。 另請注意, 頁面會載入, 但瀏覽器會顯示一些錯誤。 開啟瀏覽器主控台會顯示與嘗試載入 HTTP 資源的 HTTPS 頁面相關的錯誤。 例如:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

若要修正此錯誤, 請將[BikeSharingWeb/azds][azds-yaml]更新為使用*kubernetes.io/ingress.class*的*traefik* , 並將您的自訂網域用於 *$ (hostSuffix)* 。 例如:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

以*url*套件的相依性更新[BikeSharingWeb/package. json][package-json] 。

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

更新[BikeSharingWeb/pages/helper .js][helpers-js]中的*getApiHostAsync*方法, 以使用 HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

流覽至`BikeSharingWeb`目錄, 並使用`azds up`來執行更新的 BikeSharingWeb 服務。

```console
cd BikeSharingWeb/
azds up
```

流覽至*dev/azureuser1*子空間中的範例應用程式, 並注意您會被重新導向至使用 HTTPS, 而不會發生任何錯誤。

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml