---
title: 透過 Azure SQL Database 進行異動複寫 | Microsoft Docs
description: 了解如何將 SQL Server 異動複寫搭配 Azure SQL Database 中的單一、集區和執行個體資料庫使用。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 86bd479eff48a7feb42557eb1d175345728f0a69
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879063"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>搭配 Azure SQL Database 中單一、集區和執行個體資料庫使用的異動複寫

異動複寫是 Azure SQL Database 和 SQL Server 的功能，可讓您將 Azure SQL Database 或 SQL Server 中資料表的資料複寫到遠端資料庫上的資料表。 此功能可讓您同步處理不同資料庫中的多個資料表。

## <a name="when-to-use-transactional-replication"></a>使用異動複寫的時機

異動複寫對於下列情況非常有用：
- 發佈在資料庫中的一或多個資料表中所進行的變更，然後將它們散發到訂閱變更的 SQL Server 或 Azure SQL 資料庫。
- 讓數個散發資料庫處於同步狀態。
- 藉由持續發佈變更, 將資料庫從一個 SQL Server 或受控實例遷移至另一個資料庫。

## <a name="overview"></a>總覽

下圖顯示異動複寫中的主要元件：  

![使用 SQL Database 的複寫](media/replication-to-sql-database/replication-to-sql-database.png)

**發行者**是執行個體或伺服器，它會藉由將更新傳送到散發者，來發行在某些資料表 (文章) 上的變更。 下列 SQL Server 版本支援從內部部署 SQL Server 發佈至任何 Azure SQL 資料庫：

