---
title: Azure Load Balancer 的安全性屬性
description: 評估 Load Balancer 的安全性屬性檢查清單
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440885"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure Load Balancer 的安全性屬性

本文記載 Azure Load Balancer 內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | N/A | |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| N/A | |
| 加密金鑰處理 (CMK、BYOK 等)| N/A | |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | . |
| 網路隔離和防火牆支援| N/A |  |
| 強制通道支援| N/A | |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[公用基本 Load Balancer 的 Azure 監視器記錄](load-balancer-monitor-log.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| N/A |  |
| Authorization| N/A |  |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 請參閱[公用基本 Load Balancer 的 Azure 監視器記錄](load-balancer-monitor-log.md)。 |
| 資料平面記錄和審核 | N/A |  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| N/A |  | 
