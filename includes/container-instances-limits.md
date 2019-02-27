---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: dd5c8878ce71b49b3a25f5d14a00bfe4f49ee769
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334014"
---
| 資源 | 預設限制 |
| --- | :--- |
| 每一訂用帳戶的[容器群組](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| 每個容器群組的容器數目 | 60 |
| 每個容器群組的磁碟區數目 | 20 |
| 每個 IP 的連接埠數目 | 5 |
| 容器執行個體記錄大小 - 執行中的執行個體 | 4 MB |
| 容器執行個體記錄大小 - 已停止的執行個體 | 16 KB 或 1000 行 |
| 每小時的容器建立 |300<sup>1</sup> |
| 每 5 分鐘的容器建立 | 100<sup>1</sup> |
| 每小時的容器刪除 | 300<sup>1</sup> |
| 每 5 分鐘的容器刪除 | 100<sup>1</sup> |


<sup>1</sup> 建立 [Azure 支援要求][azure-support]可要求提高限制。<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
