---
title: "為 Azure Container Service (AKS) 叢集設定輸入"
description: "在 Azure Container Service (AKS) 叢集中安裝及設定 NGINX 輸入控制器。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c25a0171bd412050a7c94e9b077436cd1ebe893b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>Azure Container Service (AKS) 上的 HTTPS 輸入

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您將可使用單一外部位址將流量路由至 Kubernetes 叢集中的多個服務。

本文將在 Azure Container Service (AKS) 叢集中逐步執行 [NGINX 輸入控制器][nginx-ingress]的範例部署。 此外也會使用 [KUBE-LEGO][kube-lego] 專案自動產生和設定 [Let's Encrypt][lets-encrypt] 憑證。 最後，將會有數個應用程式執行於 AKS 叢集中，且全都可透過同一個位址來存取。

## <a name="install-an-ingress-controller"></a>安裝輸入控制器

使用 Helm 安裝 NGINX 輸入控制器。 如需詳細的部署資訊，請參閱 NGINX 輸入控制器[文件][nginx-ingress]。 

```
helm install stable/nginx-ingress
```

在安裝期間，會為輸入控制器建立 Azure 公用 IP 位址。 若要取公用 IP 位址，請使用 kubectl get service 命令。 將 IP 位址指派給服務可能需要一些時間。

```console
$ kubectl get service

NAME                                          TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
kubernetes                                    ClusterIP      10.0.0.1       <none>           443/TCP                      3d
toned-spaniel-nginx-ingress-controller        LoadBalancer   10.0.236.223   52.224.125.195   80:30927/TCP,443:31144/TCP   18m
toned-spaniel-nginx-ingress-default-backend   ClusterIP      10.0.5.86      <none>           80/TCP                       18m
```

由於尚未建立任何輸入規則，如果您瀏覽至公用 IP 位址，將會被導向至 NGINX 輸入控制器預設 404 頁面。

![預設 NGINX 後端](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>設定 DNS 名稱

由於會使用 HTTPS 憑證，因此您必須設定輸入控制器 IP 位址的 FQDN 名稱。 在此範例中，會使用 Azure CLI 建立 Azure FQDN。 請以輸入控制器的 IP 位址和您要在 FQDN 中使用的名稱，來更新指令碼。

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

如有需要，請執行下列命令以擷取 FQDN。 以您輸入控制器的 IP 位址值更新 IP 位址值。

```azurecli
az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '52.224.125.195')].[dnsSettings.fqdn]" --output tsv
```

輸入控制器現在已可透過 FQDN 來存取。

## <a name="install-kube-lego"></a>安裝 KUBE-LEGO

NGINX 輸入控制器支援 TLS 終止。 有數種方式可擷取及設定 HTTPS 憑證，本文示範的是使用 [KUBE-LEGO][kube-lego]，此方式會提供自動產生 [Lets Encrypt][lets-encrypt] 憑證的功能和管理功能。

若要安裝 KUBE-LEGO 控制器，請使用下列 Helm 安裝命令。 以您的組織的其中一個電子郵件地址更新電子郵件地址。

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

如需 KUBE-LEGO 組態的詳細資訊，請參閱 [KUBE-LEGO 文件][kube-lego]。

## <a name="run-application"></a>執行應用程式

至此，輸入控制器和憑證管理解決方案皆已設定。 現在，請在您的 AKS 叢集中執行一些應用程式。

在此範例中，會使用 Helm 來執行簡單的 hello world 應用程式的多個執行個體。

執行應用程式前，請在您的開發系統上新增 Azure 範例 Helm 存放庫。

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 使用下列命令執行 AKS hello world 圖表：

```
helm install azure-samples/aks-helloworld
```

現在，請安裝 hello world 應用程式的第二個執行個體。

對於第二個執行個體指定新的標題，以便在視覺上輕易區分兩個應用程式。 您也需要指定唯一的服務名稱。 您可以在下列命令中看到這些組態。

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行，不過是以 `ClusterIP` 類型的服務設定的。 因此，這些應用程式無法從網際網路存取。 若要使其可供使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

建立檔案名稱 `hello-world-ingress.yaml`，然後將下列 YAML 複製進來。

請注意，傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量會路由至名為 `aks-helloworld` 的服務。 傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量會路由至 `ingress-demo` 服務。

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
    ingress.kubernetes.io/rewrite-target: /
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

- [NGINX 輸入控制器][nginx-ingress]
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
