---
title: 常見的 Azure 服務匯流排傳訊的安全性屬性
description: 常見的安全性屬性，來評估 Azure 服務匯流排傳訊的檢查清單
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513525"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>常見的 Azure 服務匯流排傳訊的安全性屬性

Azure 服務的各個層面都整合了安全性。 這篇文章會說明常見內建於 Azure 服務匯流排傳訊的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>|  依預設是伺服器端加密靜止。 | 客戶管理的金鑰和 BYOK 尚不支援。 用戶端加密是用戶端的責任 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支援標準的 HTTPS/TLS 機制。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 |   |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | API 呼叫都會經過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 （進階層） | 支援 VNet 服務端點[服務匯流排進階層](service-bus-premium-messaging.md)只。 |
| VNet 插入支援| 否 | |
| 網路隔離，而且防火牆支援| 是 （進階層） |  |
| 強制通道的支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 支援透過[Azure 監視器和警示](service-bus-metrics-azure-monitor.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 透過管理[Azure Active Directory 受控服務識別](service-bus-managed-service-identity.md); 請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。|
| 授權| 是 | 支援透過授權[RBAC](service-bus-role-based-access-control.md) （預覽） 和 SAS 權杖，請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。 |



## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 作業記錄檔會使用;請參閱[服務匯流排診斷記錄](service-bus-diagnostic-logs.md)。  |
| 資料平面記錄與稽核| 否 |  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 支援資源提供者版本設定，透過[Azure Resource Manager API](/rest/api/resources/)。|
