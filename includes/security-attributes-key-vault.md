---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: aba09012bf2e9d2741f598280add8b599a6f6d1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807075"
---
## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| yes | 所有物件都會加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| yes | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 (CMK、BYOK 等)| yes | 客戶在其 Key Vault 中控制所有金鑰。 如果指定了硬體安全性模組 (HSM) 支援的金鑰，則 FIPS Level 2 HSM 會保護金鑰、憑證或秘密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| yes | 使用 HTTPS。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| yes | 使用虛擬網路 (VNet) 服務端點。 |
| vNET 插入支援| 否 |  |
| 網路隔離/防火牆支援| yes | 使用 Vnet 防火牆規則。 |
| 支援強制通道 | 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log Analytics、App Insights 等)| yes | 使用 Log Analytics。 |

## <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| yes | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| yes | 使用 Key Vault 存取原則。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| yes | 使用 Log Analytics。 |
| 資料平面記錄和稽核| yes | 使用 Log Analytics。 |

## <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | yes | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | yes | Key Vault 存取原則 |
