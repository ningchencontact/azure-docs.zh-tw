---
title: Azure SQL Database 的安全性屬性
description: 評估 Azure SQL Database 的安全性屬性的檢查清單
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001044"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database 的安全性屬性

這篇文章說明 Azure SQL Database 內建的一般安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

同時包含 azure SQL Database[單一資料庫](sql-database-single-index.yml)並[受管理的執行個體](sql-database-managed-instance.md)。 底下的項目套用至這兩個供應項目，除了有註明。

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 有 | 稱為 「 加密--使用中 」，如本文所述[Always Encrypted](sql-database-always-encrypted.md)。 使用服務端加密[透明資料加密](transparent-data-encryption-azure-sql.md)(TDE)。|
| 傳輸中加密：<ul><li>ExpressRoute 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 有 | 使用 HTTPS。 |
| 加密金鑰處理 （CMK、 BYOK）| 有 | 提供服務管理和客戶管理金鑰的處理 (透過後者[Azure Key Vault](../key-vault/index.yml)。 |
| 資料行層級加密 (Azure Data Services)| 有 | 透過[Always Encrypted](sql-database-always-encrypted.md)。 |
| API 呼叫加密| 有 | 使用 HTTPS/SSL。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 有 | 適用於[單一資料庫](sql-database-single-index.yml)只。 |
| VNet 插入支援| 有 | 適用於[受管理的執行個體](sql-database-managed-instance.md)只。 |
| 網路隔離，而且防火牆支援| 有 | 防火牆在這兩個資料庫和伺服器層級;網路隔離[受管理的執行個體](sql-database-managed-instance.md)只 |
| 強制通道的支援| 有 | [受控執行個體](sql-database-managed-instance.md)經由[Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 有 | 第三方 SIEM 解決方案，從 Imperva (SecureSphere)，因此也支援透過[Azure 事件中樞](../event-hubs/index.yml)透過整合[SQL 稽核](sql-database-auditing.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 有 | Azure Active Directory。 |
| 授權| 有 |  |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 有 | 是僅適用於某些事件。 |
| 資料平面記錄與稽核 | 有 | 透過[SQL 稽核](sql-database-auditing.md)。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 無  | | 

## <a name="additional-security-attributes-for-sql-database"></a>SQL Database 的額外的安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 預防： 弱點評量 | 有 | 請參閱[SQL 弱點評量服務，可協助您識別的資料庫弱點](sql-vulnerability-assessment.md)。 |
| 預防： 資料探索與分類  | 有 | 請參閱[Azure SQL Database 和 SQL 資料倉儲的資料探索與分類](sql-database-data-discovery-and-classification.md)。 |
| 偵測： 威脅偵測 | 有 | 請參閱[進階威脅防護，Azure SQL Database 的](sql-database-threat-detection-overview.md)。 |
