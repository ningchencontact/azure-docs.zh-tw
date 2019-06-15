---
title: 適用於 Azure ExpressRoute 的常見安全性屬性
description: 常見的安全性屬性，來評估 Azure ExpressRoute 的檢查清單
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083271"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>適用於 Azure ExpressRoute 的常見安全性屬性

Azure 服務的各個層面都整合了安全性。 這篇文章說明內建於 Azure ExpressRoute 的常見安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>|  N/A | ExpressRoute 不會儲存客戶資料。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 否 | |
| 加密金鑰處理 （CMK、 BYOK）| N/A |  |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A |  |
| vNET 插入支援| N/A | |
| 網路隔離，而且防火牆支援| 是 | 每個客戶都包含在它自己的路由網域，並且通道傳送至它自己的 VNet |
| 強制通道的支援| N/A | 透過邊界閘道協定 (BGP)。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 請參閱[ExpressRoute 監視計量和警示](expressroute-monitoring-metrics-alerts.md)。|

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | Microsoft (GWM) （控制器），閘道服務帳戶Just-in-time (JIT) 存取進行開發和 OP. |
| 授權|  是 |Microsoft (GWM) （控制器），閘道服務帳戶Just-in-time (JIT) 存取進行開發和 OP. |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意| 
|---|---|--|
| 控制和管理平面記錄與稽核| 是 |  |
| 資料平面記錄與稽核| 否 |   |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 透過網路的資源提供者 (NRP) 中。 |
