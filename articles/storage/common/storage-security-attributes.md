---
title: Azure 儲存體的一般安全性屬性
description: 常見的安全性屬性，來評估 Azure 儲存體的檢查清單
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 922273e3805004f6af068ea748c16f5675810144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001468"
---
# <a name="security-attributes-for-azure-storage"></a>Azure 儲存體的安全性屬性

這篇文章說明內建於 Azure 儲存體的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 |  |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支援標準的 HTTPS/TLS 機制。  它會傳送至服務之前，使用者也可以加密資料。 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 請參閱[在 Azure Key Vault 中使用客戶管理金鑰的儲存體服務加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 是 |  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| N/A |  |
| 網路隔離，而且防火牆支援| 是 | |
| 強制通道的支援| N/A |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 現在，可用的 azure 監視器計量記錄的開始預覽 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory 中，共用金鑰、 共用存取權杖。 |
| Authorization| 是 | 支援透過 RBAC、 POSIX Acl 中和 SAS 權杖的授權 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核 | 是 | Azure Resource Manager 活動記錄檔 |
| 資料平面記錄與稽核| 是 | 服務診斷記錄和 Azure 監視器記錄開始預覽  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 支援透過 Azure Resource Manager Api 的資源提供者版本控制 |