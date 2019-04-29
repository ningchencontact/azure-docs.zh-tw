---
title: 授與存取 Azure SQL Database 和 SQL 資料倉儲的權限 | Microsoft Docs
description: 了解授與存取 Microsoft Azure SQL Database 和 SQL 資料倉儲的權限。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 8cb044397cf439e97f3630b5c1c3f53fbf3f356d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61468392"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Azure SQL Database 和 SQL 資料倉儲存取控制

為了提供安全性，Azure [SQL Database](sql-database-technical-overview.md) 和 [SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)會透過下列方式來控制存取：依 IP 位址限制連線的防火牆規則、要求使用者證明其身分識別的驗證機制，以及將使用者限制在特定動作和資料的授權機制。 

> [!IMPORTANT]
> 如需 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。 如需教學課程，請參閱[保護 Azure SQL Database](sql-database-security-tutorial.md)。 如需所有 SQL 資料倉儲安全性功能的概觀，請參閱 [SQL 資料倉儲安全性概觀](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>防火牆與防火牆規則

Microsoft Azure SQL Database 為 Azure 和其他網際網路式應用程式提供關聯式資料庫服務。 為了協助保護您的資料，防火牆會防止對您的資料庫伺服器的所有存取，直到您指定哪些電腦擁有權限。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。 如需詳細資訊，請參閱 [Azure SQL Database 防火牆規則概觀](sql-database-firewall-configure.md)

Azure SQL Database 服務唯有透過 TCP 通訊埠 1433 才能使用。 若要从计算机访问 SQL 数据库，请确保客户端计算机防火墙允许 TCP 端口 1433 上的传出 TCP 通信。 如果其他应用程序不需要，则阻止 TCP 端口 1433 上的入站连接。 

連接程序當中，從 Azure 虛擬機器的連接會被重新導向到不同的 IP 位址和連接埠，針對每個背景工作角色都是唯一。 该端口号在 11000 到 11999 的范围内。 如需 TCP 連接埠的詳細資訊，請參閱[適用於 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。

## <a name="authentication"></a>Authentication

SQL Database 支援兩種驗證類型：

- **SQL 驗證**：

  此驗證方法會使用使用者名稱和密碼。 當您為資料庫建立 SQL Database 伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。 
- **Azure Active Directory 驗證**：

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 驗證 (整合式安全性)。 如果想要使用 Azure Active Directory 身份验证，则必须创建名为“Azure AD 管理员”的另一个服务器管理员，用于管理 Azure AD 用户和组。 此管理员还能执行普通服务器管理员可以执行的所有操作。 如需如何建立 Azure AD 管理員以啟用 Azure Active Directory 驗證的逐步解說，請參閱 [使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md) 。

資料庫引擎會關閉保持閒置超過 30 分鐘的連接。 必須重新登入才能使用連接。 持續作用中的 SQL Database 連接，需要至少每 10 個小時重新授權一次 (由資料庫引擎執行)。 資料庫引擎嘗試重新授權時，會使用原本送出的密碼，但不需要使用者輸入。 基於效能考量，當在 SQL Database 中重設密碼時，並不會重新驗證連線，即使因連線共用而重設連線時也一樣。 這點與內部部署 SQL Server 的行為不同。 如果連接經過初始授權後，密碼已經變更，則必須終止連接並使用新密碼建立新的連接。 具有 `KILL DATABASE CONNECTION` 權限的使用者可以使用 [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql) 命令，明確地終止 SQL Database 的連線。

使用者帳戶可以建立在主要資料庫中，並獲得伺服器上所有資料庫的權限，或者，可將帳戶建立在資料庫自身當中 (稱為自主使用者)。 如需建立和管理登入的資訊，請參閱[管理登入](sql-database-manage-logins.md)。 使用自主資料庫來提升可攜性和延展性。 如需自主使用者的詳細資訊，請參閱[自主的資料庫使用者 - 使資料庫可攜](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)、[CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 及[自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。

最佳作法是，您的應用程式應該使用專用帳戶進行驗證，因為萬一應用程式的程式碼容易受到 SQL 插入式攻擊，您就可以限制授與應用程式的權限，並降低惡意活動的風險。 建议的方法是创建 [包含数据库用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)，使应用程序能够直接向数据库进行身份验证。 

## <a name="authorization"></a>授權

授權是指使用者可以在 Azure SQL Database 內執行的動作，這是由使用者帳戶的資料庫[角色成員資格](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)和[物件層級權限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)所控制。 最好的作法是，您應該授與使用者所需的最低權限。 您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。 有关详细信息，请参阅[管理登录名](sql-database-manage-logins.md)。

通常，只有系統管理員需要存取 `master` 資料庫。 對每個使用者資料庫的例行存取，應該透過每個資料庫中建立的非系統管理員的自主資料庫使用者來存取。 當您使用自主資料庫使用者時，您不需要在 `master` 資料庫中建立登入。 如需詳細資訊，請參閱 [自主資料庫使用者 - 讓資料庫具有可攜性](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)。

您應該熟悉下列功能，這些功能可用來限制或提高權限︰

- [模拟](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)和[模块签名](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)可用于安全地暂时提升权限。
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 可用於使用者可存取資料列的限制。
- [資料遮罩](sql-database-dynamic-data-masking-get-started.md) 可用來限制公開機密資料。
- [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 可用來限制對資料庫可採取的動作。

## <a name="next-steps"></a>後續步驟

- 有关 SQL 数据库安全功能的概述，请参阅 [SQL 安全概述](sql-database-security-overview.md)。
- 若要深入了解防火牆規則，請參閱[防火牆規則](sql-database-firewall-configure.md)。
- 若要了解使用者和登入，請參閱[管理登入](sql-database-manage-logins.md)。 
- 關於主動式監視的討論，請參閱[資料庫稽核](sql-database-auditing.md)和 [SQL Database 威脅偵測](sql-database-threat-detection.md)。
- 如需教學課程，請參閱[保護 Azure SQL Database](sql-database-security-tutorial.md)。
