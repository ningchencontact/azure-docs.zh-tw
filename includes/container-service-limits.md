---
title: 包含檔案
description: 包含檔案
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "67174342"
---
| Resource | 預設限制 |
| --- | :--- |
| 每個訂用帳戶的群集上限 | 100 |
| 每個叢集的節點數目上限 | 100 |
| 每個節點的最大 pod 數：使用 Kubenet 的[基本網路][basic-networking]功能 | 110 |
| 每個節點的最大 pod 數：使用 Azure Container 網路介面的[Advanced 網路][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure Resource Manager 範本：30<sup>1</sup><br />入口網站部署：30 |

<sup>1</sup>當您使用 Azure CLI 或 Resource Manager 範本部署 Azure Kubernetes Service （AKS）叢集時，此值最多可設定為每個節點250個 pod。 您無法在已部署 AKS 叢集之後，或使用 Azure 入口網站部署叢集時，設定每個節點的最大 pod 數。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
