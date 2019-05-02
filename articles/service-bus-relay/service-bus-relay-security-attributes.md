---
title: Azure 服務匯流排轉送的常見安全性屬性
description: 常見的安全性屬性，來評估 Azure 服務匯流排轉送的檢查清單
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927488"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Azure 服務匯流排轉送的常見安全性屬性

Azure 服務的各個層面都整合了安全性。 這篇文章會說明常見的安全性屬性，內建 Azure 服務匯流排轉送。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>|  N/A | 轉送是 web 通訊端，並不會保存資料。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 服務需要 TLS。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 | 使用只在 Microsoft 的 TLS 憑證。  |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| 網路隔離，而且防火牆支援| 否 |  |
| 強制通道的支援| N/A | 轉送是 TLS 通道  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 透過 SAS。 |
| 授權|  是 | 透過 SAS。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |
| 資料平面記錄與稽核| 是 | 連接成功 / 失敗和錯誤記錄。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。|
