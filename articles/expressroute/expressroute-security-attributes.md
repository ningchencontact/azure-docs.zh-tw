---
title: Azure ExpressRoute 的安全性屬性
description: 評估 Azure ExpressRoute 的安全性屬性檢查清單
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442193"
---
# <a name="security-attributes-for-azure-expressroute"></a>Azure ExpressRoute 的安全性屬性

本文記載 Azure ExpressRoute 內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)|  N/A | ExpressRoute 不會儲存客戶資料。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 否 | |
| 加密金鑰處理 (CMK、BYOK 等)| N/A |  |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A |  |
| VNet 插入支援| N/A | |
| 網路隔離和防火牆支援| 是 | 每個客戶都包含在自己的路由網域中, 並通道傳送至自己的 VNet |
| 強制通道支援| N/A | Via 邊界閘道協定 (BGP)。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[ExpressRoute 監視、計量和警示](expressroute-monitoring-metrics-alerts.md)。|

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 適用于 Microsoft 的閘道服務帳戶 (GWM) (控制器);適用于 Dev 和 OP 的即時 (JIT) 存取。 |
| Authorization|  是 |適用于 Microsoft 的閘道服務帳戶 (GWM) (控制器);適用于 Dev 和 OP 的即時 (JIT) 存取。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意| 
|---|---|--|
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核| 否 |   |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過網路資源提供者 (NRP)。 |
