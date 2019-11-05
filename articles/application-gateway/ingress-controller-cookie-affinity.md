---
title: 啟用以 cookie 為基礎的親和性應用程式閘道
description: 本文提供如何使用應用程式閘道啟用 cookie 型親和性的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513571"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>啟用以 Cookie 為基礎的親和性與應用程式閘道
如[Azure 應用程式閘道檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)中所述，應用程式閘道支援以 cookie 為基礎的親和性，這表示它可以將來自使用者會話的後續流量導向至同一部伺服器進行處理。

## <a name="example"></a>範例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
