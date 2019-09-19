---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: f8821060b98ebfc954a6e59abad60350e6779b76
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "67174344"
---
| Resource | 預設限制 |
| --- | :--- |
| 每一訂用帳戶的[容器群組](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| 每個容器群組的容器數目 | 60 |
| 每個容器群組的磁碟區數目 | 20 |
| 每個 IP 的連接埠數目 | 5 |
| 容器執行個體記錄大小 - 執行中的執行個體 | 4 MB |
| 容器執行個體記錄大小 - 已停止的執行個體 | 16 KB 或 1,000 行 |
| 每小時的容器建立 |300<sup>1</sup> |
| 每 5 分鐘的容器建立 | 100<sup>1</sup> |
| 每小時的容器刪除 | 300<sup>1</sup> |
| 每 5 分鐘的容器刪除 | 100<sup>1</sup> |


<sup>1</sup>若要要求增加限制，請建立[Azure 支援要求][azure-support]。<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
