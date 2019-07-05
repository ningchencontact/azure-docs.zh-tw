---
title: 已知與連接至來源資料庫的 Azure 資料庫移轉服務相關聯的問題/錯誤進行疑難排解的相關文件 |Microsoft Docs
description: 深入了解如何疑難排解已知的問題/錯誤與連接至來源資料庫的 Azure 資料庫移轉服務相關聯。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462846"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>連接到來源資料庫時，針對 DMS 錯誤進行疑難排解

下列文章提供有關如何解決連接到您的來源資料庫的 Azure 資料庫移轉服務 (DMS) 時，可能會遇到的潛在問題的詳細資料。 與特定類型的來源資料庫的每一節列出的錯誤可能會遇到與詳細資料以及如何針對連線進行疑難排解的相關資訊的連結。

## <a name="sql-server"></a>SQL Server

與連接至來源 SQL Server 資料庫相關聯的潛在問題以及如何解決這些會提供下表中。

| Error         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| SQL 連線失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 請確認執行個體名稱正確，且該 SQL Server 設定為允許遠端連接。<br> | 如果服務找不到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱文章[連接到來源 SQL Server 時使用動態連接埠或具名執行個體的錯誤](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)。 |
| **錯誤 53** -SQL 連線失敗。 (此外，對於錯誤代碼 1、 2、 5、 53、 233，258、 1225，11001)<br><br> | 如果服務無法連線到來源伺服器，就會發生此錯誤。 若要解決這個問題，請參閱下列資源，並再試一次。 <br><br>  [若要疑難排解連線問題的互動式使用者指南](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [針對 SQL Server 移轉到 Azure SQL Database 的必要條件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [針對 SQL Server 移轉至 Azure SQL Database 受控執行個體的必要條件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **錯誤 18456** -登入失敗。<br> | 如果服務無法連線到使用提供的 T-SQL 認證之來源資料庫，就會發生此錯誤。 若要解決此問題，請確認輸入的認證。 您也可以參考[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或疑難排解文件中注意下面所列資料表，並再試一次。 |
| 格式不正確的 AccountName 值 '{0}' 所提供。 預期的格式，針對 AccountName 是 DomainName\UserName<br> | 如果使用者選取 Windows 驗證，但提供的使用者名稱格式無效，就會發生此錯誤。 若要解決此問題，請提供適用於 Windows 驗證或選取正確的格式的使用者名稱**SQL 驗證**。 |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

與連線到來源 AWS RDS MySQL 資料庫相關聯的潛在問題以及如何解決這些會提供下表中。

| Error         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| **錯誤 [2003]** [HY000]-連線失敗。 錯誤 [HY000] [MySQL] [ODBC x.x(w) 驅動程式] 無法連接至 '{server}' (10060) 上的 MySQL 伺服器 | 如果 MySQL ODBC 驅動程式無法連線到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。<br> |
| **錯誤 [2005]** [HY000]-連線失敗。 錯誤 [HY000] [MySQL] [ODBC x.x(w) 驅動程式] 無法辨識的 MySQL 伺服器主機 '{server}' | 如果服務在 RDS 上找不到來源主機，就會發生此錯誤 此問題可能是可能是因為所列的來源不存在或與 RDS 基礎結構發生問題。 若要解決此問題，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。<br> |
| **錯誤 [1045]** [HY000]-連線失敗。 錯誤 [HY000] [MySQL] [ODBC x.x(w) 驅動程式] 存取拒絕的使用者 '{user}'@'{server}' (使用密碼：[是]) | 如果 MySQL ODBC 驅動程式無法連線到來源伺服器，因為認證無效，就會發生此錯誤。 請確認您輸入的認證。 如果問題持續發生，請確認該來源電腦具有正確的認證。 您可能需要重設主控台中的密碼。 如果您仍然遇到問題，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。<br> |
| **錯誤 [9002]** [HY000]-連線失敗。 錯誤 [HY000] [MySQL] [ODBC x.x(w) 驅動程式] 的連接字串可能不正確。 請瀏覽入口網站的參考。| 如果連線失敗的原因是連接字串的問題，就會發生此錯誤。 確認提供的連接字串有效。 若要解決此問題，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。<br> |
| **二進位記錄時發生錯誤。變數 binlog_format 具有值 '{value}'。請將它變更為 'row'。** | 如果二進位記錄; 中沒有錯誤，就會發生此錯誤變數 binlog_format 有錯誤的值。 若要解決此問題，將參數群組中的 binlog_format 變更為 'ROW'，，然後重新啟動執行個體。 如需詳細資訊，請參閱[二進位記錄選項和變數](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)或是[AWS RDS MySQL 資料庫記錄檔的文件](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)。<br> |

> [!NOTE]
> 如需有關針對連線到來源 AWS RDS MySQL 資料庫相關的問題進行疑難排解的詳細資訊，請參閱下列資源：
> * [針對 Amazon RDS 連線問題進行疑難排解](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解決連接到我的 Amazon RDS 資料庫執行個體的問題？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

與連接至來源 AWS RDS PostgreSQL 資料庫相關聯的潛在問題以及如何解決這些會提供下表中。

| Error         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| **錯誤 [101]** [08001]-連線失敗。 錯誤 [08001] 逾時過期。 | 如果 Postgres 驅動程式無法連線到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。 |
| **錯誤：參數 wal_level 具有值 '{value}'。請將它變更為 'logical' 允許複寫。** | 如果參數 wal_level 有錯誤的值，就會發生此錯誤。 若要解決這個問題，將參數群組中的 rds.logical_replication 變更為 1，然後重新啟動執行個體。 如需詳細資訊，請參閱[移轉到 Azure PostgreSQL 使用 DMS 的必要條件](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)或是[Amazon RDS 上的 PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)。 |

> [!NOTE]
> 如需有關針對連線到來源 AWS RDS PostgreSQL 資料庫相關的問題進行疑難排解的詳細資訊，請參閱下列資源：
> * [針對 Amazon RDS 連線問題進行疑難排解](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解決連接到我的 Amazon RDS 資料庫執行個體的問題？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

與連線到來源 AWS RDS 的 SQL Server 資料庫相關聯的潛在問題以及如何解決這些會提供下表中。

| Error         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| **錯誤 53** -SQL 連線失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 伺服器找不到或不是可存取。 請確認執行個體名稱正確，且該 SQL Server 設定為允許遠端連接。 (提供者：具名的管道提供者，錯誤：40-無法開啟 SQL Server 的連線 | 如果服務無法連線到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。 |
| **錯誤 18456** -登入失敗。 使用者 '{user}' 的登入失敗 | 如果服務時，無法連線到來源資料庫上，使用提供的 T-SQL 的認證，就會發生此錯誤。 若要解決此問題，請確認輸入的認證。 您也可以參考[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或疑難排解的文件中注意下面所列資料表，並再試一次。 |
| **錯誤 87** -連接字串無效。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 請確認執行個體名稱正確，且該 SQL Server 設定為允許遠端連接。 (提供者：SQL 網路介面，錯誤：25-連接字串無效) | 如果服務時，無法連線到來源伺服器上，因為無效的連接字串，就會發生此錯誤。 若要解決此問題，請確認提供的連接字串。 如果問題持續發生，請參閱此表格中，下方的附註中所列的疑難排解文件，並再試一次。 |
| **錯誤-未受信任的伺服器憑證。** 與這個伺服器已成功建立連線，但在登入程序期間發生錯誤。 (提供者：SSL 提供者，錯誤：0-憑證鏈結是由核發不受信任的授權單位。） | 如果使用的憑證不受信任，就會發生此錯誤。 若要解決這個問題，您需要尋找可以受到信任，然後再將它啟用在伺服器上的憑證。 或者，您可以選取連線時的 [信任的憑證] 選項。 只有當您已熟悉使用的憑證，而且您信任它，請採取此動作。 <br> 使用自我簽署的憑證進行加密的 SSL 連線不會提供更強的安全性--它們容易受到攔截攻擊。 不依賴或連線到網際網路的伺服器上使用自我簽署的憑證，在生產環境中的 SSL。 <br> 如需詳細資訊，請參閱[使用 SSL 與 Microsoft SQL Server 資料庫執行個體](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)或[教學課程：將 RDS SQL Server 移轉至 Azure 中使用 DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)。 |
| **錯誤 300** -使用者沒有必要權限。 物件 '{server}' 資料庫 '{database}' 沒有 VIEW SERVER STATE 權限 | 如果使用者沒有權限來執行移轉，就會發生此錯誤。 若要解決這個問題，請參閱[GRANT 伺服器權限的 Transact SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017)或[教學課程：將 RDS SQL Server 移轉至 Azure 中使用 DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)如需詳細資訊。 |

> [!NOTE]
> 如需有關針對連線到來源 AWS RDS SQL Server 相關的問題進行疑難排解的詳細資訊，請參閱下列資源：
>
> * [解決 SQL Server 的連線錯誤](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [如何解決連接到我的 Amazon RDS 資料庫執行個體的問題？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>已知問題

* [使用線上移轉到 Azure SQL Database 的已知的問題/移轉限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [使用線上移轉至 Azure Database for MySQL 的已知的問題/移轉限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [使用線上移轉至 Azure Database for PostgreSQL 的已知的問題/移轉限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>後續步驟

* 檢視發行項[Azure 資料庫移轉服務 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 檢視發行項[如何使用 Azure 入口網站中的 Azure 資料庫設定伺服器參數適用於 MySQL](https://docs.microsoft.com/azure/mysql/howto-server-parameters)。
* 檢視發行項[使用 Azure 資料庫移轉服務的必要條件概觀](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 請參閱[使用 Azure 資料庫移轉服務相關常見問題集](https://docs.microsoft.com/azure/dms/faq)。
