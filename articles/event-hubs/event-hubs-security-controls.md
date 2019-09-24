---
title: Azure 事件中樞的安全性控制項
description: 評估 Azure 事件中樞的安全性控制檢查清單
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219404"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure 事件中樞的安全性控制項

本文記載內建在 Azure 事件中樞中的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務端點支援| 是 |  |  |
| VNet 插入支援| 否 | |  |
| 網路隔離和防火牆支援| 是 |  |  |
| 強制通道支援| 否 |  |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | |  |
| 控制和管理平面記錄和審核| 是 |  |  |
| 資料平面記錄和審核| 是 |   |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | | [授權存取 Azure 事件中樞](authorize-access-event-hubs.md)、[使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)、[使用共用存取簽章授權存取事件中樞資源](authorize-access-shared-access-signature.md) |
| Authorization|  是 | | 使用[Azure Active Directory 驗證受控識別以存取事件中樞資源](authenticate-managed-identity.md)、[驗證具有 Azure Active Directory 的應用程式以存取事件中樞資源](authenticate-application.md)、[驗證事件中樞資源的存取權共用存取簽章（SAS）](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 待用的伺服器端加密：Microsoft 管理的金鑰 |  是 | |  |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 否 |  |  |
| 資料行層級加密 (Azure 資料服務)| N/A | |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | |  |
| API 呼叫加密| 是 |  |  |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 設定管理支援 (設定的版本設定等)| 是 | |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
