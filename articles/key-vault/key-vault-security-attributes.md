---
title: Azure Key Vault 的安全性屬性
description: 評估 Azure Key Vault 的安全性屬性檢查清單
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444011"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure Key Vault 的安全性屬性

本文記載 Azure Key Vault 內建的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)| 是 | 所有物件都會加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶會控制其 Key Vault 中的所有金鑰。 當指定硬體安全模組 (HSM) 所支援的金鑰時, FIPS Level 2 HSM 會保護金鑰、憑證或密碼。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | 使用 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 使用虛擬網路 (VNet) 服務端點。 |
| VNet 插入支援| 否 |  |
| 網路隔離和防火牆支援| 是 | 使用 VNet 防火牆規則。 |
| 強制通道支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 使用 Log Analytics。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| Authorization| 是 | 使用 Key Vault 存取原則。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面記錄和審核| 是 | 使用 Log Analytics。 |

## <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |