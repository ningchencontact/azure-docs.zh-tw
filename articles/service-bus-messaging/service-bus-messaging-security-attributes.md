---
title: Azure 服務匯流排訊息的安全性屬性
description: 評估 Azure 服務匯流排訊息的安全性屬性檢查清單
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 875754282f9e7a2b8dd92b0eb2d7e277ac842696
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013100"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Azure 服務匯流排訊息的安全性屬性

本文記載 Azure 服務匯流排訊息內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)|  [是] 表示預設會使用伺服器端靜態加密。 | 尚不支援客戶管理的金鑰和 BYOK。 用戶端的加密是用戶端的責任 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | 支援標準 HTTPS/TLS 機制。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |   |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | API 呼叫是透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS 進行。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 (僅限 Premium 層) | 只有[服務匯流排](service-bus-premium-messaging.md)進階層支援 VNet 服務端點。 |
| VNet 插入支援| 否 | |
| 網路隔離和防火牆支援| 是 (僅限 Premium 層) |  |
| 強制通道支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 透過[Azure 監視器和警示](service-bus-metrics-azure-monitor.md)來支援。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 透過[Azure Active Directory 受控服務識別](service-bus-managed-service-identity.md)進行管理;請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。|
| Authorization| 是 | 支援透過[RBAC](authenticate-application.md)和 SAS 權杖進行授權;請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。 |



## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 作業記錄可供使用;請參閱[服務匯流排診斷記錄](service-bus-diagnostic-logs.md)。  |
| 資料平面記錄和審核| 否 |  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過[AZURE RESOURCE MANAGER API](/rest/api/resources/)支援資源提供者版本設定。|
