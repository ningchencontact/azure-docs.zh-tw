---
title: 使用 Java 和 VS Code 透過 Azure Dev Spaces 進行小組開發
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 7b4785e1fbcc9f7946526920113b453c0a811c42
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707178"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>進行程式碼變更
請移至 `mywebapi` 的 VS Code 視窗，並對 `src/main/java/com/ms/sample/mywebapi/Application.java` 中的 `String index()` 方法進行程式碼編輯，例如：

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
