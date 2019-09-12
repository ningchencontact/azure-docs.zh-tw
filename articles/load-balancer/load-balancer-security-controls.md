---
title: Azure Load Balancer 的安全性控制項
description: 評估 Load Balancer 的安全性控制檢查清單
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e0be6635a0916183e1dfe776bef4c547578383dc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886738"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure Load Balancer 的安全性控制項

本文記載內建在 Azure Load Balancer 中的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | |
| 網路隔離和防火牆支援| N/A |  |
| 強制通道支援| N/A | |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[公用基本 Load Balancer 的 Azure 監視器記錄](load-balancer-monitor-log.md)。 |
| 控制和管理平面記錄和審核| 是 | 請參閱[公用基本 Load Balancer 的 Azure 監視器記錄](load-balancer-monitor-log.md)。 |
| 資料平面記錄和審核 | N/A |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| N/A |  |
| Authorization| N/A |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密：Microsoft 管理的金鑰 | N/A | |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| N/A | |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | N/A | |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| N/A |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。