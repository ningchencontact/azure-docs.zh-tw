---
title: 適用於 Azure 備份的常見安全性屬性
description: 用來評估 Azure 備份的常見安全性屬性檢查清單
services: backup
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 86b8ab96a94a6ffc44c304d8a0a689301560a989
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002809"
---
# <a name="security-attributes-for-azure-backup"></a>Azure 備份的安全性屬性

這篇文章說明 Azure 備份內建的安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 有 | 對儲存體帳戶使用儲存體服務加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 無 | 使用 HTTPS。 |
| 加密金鑰處理 （CMK、 BYOK）| 無 |  |
| 資料行層級加密 (Azure Data Services)| 無 |  |
| API 呼叫加密| 有 |  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 無 |  |
| VNet 插入支援| 無 |  |
| 網路隔離，而且防火牆支援| 有 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |
| 強制通道的支援| 無 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 有 | 透過診斷記錄支援 Log Analytics。 請參閱[監視 Azure 備份保護工作負載使用 Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)如需詳細資訊。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 有 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 有 | 會使用建立客戶和內建的 RBAC 角色。 請參閱[使用角色型存取控制來管理 Azure 備份復原點](/azure/backup/backup-rbac-rs-vault)如需詳細資訊。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 有 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |
| 資料平面記錄與稽核| 無 | 無法直接存取 Azure 備份資料平面。  |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 有|  |