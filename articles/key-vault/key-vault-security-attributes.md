---
title: Azure 金鑰保存庫的常見安全性屬性
description: 用來評估 Azure Key Vault 的常見安全性屬性檢查清單
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000185"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure 金鑰保存庫的安全性屬性

這篇文章說明 Azure 金鑰保存庫內建的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 有 | 所有物件都會加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 有 | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 （CMK、 BYOK）| 有 | 客戶控制其金鑰保存庫中的所有索引鍵。 當指定的硬體安全性模組 (HSM) 備份金鑰時，FIPS 層級 2 HSM 保護金鑰、 憑證或密碼。 |
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 有 | 使用 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 有 | 使用虛擬網路 (VNet) 服務端點。 |
| VNet 插入支援| 無 |  |
| 網路隔離，而且防火牆支援| 有 | 使用 VNet 防火牆規則。 |
| 強制通道的支援| 無 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 有 | 使用 Log Analytics。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 有 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 有 | 使用 Key Vault 存取原則。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| 有 | 使用 Log Analytics。 |
| 資料平面記錄與稽核| 有 | 使用 Log Analytics。 |

## <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 有 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 有 | Key Vault 存取原則 |