---
title: Azure Database for PostgreSQL-超大規模 (Citus) （預覽） 中的散發的資料
description: 資料表和散發的伺服器群組中的分區。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077331"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL-超大規模 (Citus) （預覽） 中的散發的資料

本文章概述三個資料表中的型別超大規模 (Citus)。
它會顯示如何分散式的資料表會儲存為分區和分區會放在節點的方式。

## <a name="table-types"></a>資料表類型

有三種類型的超大規模的伺服器群組中的資料表，每個用於不同用途。

### <a name="type-1-distributed-tables"></a>類型 1： 分散式的資料表

第一種類型，以及大部分常見的是*分散式*資料表。 它們似乎是一般資料表 SQL 陳述式，但有 水平*分割*背景工作節點之間。 這表示是資料表的資料列會儲存在稱為 「 片段資料表的不同節點上*分區*。

超大規模執行不只是 SQL，但在整個叢集中，因此變更配置資源分散式資料表的結構描述的 DDL 陳述式動作會串聯到背景工作角色之間更新所有資料表的分區。

#### <a name="distribution-column"></a>散發資料行

超大規模使用演算法的分區化資料列指派給分區。 指派已確定的方式根據呼叫的資料表資料行值*散發資料行。* 叢集系統管理員必須指定這個資料行，當散發資料表。
選擇正確是很重要的效能和功能。

### <a name="type-2-reference-tables"></a>類型 2： 參考資料表

參考資料表是一種集中到單一分區的整個內容配置資源分散式資料表。 分區會複寫每個背景工作角色，在中，因此任何背景工作角色上的查詢可以存取的參考資訊在本機，而不會要求資料列，從另一個節點的網路負擔。 參考資料表有任何散發資料行，因為不需要區分每個資料列的個別分區。

參考資料表通常很小，而且用來儲存的任何背景工作節點上執行查詢的相關資料。 例如，列舉值，例如訂單狀態 」 或 「 產品類別目錄。

### <a name="type-3-local-tables"></a>類型 3： 本機資料表

當您使用超大規模時，您連接至協調器節點會是一般的 PostgreSQL 資料庫。 您可以協調器上建立一般的資料表，並選擇不分區它們。

適合做為本機資料表是小型系統管理的資料表，且不會參與聯結查詢。 例如，使用者資料表的應用程式登入和驗證。

## <a name="shards"></a>分區

上一節所述的方式分散式的資料表會儲存為背景工作節點上的分區。 這一節取得更深入的技術詳細資料。

`pg_dist_shard`協調器上的中繼資料資料表包含系統中的每個分區的每個配置資源分散式資料表的資料列。 資料列符合分區識別碼範圍的整數雜湊空格 （shardminvalue、 shardmaxvalue）：

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

如果協調器節點會想要判斷哪一個分區所保存的資料列`github_events`，它會雜湊散發資料行的資料列中的值，並檢查哪一個分區\'的範圍包含雜湊的值。 （以便雜湊函式的影像是它們不相鄰的聯集已定義的範圍）。

### <a name="shard-placements"></a>分區位置

假設該分區 102027 是有問題的資料列相關聯。 會讀取或寫入呼叫資料表中資料列`github_events_102027`中其中一個背景工作角色。 哪一個背景工作？ 完全取決於中繼資料資料表和背景工作角色的分區對應也稱為分區*放置*。

協調器節點重寫查詢，為片段以參考特定資料表，如同`github_events_102027`，並在適當的背景工作角色上執行這些片段。 找不到保存分區識別碼 102027 節點在幕後執行的查詢範例如下。

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>後續步驟
- 了解如何[選擇散發資料行](concepts-hyperscale-choose-distribution-column.md)分散式資料表
