---
title: 適用於 Azure 備份的常見安全性屬性
description: 用來評估 Azure 備份的常見安全性屬性檢查清單
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7bc4fd42f64bba09fd20fedf5e06dc30c3b2cc49
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64680806"
---
# <a name="common-security-attributes-for-azure-backup"></a>適用於 Azure 備份的常見安全性屬性

Azure 服務的各個層面都整合了安全性。 這篇文章說明內建於 Azure 備份的常見安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 對儲存體帳戶使用儲存體服務加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 |  |
| 資料行層級加密 (Azure Data Services)| 否 |  |
| API 呼叫加密| 是 |  |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| VNet 插入支援| 否 |  |
| 網路隔離，而且防火牆支援| 是 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |
| 強制通道的支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 透過診斷記錄支援 Log Analytics。 請參閱[監視 Azure 備份保護工作負載使用 Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)如需詳細資訊。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 會使用建立客戶和內建的 RBAC 角色。 請參閱[使用角色型存取控制來管理 Azure 備份復原點](/azure/backup/backup-rbac-rs-vault)如需詳細資訊。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |
| 資料平面記錄與稽核| 否 | 無法直接存取 Azure 備份資料平面。  |

## <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是|  |