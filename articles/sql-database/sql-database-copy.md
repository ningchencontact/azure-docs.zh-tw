---
title: 複製 Azure SQL Database | Microsoft Docs
description: 在同個伺服器或不同伺服器上，建立現有 Azure SQL Database 的交易一致性複本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: sahsan
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: 363803e9276a8356b52438f251391378c54f1655
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678933"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>複製 Azure SQL 資料庫的交易一致性複本

Azure SQL Database 提供數種方式，可讓您在同個伺服器或不同的伺服器上，建立現有 Azure SQL Database 的交易一致性複本。 若要複製 SQL Database，您可使用 Azure 入口網站、PowerShell 或 T-SQL。 

## <a name="overview"></a>概觀

資料庫複本是發生複製要求時的來源資料庫快照集。 您可以選取相同的伺服器或不同的伺服器。 也您可以選擇保留其服務層和計算大小，或使用相同的服務層 （版本） 內的不同計算大小。 複製完成之後，複本會變成功能完整的獨立資料庫。 此時，您可以將它升級或降級成任何版本。 可以個別管理登入、使用者和權限。  

> [!NOTE]
> 當您建立資料庫複本時，會使用[自動資料庫備份](sql-database-automated-backups.md)。

## <a name="logins-in-the-database-copy"></a>資料庫複本中的登入

當您將資料庫複製到相同的 SQL Database 伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。  

當您將資料庫複製到不同的 SQL Database 伺服器時，新伺服器上的安全性主體就會變成新資料庫上的資料庫擁有者。 如果您使用[自主資料庫使用者](sql-database-manage-logins.md)來進行資料存取，請確保主要和次要資料庫一律具有相同的使用者認證，以便在複製完成時，您可以使用相同的認證立即存取它。 

如果您使用 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，則可以完全不需管理副本中的認證。 不過，當您將資料庫複製到新的伺服器時，以登入為基礎的存取可能無法運作，因為登入不存在於新的伺服器上。 若要了解如何在將資料庫複製到不同的 SQL Database 伺服器時管理登入，請參閱[如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)。 

在複製成功之後，重新對應其他使用者之前，只有起始複製的登入 (也就是資料庫擁有者) 可以登入新的資料庫。 若要在複製作業完成之後解析登入，請參閱 [解析登入](#resolve-logins)。

## <a name="copy-a-database-by-using-the-azure-portal"></a>使用 Azure 入口網站來複製資料庫

若要使用 Azure 入口網站來複製資料庫，請開啟資料庫頁面，然後按一下 [複製]。 

   ![資料庫複本](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>使用 PowerShell 來複製資料庫

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要使用 PowerShell 复制数据库，请使用 [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet。 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

如需完整範例指令碼，請參閱[將資料庫複製到新伺服器](scripts/sql-database-copy-database-to-new-server-powershell.md)。

## <a name="copy-a-database-by-using-transact-sql"></a>使用 Transact-SQL 來複製資料庫

使用伺服器層級主體登入或建立您要複製之資料庫的登入來登入 master 資料庫。 若要成功複製資料庫，不是伺服器層級主體的登入必須是 dbmanager 角色的成員。 有关登录名和链接到服务器的详细信息，请参阅[管理登录名](sql-database-manage-logins.md)。

使用 [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 陳述式，開始複製來源資料庫。 執行此陳述式會起始資料庫複製程序。 因為複製資料庫是非同步程序，所以 CREATE DATABASE 陳述式會在資料庫完成複製之前傳回。

### <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL Database 複製到相同伺服器

使用伺服器層級主體登入或建立您要複製之資料庫的登入來登入 master 資料庫。 若要成功複製資料庫，不是伺服器層級主體的登入必須是 dbmanager 角色的成員。

此命令會將 Database1 複製到相同伺服器上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>將 SQL Database 複製到不同伺服器

登入目的地伺服器的 master 資料庫，也就是即將建立新資料庫的 SQL Database 伺服器。 使用具有與來源 SQL Database 伺服器上來源資料庫的資料庫擁有者相同之名稱和密碼的登入。 目的地伺服器上的登入必須也是 dbmanager 角色的成員或是伺服器層級主體登入。

此命令會將 server1 上的 Database1 複製到 server2 上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> 這兩部伺服器的防火牆必須允許輸入的連線，從用戶端發出 T-SQL 複製命令的 IP 設定。

### <a name="copy-a-sql-database-to-a-different-subscription"></a>將 SQL database 複製到不同的訂用帳戶

您可以使用上一節中的步驟 descrbed，將資料庫複製到不同的訂用帳戶中的 SQL Database 伺服器。 請確定您使用來源資料庫的資料庫擁有者具有相同的名稱和密碼登入，而且它是 dbmanager 角色的成員或伺服器層級主體登入。 

> [!NOTE]
> [Azure 入口網站](https://portal.azure.com)不支援複製到不同的訂用帳戶，因為入口網站呼叫 ARM API，並使用訂用帳戶憑證存取這兩個異地複寫中涉及的伺服器。  

### <a name="monitor-the-progress-of-the-copying-operation"></a>監視複製作業的進度

藉由查詢 sys.databases 和 sys.dm_database_copies 檢視來監視複製程序。 當複製正在進行時，新資料庫的 sys.databases 檢視的 **state_desc** 資料行會設定為 **COPYING**。

* 如果複製失敗，新資料庫的 sys.databases 檢視的 **state_desc** 資料行會設定為 **SUSPECT**。 在新的資料庫上執行 DROP 陳述式，稍後再試一次。
* 如果複製成功，新資料庫的 sys.databases 檢視的 **state_desc** 資料行會設定為 **ONLINE**。 複製已完成且新資料庫是一般資料庫，能夠與來源資料庫分開進行變更。

> [!NOTE]
> 如果您決定在進行複製時予以取消，請在新資料庫上執行 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 陳述式。 或者，在來源資料庫上執行 DROP DATABASE 陳述式也會取消複製程序。

## <a name="resolve-logins"></a>解析登入

在新資料庫於目的地伺服器上線之後，使用 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 陳述式將使用者從新的資料庫重新對應至目的地伺服器上的登入。 若要解析被遺棄的使用者，請參閱 [被遺棄使用者疑難排解](https://msdn.microsoft.com/library/ms175475.aspx)。 另請參閱 [如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)。

新資料庫中的所有使用者都保有其在來源資料庫中原有的權限。 起始資料庫複製的使用者會變成新資料庫的資料庫擁有者，並且被指派新的安全性識別碼 (SID)。 在複製成功之後，重新對應其他使用者之前，只有起始複製的登入 (也就是資料庫擁有者) 可以登入新的資料庫。

若要了解將資料庫複製到不同的邏輯 SQL Database 伺服器時如何管理使用者與登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。

## <a name="next-steps"></a>後續步驟

* 如需登入相關資訊，請參閱[管理登入](sql-database-manage-logins.md)以及[如何管理災害復原後的 Azure SQL Database 安全性](sql-database-geo-replication-security-config.md)。
* 若要匯出資料庫，請參閱[將資料庫匯出至 BACPAC](sql-database-export.md)。
