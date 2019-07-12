---
title: Azure API 管理的安全性屬性
description: 常見的安全性屬性，來評估 API 管理的檢查清單
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b5826d472b80179c5eb76e0e3a6b1c7ee282487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001082"
---
# <a name="security-attributes-for-api-management"></a>適用於 API 管理的安全性屬性

這篇文章說明 API 管理內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 （只在服務端加密） | 機密資料，例如憑證、 金鑰和祕密的值會以加密受服務管理，每個服務執行個體索引鍵。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | [Express Route](../expressroute/index.yml)由所提供 VNet 加密[Azure 網路](../virtual-network/index.yml)。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 | 所有加密金鑰是每個服務執行個體，受管理的服務。 |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 管理平面呼叫都會經過[Azure Resource Manager](../azure-resource-manager/index.yml)透過 TLS。 需要有效的 JSON Web 權杖 (JWT)。  資料平面的呼叫可以保護使用 TLS 和其中一個支援的驗證機制 （例如，用戶端憑證或 JWT）。
 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離，而且防火牆支援| 是 | 使用網路安全性群組 (NSG) 和 Azure 應用程式閘道 （或其他軟體應用裝置） 分別。 |
| 強制通道的支援| 是 | Azure 網路提供強制通道功能。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | |
| Authorization| 是 | |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | [Azure 監視器活動記錄檔](../azure-monitor/platform/activity-logs-overview.md) |
| 資料平面記錄與稽核| 是 | [Azure 監視器的診斷記錄](../azure-monitor/platform/diagnostic-logs-overview.md)及 （選擇性） [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 使用[Azure API 管理 DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>弱點掃描誤判

本節說明常見的弱點，並不會影響 Azure API 管理。

| 弱點               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016年-9244) | Ticketbleed 是某些 F5 產品中找到 TLS SessionTicket 延伸模組實作中的弱點。 它可讓 （「 高度 」） 的最多 31 個位元組的未初始化的記憶體中的資料外洩。 這被因為填補工作階段識別碼，從用戶端，讓 32 位元的資料傳遞的 TLS 堆疊。 |
