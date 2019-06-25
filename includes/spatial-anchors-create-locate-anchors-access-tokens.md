---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173674"
---
### <a name="access-tokens"></a>存取權杖

存取權杖是更強固的方法，來向 Azure 空間的錨點。 特別是當您準備應用程式的生產環境部署。 若要設定用戶端應用程式可以安全地向後端服務是這種方法的摘要。 在執行階段的 AAD 與 Azure 空間的錨點安全權杖服務來要求存取權杖與您後端服務介面。 這個語彙基元然後傳遞至用戶端應用程式，並在 SDK 中用來向 Azure 空間的錨點。
