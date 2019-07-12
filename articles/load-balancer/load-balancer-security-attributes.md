---
title: Azure 負載平衡器的安全性屬性
description: 檢查清單來評估負載平衡器的安全性屬性
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800077"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure 負載平衡器的安全性屬性

這篇文章說明 Azure Load Balancer 內建的一般安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 靜態加密 （例如伺服器端加密，使用客戶管理的金鑰，伺服器端加密和其他加密功能） | N/A | |
| （例如 ExpressRoute 加密，在 VNet 加密和 VNet 對 VNet 加密） 的傳輸中加密| N/A | |
| 加密金鑰處理 （CMK、 BYOK）| N/A | |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | 。 |
| 網路隔離和 Firewalling 支援| N/A |  |
| 強制通道的支援| N/A | |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 請參閱[Azure 監視器記錄公用基本負載平衡器](load-balancer-monitor-log.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| N/A |  |
| Authorization| N/A |  |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 請參閱[Azure 監視器記錄公用基本負載平衡器](load-balancer-monitor-log.md)。 |
| 資料平面記錄與稽核 | N/A |  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| N/A |  | 
