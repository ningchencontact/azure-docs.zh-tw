---
title: 在 Azure SQL Database 受控執行個體資料庫中設定複寫 |Microsoft Docs
description: 了解如何在 Azure SQL Database 受控執行個體資料庫中設定異動複寫
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: e4d056aacf8f3969b645747e2303574f3fea3bda
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357111"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL Database 受控執行個體資料庫中設定複寫

異動複寫可讓您將資料從 SQL Server 資料庫或其他執行個體資料庫複寫到 Azure SQL Database 受控執行個體資料庫。 

您也可以使用異動複寫，將 Azure SQL Database 受控執行個體中要執行個體資料庫中所做的變更：

- SQL Server 資料庫。
- Azure SQL Database 中單一資料庫。
- Azure SQL Database 彈性集區中的集區的資料庫。
 
異動複寫上處於公開預覽[Azure SQL Database 受控執行個體](sql-database-managed-instance.md)。 受控執行個體可以裝載發行者、散發者和訂閱者資料庫。 如需可用設定的相關資訊，請參閱[異動複寫設定](sql-database-managed-instance-transactional-replication.md#common-configurations)。

  > [!NOTE]
  > 這篇文章的目的是引導中使用的 Azure 資料庫設定複寫的使用者管理執行個體從端對端，開始建立資源群組。 如果您已經有 managed 執行個體部署，請直接跳到[步驟 4](#4---create-a-publisher-database)來建立您的發行者資料庫，或[步驟 6](#6---configure-distribution)如果您已經有發行者和訂閱者的資料庫，並且已準備好開始設定複寫。  

## <a name="requirements"></a>需求

設定受管理的執行個體做為 「 發行者 」 和/或 「 散發者 」 需要：

- 受控執行個體目前未參與異地複寫關聯性。
- 「 發行者 」 端受控執行個體位於相同的虛擬網路，做為 「 散發者 」 和 「 訂閱者 」，或是[vNet 對等互連](../virtual-network/tutorial-connect-virtual-networks-powershell.md)已建立虛擬網路的所有三個實體之間。 
- 連線會在複寫參與者之間使用 SQL 驗證。
- 複寫工作目錄的 Azure 儲存體帳戶共用。
- 在安全性規則的 NSG，以受管理的執行個體來存取 Azure 檔案共用中開啟連接埠 445 (TCP 輸出)。 


 > [!NOTE]
 > Azure SQL Database 中的單一資料庫與集區資料庫只能是訂閱者。 


## <a name="features"></a>功能

支援：

- 混用 SQL Server 內部部署和 Azure SQL Database 中受控執行個體的異動與快照式複寫。
- 訂閱者可以在內部部署 SQL Server 資料庫，在 Azure SQL Database 或 Azure SQL Database 彈性集區中的集區的資料庫中的 單一資料庫/受控執行個體。
- 單向或雙向複寫。

Azure SQL Database 的受控執行個體中不支援下列功能：

- [可更新訂閱](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- [作用中異地複寫](sql-database-active-geo-replication.md)並[自動容錯移轉群組](sql-database-auto-failover-group.md)不應在異動複寫設定。
 
## <a name="1---create-a-resource-group"></a>1-建立資源群組

使用[Azure 入口網站](https://portal.azure.com)來建立資源群組名稱`SQLMI-Repl`。  

## <a name="2---create-managed-instances"></a>2-建立受控執行個體

使用[Azure 入口網站](https://portal.azure.com)建立兩個[受管理執行個體](sql-database-managed-instance-create-tutorial-portal.md)相同虛擬網路和子網路上。 兩個受管理的執行個體應該命名為：

- `sql-mi-pub`
- `sql-mi-sub`

您也必須[設定 Azure VM 連線](sql-database-managed-instance-configure-vm.md)到您的 Azure SQL Database 受控執行個體。 

## <a name="3---create-azure-storage-account"></a>3-建立 Azure 儲存體帳戶

[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)的工作目錄，然後建立[檔案共用](../storage/files/storage-how-to-create-file-share.md)儲存體帳戶內。 

複製檔案共用路徑，格式為： `\\storage-account-name.file.core.windows.net\file-share-name`

複製儲存體存取金鑰的格式： `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 如需詳細資訊，請參閱 [檢視和複製儲存體存取金鑰](../storage/common/storage-account-manage.md#access-keys)。 

## <a name="4---create-a-publisher-database"></a>4-建立發行者資料庫

連接到您`sql-mi-pub`受控執行個體使用 SQL Server Management Studio，並執行下列的 TRANSACT-SQL (T-SQL) 程式碼，以建立您的發行者資料庫：

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5-建立訂閱者資料庫

連接到您`sql-mi-sub`受控執行個體使用 SQL Server Management Studio，並執行下列 T-SQL 程式碼，建立空白的訂閱者資料庫：

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6-設定散發

連接到您`sql-mi-pub`受控執行個體使用 SQL Server Management Studio，並執行下列 T-SQL 程式碼來設定您的散發資料庫。 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-設定以使用散發者的發行者 

在您的發行者上受控執行個體`sql-mi-pub`，變更執行查詢[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式並執行下列的程式碼，以向發行者註冊新的散發者。 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

此指令碼會在受管理的執行個體，來設定本機 「 發行者 」 新增連結的伺服器，並建立一組工作的 SQL Server agent。 

## <a name="8---create-publication-and-subscriber"></a>8-建立發行集和訂閱者

使用[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式中，執行下列 T-SQL 指令碼，以啟用複寫您的資料庫，並設定 「 發行者 」、 「 散發者 」 與 「 訂閱者 」 之間的複寫。 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-修改代理程式參數

Azure SQL Database 受控執行個體目前的複寫代理程式的連線發生一些後端的問題。 此問題時已特別處理定址，增加的複寫代理程式的登入逾時值的因應措施。 

增加登入逾時的 「 發行者 」 端，執行下列 T-SQL 命令： 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

執行下列 T-SQL 命令來登入逾時重設為預設值，要這樣做應該：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

重新啟動所有的三個代理程式，才能套用這些變更。 

## <a name="10---test-replication"></a>10-測試複寫

一旦已設定複寫，您可以插入新的項目，「 發行者 」 端，監看的變更傳播到 「 訂閱者 」 來進行測試。 

執行下列 T-SQL 程式碼片段，以檢視在訂閱者上的資料列：

```sql
select * from dbo.ReplTest
```

執行下列 T-SQL 程式碼片段，以插入其他 「 發行者 」 上的資料列，然後檢查 一次在 「 訂閱者 」 上的資料列。 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>清除資源

若要卸除發行集，請執行下列 T-SQL 命令：

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

若要從資料庫移除複寫選項，請執行下列 T-SQL 命令：

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

若要停用發行和散發，請執行下列 T-SQL 命令：

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

您可以清除您的 Azure 資源所[刪除資源群組的受管理的執行個體資源](../azure-resource-manager/manage-resources-portal.md#delete-resources)然後刪除資源群組`SQLMI-Repl`。 

   
## <a name="see-also"></a>另请参阅

- [異動複寫](sql-database-managed-instance-transactional-replication.md)
- [什麼是受控執行個體？](sql-database-managed-instance.md)
