---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 瞭解如何保護您的容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994918"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 認知服務容器安全性

在開發應用程式時，安全性應該是主要焦點。 安全性的重要性是成功的度量。 當您架構包含認知服務容器的軟體解決方案時，請務必瞭解您可用的限制和功能。 如需詳細資訊，請參閱[Azure 安全性][az-security]。

> [!IMPORTANT]
> 根據預設，認知服務容器 API*沒有安全性*。 這是因為容器最常作為 pod 的一部分來執行，而這是由網路橋接器的外部保護。 不過，您可以啟用驗證，其運作方式與存取[雲端式認知服務][request-authentication]時所使用的驗證相同。

下圖說明預設和不**安全**的方法：

![容器安全性](../media/container-security.svg)

作為另一個*安全*的方法，認知服務容器的取用者可以使用前方元件來增加容器，並將容器端點設為私用。 讓我們來看一個案例，我們會使用[Istio][istio]作為輸入閘道。 Istio 支援 HTTPS/SSL 和用戶端憑證驗證。 在此案例中，Istio 前端會公開容器存取權，呈現預先列入 Istio 的用戶端憑證。

[Nginx][nginx]是相同類別中另一個常用的選擇。 Istio 和 Nginx 都扮演服務網格，並提供額外的功能，包括負載平衡、路由及速率控制等專案。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
