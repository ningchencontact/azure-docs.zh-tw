---
title: 將適用於 PostgreSQL 的 Azure 資料庫伺服器中的自動資料清理最佳化
description: 本文描述您可以如何將適用於 PostgreSQL 的 Azure 資料庫伺服器中的自動資料清理最佳化。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 21ac48ff473dcf494f96f87210bdfe09e4d82646
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103389"
---
# <a name="optimizing-autovacuum-on-azure-database-for-postgresql-server"></a>將適用於 PostgreSQL 的 Azure 資料庫伺服器上的自動資料清理最佳化 
本文描述如何將適用於 PostgreSQL 的 Azure 資料庫上的自動資料清理有效地最佳化。

## <a name="overview-of-autovacuum"></a>自動資料清理的概觀
PostgreSQL 使用 MVCC，以允許更高的資料庫並行。 每次更新都會導致插入和刪除，且每次刪除都會導致資料列虛標示為要刪除。 虛標記會導致無效 Tuple 在稍後被清除。 PostgreSQL 藉由執行資料清理作業達到這些目的。

資料清理作業可以由手動或自動的方式觸發。 當資料庫遇到大量更新或刪除作業時，會有較多無效 Tuple 存在，閒置時則較少。  當資料庫負載量大時，需要更頻繁地執行資料清理，因此**手動**執行資料清理作業會變得不方便。

您可以設定自動資料清理，並進行調整從中獲益。 隨 PostgreSQL 提供的預設值嘗試讓產品能在所有類型的裝置 (包括 Raspberry Pi) 上運作，而理想的設定值會根據數個因素而不同：
- 可用的總資源 - SKU 和儲存體大小。
- 資源使用狀況。
- 個別物件特性。

## <a name="autovacuum-benefits"></a>自動資料清理的優點
如果您沒有定時執行資料清理，累積無效 Tuple 可能導致：
- 資料膨脹 - 較大的資料庫和資料表。
- 較大且非最佳化的索引。
- I/O 增加。

## <a name="monitoring-bloat-with-autovacuum-queries"></a>使用自動資料清理查詢監視膨脹
下列範例查詢是設計來識別名為 "XYZ" 的資料表中，無效和有效 Tuple 的數目：'SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;'

## <a name="autovacuum-configurations"></a>自動資料清理設定
控制自動資料清理的設定參數和兩個問題相關：
- 它應該在何時啟動？
- 啟動之後它應該清除多少？

以下是您可以根據上述問題來更新的一些自動資料清理設定參數，以及一些指導方針：
參數|說明|預設值
---|---|---
autovacuum_vacuum_threshold|指定在任一資料表中，觸發 VACUUM 所需的已更新或已刪除 Tuple 的最小數量。 預設值為 50 個 Tuple。 此參數只能在 postgresql.conf 檔案中或在伺服器命令列上設定。 變更資料表儲存體參數可以針對個別資料表覆寫此設定。|50
autovacuum_vacuum_scale_factor|指定當決定是否要觸發 VACUUM 時，要加入至 autovacuum_vacuum_threshold 之資料表大小的比例。 預設為 0.2 (資料表大小的 20%)。 此參數只能在 postgresql.conf 檔案中或在伺服器命令列上設定。 變更資料表儲存體參數可以針對個別資料表覆寫此設定。|5%
autovacuum_vacuum_cost_limit|指定要用於自動 VACUUM 作業中的成本限制值。 如果指定 -1 (這是預設值)，系統會使用一般 vacuum_cost_limit 值。 該值會在執行中的自動資料清理背景工作角色之間按比例分散。 每個背景工作角色的上限之總和不會超過此變數的值。 此參數只能在 postgresql.conf 檔案中或在伺服器命令列上設定。 變更資料表儲存體參數可以針對個別資料表覆寫此設定。|-1
autovacuum_vacuum_cost_delay|指定要用於自動 VACUUM 作業中的成本延遲值。 如果指定 -1，系統會使用一般 vacuum_cost_delay 值。 預設值是 20 毫秒。 此參數只能在 postgresql.conf 檔案中或在伺服器命令列上設定。 變更資料表儲存體參數可以針對個別資料表覆寫此設定。|20 毫秒
autovacuum_nap_time|指定在任何給定的資料庫上，每回合自動資料清理之間的最小延遲。 精靈會在每回合中檢查資料庫，並針對該資料庫中的資料表視需要發出 VACUUM 和 ANALYZE 命令。 延遲是以秒為單位，預設值為一分鐘 (1 min)。 此參數只能在 postgresql.conf 檔案中或在伺服器命令列上設定。|15 秒
autovacuum_max_workers|指定在任何時間可執行的自動資料清理處理序 (不是自動資料清理啟動程式) 的最大數目。 預設值為三。 此參數只能在伺服器啟動時設定。|3
變更資料表儲存體參數可以針對個別資料表覆寫上述設定。  

