---
title: Azure 服務匯流排轉送的安全性屬性
description: 評估 Azure 服務匯流排轉送的安全性屬性檢查清單
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443871"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Azure 服務匯流排轉送的安全性屬性

本文記載 Azure 服務匯流排轉送內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)|  N/A | 轉送是 web 通訊端, 不會保存資料。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | 服務需要 TLS。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 | 僅使用 Microsoft TLS 憑證。  |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | IP-HTTPS. |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| 網路隔離和防火牆支援| 否 |  |
| 強制通道支援| N/A | 轉送是 TLS 通道  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 透過 SAS。 |
| Authorization|  是 | 透過 SAS。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |
| 資料平面記錄和審核| 是 | 連接成功/失敗及已記錄的錯誤。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。|
