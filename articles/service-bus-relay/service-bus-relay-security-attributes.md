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
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000157"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Azure 服務匯流排轉送的安全性屬性

這篇文章說明 Azure 服務匯流排轉送內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>|  N/A | 轉送是 web 通訊端，並不會保存資料。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 有 | 服務需要 TLS。 |
| 加密金鑰處理 （CMK、 BYOK）| 無 | 使用只在 Microsoft 的 TLS 憑證。  |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 有 | HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 無 |  |
| 網路隔離，而且防火牆支援| 無 |  |
| 強制通道的支援| N/A | 轉送是 TLS 通道  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 有 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 有 | 透過 SAS。 |
| 授權|  有 | 透過 SAS。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 有 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |
| 資料平面記錄與稽核| 有 | 連接成功 / 失敗和錯誤記錄。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 有 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。|
