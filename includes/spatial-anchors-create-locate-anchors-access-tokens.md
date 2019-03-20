---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751671"
---
### <a name="access-tokens"></a>存取權杖

存取權杖是更強固的方法，來向 Azure 空間的錨點。 特別是當您準備應用程式的生產環境部署。 若要設定用戶端應用程式可以安全地向後端服務是這種方法的摘要。 您的後端服務介面與在執行階段的 AAD，並與 Azure 空間的錨點 STS 的服務，以要求存取權杖。 這個語彙基元然後傳遞至用戶端應用程式，並在 SDK 中用來向 Azure 空間的錨點。
