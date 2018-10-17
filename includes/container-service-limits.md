---
title: 包含檔案
description: 包含檔案
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874107"
---
| 資源 | 預設限制 |
| --- | :--- |
| 每個訂用帳戶的最大叢集數 | 100 |
| 每個叢集的最大節點數 | 100 |
| 每個節點的最大 Pod 數：採用 Kubenet 的[基本網路功能][basic-networking] | 110 |
| 每個節點的最大 Pod 數：採用 Azure CNI 的[進階網路功能][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Resource Manager 範本：30<sup>1</sup><br />入口網站部署：30 |

<sup>1</sup> 使用 Azure CLI 或 Resource Manager 範本來部署 AKS 叢集時，可在叢集部署上設定這個值。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
