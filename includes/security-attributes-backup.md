---
author: msmbaldwin
ms.service: backup
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 85d1e4d4909422b82ed38e688e3a62ca53c3430a
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007199"
---
## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 對儲存體帳戶使用儲存體服務加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| 否 |  |
| API 呼叫加密| 是 |  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| vNET 插入支援| 否 |  |
| 網路隔離/防火牆支援| 是 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |
| 支援強制通道 | 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 透過診斷記錄支援 Log Analytics。 請參閱「使用 Log Analytics 監視 Azure 備份保護的工作負載」(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) 以取得詳細資訊。 |

## <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| 是 | 會使用建立客戶和內建的 RBAC 角色。 請參閱 < 使用角色型存取控制來管理 Azure 備份的復原點 （/azure/backup/備份-rbac-rs-保存庫） 如需詳細資訊。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |
| 資料平面記錄和稽核| 否 | 無法直接存取 Azure 備份資料平面。  |

## <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是|  |