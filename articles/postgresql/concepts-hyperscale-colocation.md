---
title: 適用於 PostgreSQL 的 Azure 資料庫中的伺服器概念
description: 本文提供設定及管理適用於 PostgreSQL 之 Azure 資料庫伺服器的考量和指導方針。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077466"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>在 Azure Database for PostgreSQL-超大規模 (Citus) （預覽） 的資料表共置

共置表示一起儲存在相同節點上的相關的資訊。 查詢即可，毋需任何網路流量的所有必要的資料時，快速。 共置不同節點上的相關的資料，可讓查詢有效率地以平行方式執行每個節點上。

## <a name="data-colocation-for-hash-distributed-tables"></a>雜湊分散式資料表的資料共置

在超大規模的資料列會儲存在分區中，如果雜湊散發資料行中的值落在分區的雜湊的範圍內。 使用相同的雜湊範圍分區會放置在相同節點上。 平均散發資料行值的資料列都在相同節點上整個資料表。

![分區](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>共置的實用範例

請考慮下列資料表中可能是多租用戶 web analytics SaaS 的一部分：

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

現在我們想要回答的查詢，可能會公布的面向客戶的儀表板，例如：「 傳回過去一週的開頭的所有頁面瀏覽數 '/ 部落格' 六個的租用戶中。 」

如果我們的資料位於單一伺服器部署選項，我們就可以輕鬆地表示我們使用 SQL 所提供的關聯式作業的一組豐富的查詢：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

只要[工作組](https://en.wikipedia.org/wiki/Working_set)單一伺服器資料表的這項查詢放入記憶體，是適當的解決方案。 不過，我們來看一下縮放的超大規模的部署選項與資料模型的機會。

### <a name="distributing-tables-by-id"></a>散發資料表的識別碼

單一伺服器查詢開始變慢，因為租用戶和每個租用戶中儲存的資料數量成長。 工作集調整大小使其在記憶體中的停駐點和 CPU 成為瓶頸。

在此情況下，我們會使用超大規模的多節點之間可以分區資料。 我們需要進行分區化時散發資料行第一個也是最重要選項。 讓我們開始使用各種貝氏`event_id`事件資料表和`page_id`如`page`資料表：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

當資料散佈於不同的背景工作角色時，我們無法執行聯結，因為我們會在單一的 PostgreSQL 節點上。 相反地，我們必須發出兩個查詢：

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

之後，兩個步驟的結果必須結合應用程式。

執行查詢，必須參閱散布在節點的分區中的資料。

![效率不佳的查詢](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

在此情況下資料分佈會建立大量的缺點：

-   從查詢執行多個查詢的每個分區的額外負荷
-   傳回至用戶端的多個資料列的第 1 季負擔
-   第 2 季變得大
-   您不必在多個步驟中撰寫查詢需要應用程式中的變更

因為資料已分散，查詢可以平行處理。 不過，它才有幫助的查詢會執行的工作數量大於本質上查詢許多分區的額外負荷。

### <a name="distributing-tables-by-tenant"></a>散發租用戶資料表

在超大規模，具有相同的散發資料行值的資料列會保證在相同節點上。 從頭開始，我們可以建立具有資料表`tenant_id`作為散發資料行。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

現在超大規模可以回答原始的單一伺服器查詢，而不需修改 （第 1 季）：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

因為篩選和聯結 tenant_id，超大規模會知道可以使用共置的分區，其中包含該特定租用戶的資料集回答整個查詢。 單一 PostgreSQL 節點可以回答的查詢，以單一步驟。

![更佳的查詢](media/concepts-hyperscale-colocation/colocation-better-query.png)

在某些情況下，查詢和資料表結構描述必須變更唯一條件約束中包含租用戶識別碼，並聯結條件。 不過，這通常是簡單的變更。

## <a name="next-steps"></a>後續步驟

- 請參閱 如何將租用戶資料共置於[多租用戶教學課程](tutorial-design-database-hyperscale-multi-tenant.md)
