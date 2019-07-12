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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798679"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database 的安全性屬性

這篇文章說明 Azure SQL Database 內建的一般安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

同時包含 SQL Database[單一資料庫](sql-database-single-index.yml)並[受管理的執行個體](sql-database-managed-instance.md)。 下列項目套用至這兩個供應項目，除了明。

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他的加密功能，例如用戶端 」 或 「 永遠加密</ul>| 是 | 文件中所述，稱為 「 加密使用中，「 [Always Encrypted](sql-database-always-encrypted.md)。 使用伺服器端加密[透明資料加密](transparent-data-encryption-azure-sql.md)。|
| 傳輸中加密：<ul><li>Azure ExpressRoute 加密</li><li>虛擬網路中的加密</li><li>虛擬網路之間的加密</ul>| 是 | 使用 HTTPS。 |
| 加密金鑰的處理，例如 CMK 簡稱為 BYOK| 是 | 提供服務管理和客戶管理金鑰的處理。 後者透過提供[Azure Key Vault](../key-vault/index.yml)。 |
| Azure 資料服務所提供的資料行層級加密| 是 | 透過[Always Encrypted](sql-database-always-encrypted.md)。 |
| 加密的 API 呼叫| 是 | 使用 HTTPS/SSL。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 適用於[單一資料庫](sql-database-single-index.yml)只。 |
| Azure 虛擬網路資料隱碼支援| 是 | 適用於[受管理的執行個體](sql-database-managed-instance.md)只。 |
| 網路隔離，而且防火牆支援| 是 | 資料庫層級和伺服器層級防火牆。 網路隔離是用於[受管理的執行個體](sql-database-managed-instance.md)只。 |
| 強制通道的支援| 是 | [受控執行個體](sql-database-managed-instance.md)經由[ExpressRoute](../expressroute/index.yml) VPN。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援，例如 Log Analytics 或 Application Insights| 是 | SecureSphere、 Imperva 的 SIEM 解決方案也可透過[Azure 事件中樞](../event-hubs/index.yml)透過整合[SQL 稽核](sql-database-auditing.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory (Azure AD) |
| Authorization| 是 | None |

## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制平面與管理平面的記錄與稽核| 是 | [是]，僅適用於某些事件 |
| 資料平面記錄與稽核 | 是 | 透過[SQL 稽核](sql-database-auditing.md) |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理的支援，例如版本控制的設定| 否  | None |

## <a name="additional-security-attributes-for-sql-database"></a>SQL Database 的額外的安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 預防： 弱點評量 | 是 | 請參閱[SQL 弱點評量服務，可協助您識別的資料庫弱點](sql-vulnerability-assessment.md)。 |
| 預防： 資料探索與分類  | 是 | 請參閱[Azure SQL Database 和 SQL 資料倉儲的資料探索與分類](sql-database-data-discovery-and-classification.md)。 |
| 偵測： 威脅偵測 | 是 | 請參閱[進階威脅防護，Azure SQL Database 的](sql-database-threat-detection-overview.md)。 |
