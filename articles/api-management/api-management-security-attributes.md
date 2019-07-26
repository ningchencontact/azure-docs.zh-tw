---
title: Azure API 管理的安全性屬性
description: 用於評估 API 管理的安全性屬性檢查清單
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442289"
---
# <a name="security-attributes-for-api-management"></a>API 管理的安全性屬性

本文記載 API 管理內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)| 是 (僅限服務端加密) | 機密資料 (例如憑證、金鑰和密碼名稱值) 會使用服務管理的每個服務實例金鑰進行加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | [Express Route](../expressroute/index.yml)和 VNet 加密是由[Azure 網路](../virtual-network/index.yml)提供。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 | 所有加密金鑰都是針對每個服務實例, 而且受服務管理。 |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 管理平面呼叫是透過 TLS 的[Azure Resource Manager](../azure-resource-manager/index.yml)來進行。 需要有效的 JSON Web 權杖 (JWT)。  您可以使用 TLS 和支援的其中一種驗證機制 (例如用戶端憑證或 JWT) 來保護資料平面呼叫。
 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 | 分別使用網路安全性群組 (NSG) 和 Azure 應用程式閘道 (或其他軟體應用裝置)。 |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | |
| Authorization| 是 | |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | [Azure 監視器活動記錄](../azure-monitor/platform/activity-logs-overview.md) |
| 資料平面記錄和審核| 是 | [Azure 監視器診斷記錄](../azure-monitor/platform/diagnostic-logs-overview.md)和 (選擇性) [Azure 應用程式深入](../azure-monitor/app/app-insights-overview.md)解析。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 使用[AZURE API 管理 DevOps 資源套件](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>弱點掃描誤報

本節記載常見的弱點, 這不會影響 Azure API 管理。

| 漏洞               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed 是在某些 F5 產品中找到的 TLS SessionTicket 擴充功能的弱點。 它允許從未初始化的記憶體中, 最多31個位元組的資料洩漏 (「不規則」)。 這是由 TLS 堆疊填補從用戶端傳遞的會話識別碼所造成, 其資料會使其32位長。 |
