---
title: 包含檔案
description: 包含檔案
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667050"
---
| 資源 | 預設限制 |
| --- | :--- |
| 每個訂用帳戶的最大叢集數 | 100 |
| 每個叢集的最大節點數 | 100 |
| 每個節點的最大 Pod 數：採用 Kubenet 的[基本網路功能][basic-networking] | 110 |
| 每個節點的最大 Pod 數：採用 Azure CNI 的[進階網路功能][advanced-networking] | Azure CLI 部署：110<sup>1</sup><br />Resource Manager 範本：110<sup>1</sup><br />入口網站部署：30 |

<sup>1</sup> 使用 Azure CLI 或 Resource Manager 範本來部署 AKS 叢集時，可在叢集部署上設定這個值。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
