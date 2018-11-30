---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279506"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| [每一部署的 Web 背景工作角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [執行個體輸入端點](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint)  |25 |25 |
| [輸入端點](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint)  |25 |25 |
| [內部端點](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint)  |25 |25 |

<sup>1</sup>具有背景工作角色的每個雲端服務均可有兩個部署，一個供生產環境使用，另一個供接移環境使用。 另請注意，此限制是指個別角色的數目 (組態)，而不是每一角色的執行個體數目 (調整)。

