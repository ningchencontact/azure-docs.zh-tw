---
title: Azure SQL Database 安全性概觀 | Microsoft Docs
description: 了解 Azure SQL Database 和 SQL Server 的 安全性，包含雲端和 SQL Server 內部部署之間的差異。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 5bb3dc0245371248b005d642debb5b60026b9f4c
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635470"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database 安全性功能的概觀

本篇文章逐步說明使用 Azure SQL Database 保護應用程式資料層的基本概念。 本文尤其著重於協助您開始利用資源來保護資料、控制存取，以及進行主動式監視。

如需各種 SQL 上可用的完整安全性功能概觀，請參閱 [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](https://msdn.microsoft.com/library/bb510589)。 同時您也可於 [安全性和 Azure SQL Database 技術白皮書](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF) 中取得其他資訊。

## <a name="protect-data"></a>保護資料

### <a name="encryption"></a>加密

SQL Database 會使用[傳輸層安全性](https://support.microsoft.com/kb/3135244)為移動中的資料提供加密、使用[透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)為待用資料提供加密，以及使用[一律加密](https://msdn.microsoft.com/library/mt163865.aspx)為使用中的資料提供加密，來保護您的資料安全。

> [!IMPORTANT]
> Azure SQL Database 一律會針對所有連線強制執行加密 (SSL/TLS)，以確保所有資料在資料庫與用戶端之間「傳輸中」都會加密。 不論連接字串中 **Encrypt** 或 **TrustServerCertificate** 的設定為何，都將發生此情況。
>
> 在您的應用程式連接字串中，請確定您會指定加密的連線，而且「不會」信任伺服器憑證 (對於 ADO.NET 驅動程式，這是 **Encrypt=True** 和 **TrustServerCertificate=False**)。 這有助於藉由強制您的應用程式驗證伺服器並強制執行加密，來防止該應用程式受到「中間人」攻擊。 如果您從 Azure 入口網站取得連接字串，則它將具有正確的設定。
>
> 如需 TLS 和連線能力的相關資訊，請參閱 [TLS 考量](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

如需其他的資料加密方式，請考慮：

- [儲存格層級加密](https://msdn.microsoft.com/library/ms179331.aspx) ，可利用不同的加密金鑰來加密特定的資料行或甚至是資料儲存格。
-  如果您需要硬體安全性模型或攜帶您自己的金鑰 (BYOK) 技術以便進行透明資料加密，請考慮使用 [Azure SQL 透明資料加密：攜帶您自己的金鑰支援](transparent-data-encryption-byok-azure-sql.md)。

### <a name="data-discovery--classification"></a>資料探索與分類

資料探索與分類 (目前處於預覽階段) 提供內建於 Azure SQL Database 的進階功能，可用於探索、分類、標記和保護您資料庫中的敏感性資料。 探索與分類您最具敏感性的資料 (商業/財務、醫療保健及 PII 等)，可在您組織的資訊保護方面扮演著關鍵角色。 它可以作為以下的基礎結構：

- 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
- 對包含高度敏感性資料的資料庫進行存取控制並強化安全性。
- 協助符合資料隱私標準和法規合規性需求。

如需詳細資訊，請參閱[開始使用 SQL DB 資料探索與分類](sql-database-data-discovery-and-classification.md)。

## <a name="control-access"></a>控制存取

SQL Database 使用防火牆規則、要求使用者證明其身分的驗證機制，以及透過角色型成員資格與權限和透過資料列層級安全性與動態資料遮罩的資料授權來限制資料庫的存取，進而保護您的資料。 如需在 SQL Database 中使用存取控制功能的討論，請參閱[控制存取](sql-database-control-access.md)。

> [!IMPORTANT]
> 在 Azure 內管理資料庫和邏輯伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需有關此文章的詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../role-based-access-control/overview.md)。 控制防火牆規則的存取「不」適用於 **Azure SQL Database 受控執行個體**。 如需所需網路設定的相關資訊，請參閱下列關於[連線到受控執行個體](sql-database-managed-instance-connect-app.md)的文章。

### <a name="firewall-and-firewall-rules"></a>防火牆與防火牆規則

為了協助保護您的資料，防火牆會防止對您的資料庫伺服器的所有存取，直到您使用[防火牆規則](sql-database-firewall-configure.md)指定哪些電腦擁有權限。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。

### <a name="authentication"></a>驗證

SQL Database 驗證是指連線到資料庫時如何證明身分識別。 SQL Database 支援兩種驗證類型：

- **SQL 驗證**

  此驗證方法會使用使用者名稱和密碼。 當您為資料庫建立邏輯伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。

- **Azure Active Directory 驗證**

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://msdn.microsoft.com/library/ms144284.aspx)使用 Active Directory 驗證 (整合式安全性)。 如果您想要使用 Azure Active Directory 驗證，就必須建立另一個名為「Azure AD 管理員」的伺服器管理員，其能夠管理 Azure AD 使用者和群組。 此管理員也可以執行一般伺服器管理員可執行的所有作業。 如需如何建立 Azure AD 管理員以啟用 Azure Active Directory 驗證的逐步解說，請參閱 [使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md) 。

### <a name="authorization"></a>Authorization

授權是指使用者可以在 Azure SQL Database 內執行的動作，這是由使用者帳戶的資料庫角色成員資格和物件層級權限所控制。 最好的作法是，您應該授與使用者所需的最低權限。 您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。

### <a name="row-level-security"></a>資料列層級安全性

資料列層級安全性讓客戶能夠根據執行查詢的使用者特性 (例如，依群組成員資格或執行內容)，來控制資料庫資料表中的資料列存取。 如需詳細資訊，請參閱[資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)。

### <a name="dynamic-data-masking"></a>動態資料遮罩

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。 動態資料遮罩會自動探索 Azure SQL Database 中的可能敏感性資料，並提供可動作的建議來為這些欄位加上遮罩，盡量避免對應用程式層造成影響。 其運作方式為針對指定的資料庫欄位隱匿查詢結果集中的敏感性資料，而不變更資料庫中的資料。 如需詳細資訊，請參閱[開始使用 SQL Database 動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。

## <a name="proactive-monitoring"></a>主動監視

SQL Database 可藉由提供稽核和威脅偵測功能來保護您的資料。

### <a name="auditing"></a>稽核

SQL Database 稽核會將資料庫事件記錄到 Azure 儲存體帳戶中的稽核記錄檔，藉此追蹤資料庫活動並協助您維護法規相符性。 稽核可讓您了解進行中的資料庫活動，以及分析和調查歷史活動，以找出潛在威脅或可疑的濫用和安全性違規。 如需詳細資訊，請參閱 [開始使用 Azure Database 稽核](sql-database-auditing.md)。  

### <a name="threat-detection"></a>威脅偵測

威脅偵測會提供 Azure SQL Database 服務內建的額外安全情報層，此情報層可偵測到不尋常且有危害的資料庫存取或攻擊動作，藉此補充稽核的不足之處。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 您可以從 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)檢視威脅偵測警示，該警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。 每部伺服器的威脅偵測費用為每個月 $15 元。 前 60 天不收取任何費用。 如需詳細資訊，請參閱 [開始使用 SQL Database 威脅偵測](sql-database-threat-detection.md)。

## <a name="compliance"></a>法規遵循

除了上述可協助您的應用程式符合各種安全性需求的特色和功能之外，Azure SQL Database 也定期參與稽核，並且經過認證符合許多法規標準。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，您可以在當中找到 [SQL Database 法規認證](https://www.microsoft.com/trustcenter/compliance/complianceofferings)的最新清單。

## <a name="security-management"></a>安全性管理

SQL Database 使用 [SQL 弱點評量](sql-vulnerability-assessment.md)來提供資料庫掃描和集中式的安全性儀表板，協助您管理資料安全性。

**[SQL 弱點評量](sql-vulnerability-assessment.md)** 是容易設定的 Azure SQL Database 內建工具，可協助您探索、追蹤及修復潛在的資料庫弱點。 此評量會在您的資料庫上執行弱點掃描並產生報表，讓您掌握您的安全性狀態，其中還包括解決安全性問題和增強資料庫安全性的可採取動作。 您可以針對權限組態、功能組態及資料庫組態設定可接受的基準，為您的環境自訂評量報告。 這可協助您：

- 符合需要資料庫掃描報告的合規性需求。
- 符合資料隱私權標準。
- 監視難以追蹤變更的動態資料庫環境。

如需詳細資訊，請參閱 [SQL 弱點評量](sql-vulnerability-assessment.md)。

## <a name="next-steps"></a>後續步驟

- 如需在 SQL Database 中使用存取控制功能的討論，請參閱[控制存取](sql-database-control-access.md)。
- 如需資料庫稽核的相關討論，請參閱 [SQL Database 稽核 (英文)](sql-database-auditing.md)。
- 如需威脅偵測的相關討論，請參閱 [SQL Database 威脅偵測](sql-database-threat-detection.md)。
