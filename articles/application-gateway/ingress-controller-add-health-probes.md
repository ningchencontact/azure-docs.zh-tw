---
title: 將健康情況探查新增至您的 AKS pod
description: 本文提供有關如何使用應用程式閘道，將健康情況探查（準備就緒和/或活動）新增至 AKS pod 的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 073babf407509c9bbf05340edd828c895fa376e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513623"
---
# <a name="add-health-probes-to-your-service"></a>將健康情況探查新增至您的服務
根據預設，輸入控制器會針對公開的 pod 布建 HTTP GET 探查。
您可以在 `deployment`/`pod` 規格中新增[準備就緒或活動探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)，藉以自訂探查屬性。

## <a name="with-readinessprobe-or-livenessprobe"></a>使用 `readinessProbe` 或 `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API 參考：
* [容器探查](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 動作](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * 使用 `httpGet`來設定時，支援 `readinessProbe` 和 `livenessProbe`。
> * 目前不支援在 pod 上公開的埠上進行探查。
> * 不支援 `HttpHeaders`、`InitialDelaySeconds``SuccessThreshold`。

##  <a name="without-readinessprobe-or-livenessprobe"></a>沒有 `readinessProbe` 或 `livenessProbe`
如果未提供上述探查，則輸入控制器會假設在針對 `backend-path-prefix` 注釋指定的 `Path`，或在服務的 `ingress` 定義中指定的 `path` 中，可以連線到服務。

## <a name="default-values-for-health-probe"></a>健全狀況探查的預設值
若為任何無法由準備/活動探查推斷的屬性，則會設定預設值。

| 應用程式閘道探查屬性 | 預設值 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |