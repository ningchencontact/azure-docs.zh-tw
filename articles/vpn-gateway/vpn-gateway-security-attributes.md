---
title: Azure VPN 閘道的安全性屬性
description: 評估 Azure VPN 閘道的安全性屬性的檢查清單
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083128"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Azure VPN 閘道的安全性屬性

這篇文章會說明常見的安全性屬性，內建到 Azure VPN 閘道。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 靜態加密 （例如伺服器端加密，使用客戶管理的金鑰，伺服器端加密和其他加密功能） | N/A | VPN 閘道傳輸客戶資料，不會儲存客戶資料 |
| （例如 ExpressRoute 加密，在 VNet 加密和 VNet 對 VNet 加密） 的傳輸中加密| 是 | VPN 閘道會將客戶的 Azure VPN 閘道與客戶的內部部署 VPN 裝置 (S2S) 或 VPN 用戶端 (P2S) 之間的封包加密。 VPN 閘道也支援 VNet 對 VNet 加密。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 | 客戶指定的預先共用的金鑰皆在待用加密;但不是會與整合 CMK 尚未。 |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | 上也提供本文中使用的原始碼。 |
| 網路隔離和 Firewalling 支援| 是 | VPN 閘道是專用的 VM 執行個體，每位客戶虛擬網路  |
| 強制通道的支援| 是 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 請參閱[Azure 監視器診斷記錄檔/警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure 監視器計量警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)管理服務和設定 Azure VPN 閘道。 |
| 授權| 是 | 支援透過授權[RBAC](../role-based-access-control/overview.md)。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | Azure 資源管理員的活動記錄檔。 |
| 資料平面記錄與稽核 | 是 | [Azure 監視器的診斷記錄](../azure-resource-manager/resource-group-audit.md)記錄與稽核的 VPN 連線能力。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 管理作業的 Azure VPN 閘道組態的狀態可以匯出為 Azure Resource Manager 範本和建立版本一段時間。 | 