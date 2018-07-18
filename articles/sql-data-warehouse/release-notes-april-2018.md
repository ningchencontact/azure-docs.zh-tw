---
title: 2018 年 4 月 Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 49ffc3ddfd586964ae8a9688aeb48fffdd327b45
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738948"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Azure SQL 資料倉儲 (2018 年 4 月) 的最新功能？
Azure SQL 資料倉儲會持續收到改進功能。 本文說明 2018 年 4 月導入的新功能和變更。

## <a name="features"></a>特性

### <a name="ability-to-truncate-a-partition-before-a-switch"></a>在切換之前截斷資料分割的功能
客戶通常使用分割切換作為透過 `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` 語法變更資料表的中繼資料，將資料從一個資料表載入到另一個資料表的模式。 當目標資料分割包含資料時，SQL 資料倉儲不支援分割切換。 如果目標資料分割已經包含資料，則客戶需要截斷目標資料分割，然後執行切換。

SQL 資料倉儲現在支援單一 T-SQL 陳述式中的此操作。

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
如需詳細資訊，請參閱 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) 一文。

### <a name="improved-query-compilation-performance"></a>改進查詢編譯效能
SQL 資料倉儲導入了一組變更，以改進分散式查詢的查詢編譯步驟。 這些變更將查詢編譯時間提高 **10 倍**，從而降低整體查詢執行的執行階段。 這些變更在具有大量物件 (資料表、函數、檢視、程序) 的資料倉儲中更為明顯。

## <a name="behavior-changes"></a>行為變更

### <a name="dbcc-commands-do-not-consume-concurrency-slots"></a>DBCC 命令不會耗用並行位置
SQL 資料倉儲支援 T-SQL [DBCC 命令](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql)的子集，例如 [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql)。 之前，這些命令會耗用[並行位置](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots)，減少可執行的使用者載入/查詢數量。 `DBCC` 命令現在會在本機佇列中運行，不會耗用資源位置，從而改善整體的查詢執行效能。

### <a name="updated-error-message-for-excessive-literals"></a>更新過多常值的錯誤訊息
之前，當查詢包含太多的常值時，SQL 資料倉儲會包含「近似」計數。
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

錯誤訊息已更新，僅會表示您已到達常值限制。
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

如需詳細資訊，請參閱[容量限制](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits)一文的[查詢](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries)一節，以了解有關上限的其他詳細資料。

### <a name="removed-the-syspdwdatabasemappings-view"></a>移除 SYS.PDW_DATABASE_MAPPINGS 檢視
此 `sys.pdw_database_mappings` 檢視在 SQL 資料倉儲中未被使用。 之前，此檢視的 SELECT 不會傳回任何結果。 檢視已遭移除。 