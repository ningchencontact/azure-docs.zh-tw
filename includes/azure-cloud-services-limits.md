---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803580"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| [每個部署的 web 或背景工作角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [執行個體輸入端點](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint)每個部署 |25 |25 |
| [輸入端點](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)每個部署 |25 |25 |
| [內部端點](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint)每個部署 |25 |25 |
| 每個部署的[託管服務憑證](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>與 web 或背景工作角色的每個 Azure 雲端服務可以有兩個部署，一個用於生產環境，一個用於預備環境。 這項限制是指不同的角色，也就是組態的數目。 這項限制不參考執行個體，每個角色，也就是調整。

