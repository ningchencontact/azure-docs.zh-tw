---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174253"
---
## <a name="validate-that-a-container-is-running"></a>驗證容器正在執行 

有數種方式可驗證容器正在執行。 

|要求|目的|
|--|--|
|`http://localhost:5000/`|容器會提供首頁。|
|`http://localhost:5000/status`|透過 GET 提出要求，以驗證容器正在執行，而不會導致端點查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。|
|`http://localhost:5000/swagger`|容器提供了一組完整的端點文件集及 `Try it now` 功能。 使用此功能，您可以將自己的設定輸入至以 Web 為基礎的 HTML 表單並進行查詢，而無須撰寫任何程式碼。 當查詢傳回時，會提供範例 CURL 命令來示範所需的 HTTP 標頭和本文格式。 |

![容器的首頁](./media/cognitive-services-containers-api-documentation/container-webpage.png)
