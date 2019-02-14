---
title: 透過 Azure SQL Database 進行異動複寫 | Microsoft Docs
description: 了解如何將 SQL Server 異動複寫搭配 Azure SQL Database 中的獨立、集區和執行個體資料庫使用。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1c542c1e906b078b76b78ed30af8bdf67110199c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814107"
---
# <a name="transactional-replication-with-standalone-pooled-and-instance-databases-in-azure-sql-database"></a>搭配 Azure SQL Database 中獨立、集區和執行個體資料庫使用的異動複寫

異動複寫是 Azure SQL Database 和 SQL Server 的功能，可讓您將 Azure SQL Database 或 SQL Server 中資料表的資料複寫到遠端資料庫上的資料表。 此功能可讓您同步處理不同資料庫中的多個資料表。

## <a name="when-to-use-transactional-replication"></a>使用異動複寫的時機

異動複寫對於下列情況非常有用：

- 發佈在資料庫中的一或多個資料表中所進行的變更，然後將它們散發到訂閱變更的 SQL Server 或 Azure SQL 資料庫。
- 讓數個散發資料庫處於同步狀態。
- 藉由持續發佈變更，將資料庫從 SQL Server 或受控執行個體移轉到其他資料庫。

## <a name="overview"></a>概觀

下圖顯示異動複寫中的主要元件：  

![使用 SQL Database 的複寫](media/replication-to-sql-database/replication-to-sql-database.png)


**發行者**是執行個體或伺服器，它會藉由將更新傳送到散發者，來發行在某些資料表 (文章) 上的變更。 下列 SQL Server 版本支援從內部部署 SQL Server 發佈至任何 Azure SQL Database：

   - SQL Server 2019 (預覽)
   - SQL Server 2016 至 SQL 2017
   - SQL Server 2014 SP1 CU3 或更新版本 (12.00.4427)
   - SQL Server 2014 RTM CU10 (12.00.2556)
   - SQL Server 2012 SP3 或更新版本 (11.0.6020)
   - SQL Server 2012 SP2 CU8 (11.0.5634.0)
   - 針對其他不支援發行到 Azure 中物件的 SQL Server 版本，可以利用[重新發行](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)資料方法將資料移動到版本較新的 SQL Server。 

**散發者**是執行個體過伺服器，它會從發行者收集文章中的變更，然後將變更散發到訂閱者。 散發者可以是 Azure SQL Database 受控執行個體或 SQL 伺服器 (只要是任何與發行者相同的版本或更新版本都可以)。 

**訂閱者**是執行個體或伺服器，它會接收所收到的發行者所做的變更。 訂閱者可以是 Azure SQL Database 或 SQL Server 資料庫中的獨立、集區和執行個體資料庫。 獨立或集區資料庫上的訂閱者必須設定成發送訂閱者。 

| 角色 | 獨立和集區資料庫 | 執行個體資料庫 |
| :----| :------------- | :--------------- |
| **發行者** | 否 | yes | 
| **散發者** | 否 | yes|
| **提取訂閱者** | 否 | yes|
| **發送訂閱者**| yes | yes|
| &nbsp; | &nbsp; | &nbsp; |

[複寫有不同類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017)：


| 複寫 | 獨立和集區資料庫 | 執行個體資料庫|
| :----| :------------- | :--------------- |
| [**交易式**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是 (僅作為訂閱者) | yes | 
| [**快照集**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是 (僅作為訂閱者) | yes|
| [**合併式複寫**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**點對點**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| **單向** | yes | yes|
| [**雙向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | yes|
| [**可更新訂閱**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - 嘗試使用舊版設定複寫可能會導致錯誤號碼 MSSQL_REPL20084 (處理序無法連線到訂閱者。) 和 MSSQ_REPL40532 (無法開啟登入所要求的公開伺服器 \<名稱>。 登入失敗。)。
  > - 若要使用 Azure SQL Database 的所有功能，您必須使用最新版的 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) 和 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)。

## <a name="requirements"></a>需求

- 連線會在複寫參與者之間使用 SQL 驗證。 
- 與工作目錄共用且用於複寫的 Azure 儲存體帳戶。 
- 必須在受控執行個體子網路的安全性規則中開啟連接埠 445 (TCP 輸出) 才能存取 Azure 檔案共用。 
- 如果發行者/散發者是在受控執行個體上且訂閱者在內部部署中，則需要開啟連接埠 1433 (TCP 輸出)。 

## <a name="common-configurations"></a>一般設定

一般情況下，發行者和散發者必須位在雲端或內部部署中。 不支援下列設定： 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>本機散發者在受控執行個體上的發行者

![單一執行個體作為發行者和散發者 ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

發行者和散發者設定在單一受控執行個體內，並會將變更散發到其他受控執行個體、單一資料庫、集區資料庫或內部部署 SQL Server。 在此設定中，發行者/散發者受控執行個體無法設定為[異地複寫和自動容錯移轉群組](sql-database-auto-failover-group.md)。

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>遠端散發者在受控執行個體上的發行者

在此設定中，一個受控執行個體將變更發佈到位在其他受控執行個體上的散發者，它能服務許多來源受控執行個體，並將變更散發到受控執行個體、單一資料庫、集區資料庫或 SQL Server 上的一或多個目標。

![發行者和散發者使用個別的執行個體](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

在兩個受控執行個體上設定發行者和散發者。 在此設定中

- 這兩個受控執行個體位於相同的 vNet 上。
- 這兩個受控執行個體位於相同的位置。
- 裝載發行和散發者資料庫的執行個體無法[使用自動容錯移轉群組進行異地複寫](sql-database-auto-failover-group.md)。

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-standalone-pooled-and-instance-database"></a>內部部署的發行者和散發者，及獨立、集區和執行個體資料庫上的訂閱者 

![Azure SQL DB 作為訂閱者](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
在此設定中，Azure SQL Database (獨立、集區和執行個體資料庫) 是訂閱者。 此設定支援從內部部署移轉至 Azure。 如果訂閱者是在獨立或集區資料庫上，則它必須處於發送模式。  

## <a name="next-steps"></a>後續步驟

1. [為受控執行個體設定異動複寫](replication-with-sql-database-managed-instance.md#configure-publishing-and-distribution-example)。 
1. [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
1. [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)，方法是使用 Azure SQL Database 伺服器名稱作為訂閱者 (例如 `N'azuresqldbdns.database.windows.net`)，並使用 Azure SQL Database 名稱作為目的地資料庫 (例如 **AdventureWorks**)。 )


## <a name="see-also"></a>另請參閱  

- [複寫至 SQL Database](replication-to-sql-database.md)
- [複寫至受控執行個體](replication-with-sql-database-managed-instance.md)
- [建立發行集](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [建立發送訂閱](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [複寫的類型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (複寫)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化訂閱](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
