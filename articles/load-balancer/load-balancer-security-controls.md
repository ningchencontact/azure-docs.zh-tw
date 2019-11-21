---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214907"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| Security control | 是/否 | 注意 |
|---|---|--|
| Service endpoint support| N/A | |
| VNet injection support| N/A | |
| Network Isolation and Firewalling support| N/A |  |
| Forced tunneling support| N/A | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | 是/否 | 注意|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| 是 | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| 是 | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | N/A |  |

## <a name="identity"></a>身分識別

| Security control | 是/否 | 注意|
|---|---|--|
| Authentication| N/A |  |
| Authorization| N/A |  |

## <a name="data-protection"></a>資料保護

| Security control | 是/否 | 注意 |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | N/A | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| N/A | |
| Server-side encryption at rest: customer-managed keys (BYOK) | N/A | |
| Column level encryption (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>設定管理

| Security control | 是/否 | 注意|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| N/A |  | 

## <a name="next-steps"></a>後續步驟

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).