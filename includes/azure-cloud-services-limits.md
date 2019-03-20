---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553412"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| [每個部署的 web 或背景工作角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [執行個體輸入端點](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint)每個部署 |25 |25 |
| [輸入端點](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint)每個部署 |25 |25 |
| [內部端點](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint)每個部署 |25 |25 |
| 每個部署的[託管服務憑證](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>與 web 或背景工作角色的每個 Azure 雲端服務可以有兩個部署，一個用於生產環境，一個用於預備環境。 這項限制是指不同的角色，也就是組態的數目。 這項限制不參考執行個體，每個角色，也就是調整。