- SQL Server 2019 (預覽)
- SQL Server 2016 至 SQL 2017
- SQL Server 2014 SP1 CU3 或更新版本 (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 或更新版本 (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- 針對其他不支援發行到 Azure 中物件的 SQL Server 版本，可以利用[重新發行](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)資料方法將資料移動到版本較新的 SQL Server。 

**散發者**是執行個體過伺服器，它會從發行者收集文章中的變更，然後將變更散發到訂閱者。 散發者可以是 Azure SQL Database 受控實例或 SQL Server (任何版本只要等於或高於發行者版本)。 

**訂閱者**是執行個體或伺服器，它會接收所收到的發行者所做的變更。 訂閱者可以是 Azure SQL Database 或 SQL Server 資料庫中的單一、集區和執行個體資料庫。 單一或集區資料庫上的訂閱者必須設定為發送訂閱者。 

| Role | 單一和集區資料庫 | 執行個體資料庫 |
| :----| :------------- | :--------------- |
| **發行者** | 否 | 是 | 
| **散發者** | 否 | 是|
| **提取訂閱者** | 否 | 是|
| **發送訂閱者**| 是 | 是|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > 當散發者是實例資料庫而不支援提取訂閱時, 則不支援提取訂閱。 

[複寫有不同類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)：


| 複寫 | 單一和集區資料庫 | 執行個體資料庫|
| :----| :------------- | :--------------- |
| [**標準交易式**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是 (僅作為訂閱者) | 是 | 
| [**快照集**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是 (僅作為訂閱者) | 是|
| [**合併式複寫**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**點對點**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| [**雙向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | 是|
| [**可更新訂閱**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - 嘗試使用舊版設定複寫可能會導致錯誤號碼 MSSQL_REPL20084 (處理序無法連線到訂閱者。) 和 MSSQ_REPL40532 (無法開啟登入所要求的公開伺服器 \<名稱>。 登入失敗。)。
  > - 若要使用 Azure SQL Database 的所有功能，您必須使用最新版的 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>實例資料庫和內部部署系統的可支援性矩陣
  實例資料庫的複寫可支援性對照表與內部部署 SQL Server 的相同。 
  
  | **發行者**   | **散發者** | **預訂** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL SERVER 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL SERVER 2008 R2 <br/> SQL Server 2008 | 
| SQL SERVER 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL SERVER 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL SERVER 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>需求

- 連線會在複寫參與者之間使用 SQL 驗證。 
- 與工作目錄共用且用於複寫的 Azure 儲存體帳戶。 
- 埠 445 (TCP 輸出) 必須在受控實例子網的安全性規則中開啟, 才能存取 Azure 檔案共用。 
- 如果發行者/散發者位於受控實例上, 而且訂閱者為內部部署, 則必須開啟埠 1433 (TCP 輸出)。


>[!NOTE]
> - 當「散發者」為實例資料庫且「訂閱者」為內部部署時, 如果輸出網路安全性群組 (NSG) 埠445遭到封鎖, 您可能會在連接到 Azure 儲存體檔案時遇到錯誤53。 [更新 VNET NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)以解決此問題。 
> - 如果受控實例上的發行者和散發者資料庫使用[自動容錯移轉群組](sql-database-auto-failover-group.md), 則受管理的實例系統管理員必須[刪除舊主要複本上的所有發行集, 並在發生容錯移轉之後, 在新的主要複本上重新設定它們](sql-database-managed-instance-transact-sql-information.md#replication)。

### <a name="compare-data-sync-with-transactional-replication"></a>比較資料同步與異動複寫

| | 資料同步 | 異動複寫 |
|---|---|---|
| 優點 | - 主動-主動支援<br/>- 在內部部署與 Azure SQL Database 之間雙向進行 | - 更低的延遲性<br/>- 交易一致性<br/>- 移轉後重複使用現有的拓撲 |
| 缺點 | - 5 分鐘或更多的延遲<br/>- 無交易一致性<br/>- 更高的效能影響 | - 無法從 Azure SQL Database 單一資料庫或集區資料庫發佈<br/>- 高維護成本 |
| | | |

## <a name="common-configurations"></a>一般設定

一般情況下，發行者和散發者必須位在雲端或內部部署中。 不支援下列設定： 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>在受控實例上具有本機散發者的發行者

![單一執行個體作為發行者和散發者](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

發行者和散發者設定在單一受控實例內, 並將變更散發到其他受控實例、單一資料庫、集區資料庫或內部部署 SQL Server。 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>在受控實例上具有遠端散發者的發行者

在此設定中, 一個受控實例會將變更發佈至位於另一個受控實例上的散發者, 此服務可提供許多來源受控實例, 並將變更散發到受控實例、單一資料庫、集區資料庫或上的一或多個目標SQL Server。

![發行者和散發者使用個別的執行個體](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

在兩個受控執行個體上設定發行者和散發者。 此設定有一些條件約束: 

- 這兩個受控實例都位於相同的 vNet 上。
- 這兩個受控執行個體位於相同的位置。


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>內部部署的發行者和散發者，以及單一、集區和執行個體資料庫上的訂閱者 

![Azure SQL DB 作為訂閱者](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
在此設定中，Azure SQL Database (單一、集區和執行個體資料庫) 是訂閱者。 此設定支援從內部部署移轉至 Azure。 如果訂閱者位於單一或集區資料庫上，則它必須處於發送模式。  


## <a name="next-steps"></a>後續步驟

1. [設定兩個受控實例間](replication-with-sql-database-managed-instance.md)的複寫。 
1. [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
1. [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)，方法是使用 Azure SQL Database 伺服器名稱作為訂閱者 (例如 `N'azuresqldbdns.database.windows.net`)，並使用 Azure SQL Database 名稱作為目的地資料庫 (例如 **AdventureWorks**)。 )
1. 瞭解[受控實例的異動複寫限制](sql-database-managed-instance-transact-sql-information.md#replication)



## <a name="see-also"></a>另請參閱  

- [使用 MI 和容錯移轉群組進行複寫](sql-database-managed-instance-transact-sql-information.md#replication)
- [複寫至 SQL Database](replication-to-sql-database.md)
- [複寫至受控實例](replication-with-sql-database-managed-instance.md)
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
