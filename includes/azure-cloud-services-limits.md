---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886280"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| [每一部署的 Web 背景工作角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [執行個體輸入端點](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint)  |25 |25 |
| [輸入端點](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint)  |25 |25 |
| [內部端點](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint)  |25 |25 |
| 每個部署的[託管服務憑證](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>具有背景工作角色的每個雲端服務均可有兩個部署，一個供生產環境使用，另一個供接移環境使用。 另請注意，此限制是指個別角色的數目 (組態)，而不是每一角色的執行個體數目 (調整)。

