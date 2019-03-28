---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522500"
---
## <a name="validate-container-is-running"></a>確認容器正在執行 

有數種方式可確認容器正在執行： 

|要求|目的|
|--|--|
|`http://localhost:5000/`|容器會提供首頁。|
|`http://localhost:5000/status`|使用 GET 要求，以驗證容器正在執行而不會造成端點查詢。 這可以用於 Kubernetes[作用和整備探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。|
|`http://localhost:5000/swagger`|容器提供了一組完整的端點文件集以及 `Try it now` 功能。 這項功能可讓您將自己的設定輸入以 Web 為基礎的 HTML 表單中，並直接進行查詢而無須撰寫任何程式碼。 在查詢傳回時，將會提供範例 CURL 命令，以示範所需的 HTTP 標頭和本文格式。 |

![容器的首頁](./media/cognitive-services-containers-api-documentation/container-webpage.png)
