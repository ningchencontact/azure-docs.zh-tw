---
title: Azure VPN 閘道的安全性屬性
description: 評估 Azure VPN 閘道的安全性屬性檢查清單
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444592"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Azure VPN 閘道的安全性屬性

本文記載 Azure VPN 閘道內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | N/A | VPN 閘道傳輸客戶資料, 不會儲存客戶資料 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | VPN 閘道會加密 Azure VPN 閘道與客戶內部部署 VPN 裝置 (S2S) 或 VPN 用戶端 (P2S) 之間的客戶封包。 VPN 閘道也支援 VNet 對 VNet 加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 | 客戶指定的預先共用金鑰會在待用時加密;但尚未與 CMK 整合。 |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | . |
| 網路隔離和防火牆支援| 是 | VPN 閘道是每個客戶的專用 VM 實例虛擬網路  |
| 強制通道支援| 是 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[Azure 監視器診斷記錄/警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) &  [Azure 監視器計量/警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 用於管理服務和設定 Azure VPN 閘道的[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 。 |
| Authorization| 是 | 透過[RBAC](../role-based-access-control/overview.md)支援授權。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | Azure Resource Manager 活動記錄。 |
| 資料平面記錄和審核 | 是 | Azure 監視器 VPN 連線記錄和審核的[診斷記錄](../azure-resource-manager/resource-group-audit.md)。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 針對管理作業, Azure VPN 閘道設定的狀態可以匯出為 Azure Resource Manager 範本, 並在一段時間後進行版本設定。 | 