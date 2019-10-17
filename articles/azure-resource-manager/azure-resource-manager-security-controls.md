---
title: Azure Resource Manager 的安全性控制項
description: 用於評估 Azure Resource Manager 服務的內建安全性控制檢查清單。
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 78f3f41ca2f0aa45d295fd9846cf3aa96cccea72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390471"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure Resource Manager 的安全性控制項

本文記載內建在 Azure Resource Manager 中的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | HTTPS/TLS。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | N/A | Azure Resource Manager 不會儲存任何客戶內容，只會儲存控制資料。 |
| 資料行層級加密（Azure 資料服務）| 是 | |
| API 呼叫加密| 是 | |

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 否 |  |
| 強制通道支援| 否 |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 否 | |
| 控制和管理平面記錄和審核| 是 | 活動記錄會公開您資源上執行的所有寫入作業（PUT、POST、DELETE）;請參閱[查看活動記錄以在資源上審核動作](resource-group-audit.md)。 |
| 資料平面記錄和審核| N/A | |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 以[Azure Active Directory](/azure/active-directory)為基礎。|
| Authorization| 是 | |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。