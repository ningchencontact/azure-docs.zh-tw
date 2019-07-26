---
title: Azure SQL Database 的安全性屬性
description: 評估 Azure SQL Database 的安全性屬性檢查清單
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444382"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database 的安全性屬性

本文記載 Azure SQL Database 內建的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database 包括[單一資料庫](sql-database-single-index.yml)和[受控實例](sql-database-managed-instance.md)。 下列專案適用于這兩個供應專案, 除非另有注明。

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能, 例如用戶端或 Always Encrypted</ul>| 是 | 如[Always Encrypted](sql-database-always-encrypted.md)一文所述, 稱為「加密使用中」。 伺服器端加密會使用[透明資料加密](transparent-data-encryption-azure-sql.md)。|
| 傳輸中加密:<ul><li>Azure ExpressRoute 加密</li><li>虛擬網路中的加密</li><li>虛擬網路之間的加密</ul>| 是 | 使用 HTTPS。 |
| 加密金鑰處理, 例如 CMK 或 BYOK| 是 | 提供服務管理和客戶管理的金鑰處理。 後者會透過[Azure Key Vault](../key-vault/index.yml)來提供。 |
| Azure data services 提供的資料行層級加密| 是 | 透過[Always Encrypted](sql-database-always-encrypted.md)。 |
| 加密的 API 呼叫| 是 | 使用 HTTPS/SSL。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 僅適用于[單一資料庫](sql-database-single-index.yml)。 |
| Azure 虛擬網路插入支援| 是 | 僅適用于[受控實例](sql-database-managed-instance.md)。 |
| 網路隔離和防火牆支援| 是 | 資料庫層級和伺服器層級的防火牆。 網路隔離僅適用于[受控實例](sql-database-managed-instance.md)。 |
| 強制通道支援| 是 | 經由[ExpressRoute](../expressroute/index.yml) VPN 的[受控實例](sql-database-managed-instance.md)。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援, 例如 Log Analytics 或 Application Insights| 是 | SecureSphere (來自 Imperva 的 SIEM 解決方案) 也透過[SQL 審核](sql-database-auditing.md) [Azure 事件中樞](../event-hubs/index.yml)整合來支援。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory (Azure AD) |
| Authorization| 是 | None |

## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制平面和管理平面記錄和審核| 是 | 是, 僅適用于某些事件 |
| 資料平面記錄和審核 | 是 | 透過[SQL audit](sql-database-auditing.md) |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援, 例如設定的版本設定| 否  | None |

## <a name="additional-security-attributes-for-sql-database"></a>SQL Database 的其他安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 預防性: 弱點評估 | 是 | 請參閱[SQL 弱點評定服務協助您識別資料庫弱點](sql-vulnerability-assessment.md)。 |
| 預防性: 資料探索與分類  | 是 | 請參閱[Azure SQL Database 和 SQL 資料倉儲資料探索 & 分類](sql-database-data-discovery-and-classification.md)。 |
| 偵測: 威脅偵測 | 是 | 請參閱[Azure SQL Database 的 Advanced 威脅防護](sql-database-threat-detection-overview.md)。 |