## <a name="autovacuum-cost"></a>自動資料清理成本
以下是執行資料清理作業的「成本」:
- 系統正在上面執行資料清理的資料分頁會被鎖定。
- 當資料清理在執行時，會使用計算和記憶體。

這表示資料清理不應該太頻繁地或太不頻繁地執行，它需要隨工作負載調整。 建議您測試所有的自動資料清理參數變更，因為每個都有其取捨。

## <a name="autovacuum-start-trigger"></a>自動資料清理啟動觸發程序
當無效 Tuple 的數量超過 autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples 時會觸發自動資料清理，這裡的 reltuples 是常。

自動資料清理的清除必須跟上資料庫的負載，否則您可能會遇到儲存體不足或查詢普遍變慢的情況。 經過一段時間的攤還之後，資料清理清除無效 Tuple 的速率應該會等於無效 Tuple 的產生速率。

許多更新/刪除的資料庫會有更多無效 Tuple，且需要更多空間。 一般來說，低的 autovacuum_vacuum_scale_factor 值和低的 values of autovacuum_vacuum_threshold 值對有許多更新/刪除的資料庫有益，這樣可防止無效 Tuple 長時間累積。 對於較小的資料庫，您可以對這兩個參數使用較高的值，因為較不需要進行資料清理。 提醒您，頻繁執行資料清理的代價是耗用計算和記憶體。

預設的 20% 比例因素適合無效 Tuple 百分比低的資料表，無效 Tuple 百分比高的資料表則不適合。 例如，在 20 GB 大的資料表上這相當於 4 GB 的無效 Tuple，而在 1 TB 大的資料表上為 200 GB 的無效 Tuple。

使用 PostgreSQL 時，您可以在資料表層級或執行個體層級設定這些參數。 在適用於 PostgreSQL 的 Azure 資料庫中，您目前只能在資料表層級設定這些參數。

## <a name="estimating-the-cost-of-autovacuum"></a>估計自動資料清理的成本
執行自動資料清理的成本很高，因此系統有控制資料清理作業執行階段的參數。 下列參數可協助您估計執行資料清理的成本：
- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

資料清理處理序會讀取實體頁面並檢查無效 Tuple。 shared_buffers 中每個分頁的成本都視為 1 (vacuum_cost_page_hit)，所有其他分頁如果有無效 Tuple，其成本視為 20 (vacuum_cost_page_dirty)，如果沒有則為 10 (vacuum_cost_page_miss)。 當處理序超過 autovacuum_vacuum_cost_limit 時，資料清理作業就會停止。  

到達上限之後，處理序會根據 autovacuum_vacuum_cost_delay 參數指定的值睡眠一段時間，然後才再次啟動。 如果沒有到達上限，自動資料清理會根據 autovacuum_nap_time parameter 指定的值過一段時間之後啟動。

總而言之，autovacuum_vacuum_cost_delay 和 autovacuum_vacuum_cost_limit 參數控制每單位時間內允許清除多少資料。 請注意，預設值對於大部分的定價層都太低。 這些參數的最佳值隨定價層而不同，因此應該視情況來設定。

autovacuum_max_workers 參數決定可同時執行的自動資料清理處理序的最大數目。

使用 PostgreSQL 時，您可以在資料表層級或執行個體層級設定這些參數。 在適用於 PostgreSQL 的 Azure 資料庫中，您目前只能在資料表層級設定這些參數。

## <a name="optimizing-autovacuum-per-table"></a>針對個別資料表將自動資料清理最佳化
上述的所有設定參數，都可以對個別資料表設定，例如：
```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

自動資料清理是個別資料表同步處理序。 資料表的無效 Tuple 百分比越大，自動資料清理的「成本」就越高。  將更新/刪除比率高的資料表分割成多個資料表，有助於將自動資料清理平行化，並減少在一個資料表上完成自動資料清理的「成本」。 您也可以增加平行自動資料清理背景工作角色的數目，以確保背景工作角色充分地排程。

## <a name="next-steps"></a>後續步驟
檢閱下列 PostgreSQL 文件，以深入了解如何使用及調整自動資料清理：
 - PostgreSQL 文件 - [第 18 章：伺服器設定](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html) \(英文\)
 - PostgreSQL 文件 - [第 24 章：例行的資料庫維護工作](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html) \(英文\)
