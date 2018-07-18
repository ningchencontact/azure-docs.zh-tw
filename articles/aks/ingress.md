---
title: 為 Azure Kubernetes Service (AKS) 叢集設定輸入
description: 在 Azure Kubernetes Service (AKS) 叢集中安裝及設定 NGINX 輸入控制器。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bee80ebbaf0568706428d673d584819b1daa143
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096983"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 上的 HTTPS 輸入

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您將可使用單一外部位址將流量路由至 Kubernetes 叢集中的多個服務。

本文將在 Azure Kubernetes Service (AKS) 叢集中逐步執行 [NGINX 輸入控制器][nginx-ingress]的範例部署。 此外，也會使用 [cert-manager][cert-manager] 專案自動產生和設定 [Let's Encrypt][lets-encrypt] 憑證。 最後，將會有數個應用程式執行於 AKS 叢集中，且全都可透過同一個位址來存取。

## <a name="install-an-ingress-controller"></a>安裝輸入控制器

使用 Helm 安裝 NGINX 輸入控制器。 如需詳細的部署資訊，請參閱 NGINX 輸入控制器[文件][nginx-ingress]。

此範例將控制器安裝在 `kube-system` 命名空間，這可以修改為您選擇的命名空間。 如果您的 AKS 叢集並未啟用 RBAC，請將 `--set rbac.create=false` 加入命令中。 如需詳細資訊，請參閱 [nginx-ingress 圖表][nginx-ingress]。

```bash
helm install stable/nginx-ingress --namespace kube-system
```

在安裝期間，會為輸入控制器建立 Azure 公用 IP 位址。 若要取公用 IP 位址，請使用 kubectl get service 命令。 將 IP 位址指派給服務可能需要一些時間。

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

由於尚未建立任何輸入規則，如果您瀏覽至公用 IP 位址，將會被導向至 NGINX 輸入控制器預設 404 頁面。

![預設 NGINX 後端](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>設定 DNS 名稱

由於會使用 HTTPS 憑證，因此您必須設定輸入控制器 IP 位址的 FQDN 名稱。 在此範例中，會使用 Azure CLI 建立 Azure FQDN。 請以輸入控制器的 IP 位址和您要在 FQDN 中使用的名稱，來更新指令碼。

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

輸入控制器現在應可透過 FQDN 來存取。

## <a name="install-cert-manager"></a>安裝 cert-manager

NGINX 輸入控制器支援 TLS 終止。 有數種方式可擷取及設定適用於 HTTPS 的憑證，本文件示範的是使用 [cert-manager][cert-manager]，此方式會提供自動產生 [Lets Encrypt][lets-encrypt] 憑證的功能和管理功能。

若要安裝 cert-manager 控制器，請使用下列 Helm 安裝命令。

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

如果您的叢集並未啟用 RBAC，請使用此命令。

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

如需 cert-manager 設定的詳細資訊，請參閱 [cert-manager 專案][cert-manager]。

## <a name="create-ca-cluster-issuer"></a>建立 CA 叢集簽發者

簽發憑證之前，cert-manager 需要 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 資源。 這些資源在功能上完全相同，但是 `Issuer` 會在單一命名空間中運作，而 `ClusterIssuer` 會跨所有命名空間運作。 如需詳細資訊，請參閱 [cert-manager issuer][cert-manager-issuer] 文件。

使用下列資訊清單來建立叢集簽發者。 利用您組織中的有效電子郵件地址來更新電子郵件地址。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>建立憑證物件

接下來，必須建立憑證資源。 憑證資源會定義所需的 X.509 憑證。 如需詳細資訊，請參閱 [cert-manager 憑證][cert-manager-certificates]。

使用下列資訊清單來建立憑證資源。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>執行應用程式

至此，輸入控制器和憑證管理解決方案皆已設定。 現在，請在您的 AKS 叢集中執行一些應用程式。

在此範例中，會使用 Helm 來執行簡單的 hello world 應用程式的多個執行個體。

執行應用程式前，請在您的開發系統上新增 Azure 範例 Helm 存放庫。

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用下列命令執行 AKS hello world 圖表：

```bash
helm install azure-samples/aks-helloworld
```

現在，請安裝 hello world 應用程式的第二個執行個體。

對於第二個執行個體指定新的標題，以便在視覺上輕易區分兩個應用程式。 您也需要指定唯一的服務名稱。 您可以在下列命令中看到這些組態。

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行，不過是以 `ClusterIP` 類型的服務設定的。 因此，這些應用程式無法從網際網路存取。 若要使其可供使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

建立檔案名稱 `hello-world-ingress.yaml`，然後將下列 YAML 複製進來。

請注意，傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量會路由至名為 `aks-helloworld` 的服務。 傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量會路由至 `ingress-demo` 服務。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

使用 `kubectl apply` 命令建立輸入資源。

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>測試輸入組態

瀏覽至您的 Kubernetes 輸入控制器的 FQDN，您應該會看到 hello world 應用程式。

![應用程式範例一](media/ingress/app-one.png)

現在，經由 `/hello-world-two` 路徑瀏覽至 Kubernetes 輸入控制器的 FQDN，您應該會看到具有自訂標題的 hello world 應用程式。

![應用程式範例二](media/ingress/app-two.png)

同時也請注意，連線會加密，且會使用 Let's Encrypt 所發出的憑證。

![Let’s Encrypt 憑證](media/ingress/certificate.png)

## <a name="next-steps"></a>後續步驟

深入了解本文所示範的軟體。

- [Helm CLI][helm-cli]
- [NGINX 輸入控制器][nginx-ingress]
- [cert-manager][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
