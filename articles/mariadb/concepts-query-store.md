---
title: 適用於 MariaDB 的 Azure 資料庫中的查詢存放區
description: 本文說明適用於 MariaDB 的 Azure 資料庫中的查詢存放區功能
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 9016fa159e868f649901928cdf2dca2f08725e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079388"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>使用查詢存放區的 MariaDB 效能進行監視的 Azure 資料庫

**適用於：**  10.2 的 MariaDB 的 Azure 資料庫

> [!NOTE]
> 查詢存放區處於預覽狀態。 在 Azure 入口網站支援查詢存放區正推出，並可能尚無法在您的區域。

適用於 Mariadb 的 Azure 資料庫中的查詢存放區功能可用來追蹤一段時間的查詢效能。 查詢存放區可協助您快速找到執行時間最長又最耗資源的查詢，簡化效能疑難排解。 查詢存放區會自動擷取查詢的歷程記錄和執行階段統計資料，並予以保留以供您檢閱。 依時間範圍區分資料，以便查看資料庫使用模式。 針對所有使用者、 資料庫和查詢的資料會儲存在**mysql** MariaDB 的執行個體的 Azure 資料庫中的結構描述資料庫。

## <a name="common-scenarios-for-using-query-store"></a>使用查詢存放區的常見案例

查詢存放區可以用於許多案例，包括下列：

- 偵測迴歸的查詢
- 判斷查詢在指定時間範圍內的執行次數
- 跨時間範圍比較查詢的平均值行時間，查看大幅差異
- 識別在過去 X 小時中執行最久的查詢
- 識別等候資源的前 N 項查詢
- 了解查詢的等候本質。
- 資源等候和資源爭用所在的了解趨勢

## <a name="enabling-query-store"></a>啟用查詢存放區

查詢存放區是選擇加入的功能，因此預設不會在伺服器上啟用。 啟用或停用全域指定伺服器上的所有資料庫的查詢存放區並不能設為開啟或關閉每個資料庫。

### <a name="enable-query-store-using-the-azure-portal"></a>使用 Azure 入口網站啟用查詢存放區

1. 登入 Azure 入口網站，然後選取 Azure Database for MariaDB 伺服器。
1. 選取  **伺服器參數** 中 **設定** 功能表一節。
1. 搜尋 query_store_capture_mode 參數。
1. 將值設為所有與 **儲存**。

若要啟用查詢存放區中的等候統計資料：

1. 搜尋 query_store_wait_sampling_capture_mode 參數。
1. 將值設為所有與 **儲存**。

允許 20 分鐘的時間来保存的 mysql 資料庫中資料的第一個批次。

## <a name="information-in-query-store"></a>查詢存放區中的資訊

查詢存放區有兩個存放區：

- 執行階段統計資料存放區保存查詢執行統計資料資訊。
- 保存的等候統計資料存放區等候統計資料資訊。

空間使用量降到最低，在執行階段統計資料存放區中的執行階段執行統計資料會彙總一段固定且可設定的時間範圍。 查詢這些查詢存放區檢視，即可看到這些存放區中的資訊。

下列查詢會傳回查詢存放區中的相關資訊：

```sql
SELECT * FROM mysql.query_store;
```

或者，此查詢等候統計資料：

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>尋找等候查詢

等候事件類型會依相似性，將不同的等候事件結合成貯體。 查詢存放區提供等候事件類型、特定的等候事件名稱，以及有問題的查詢。 能夠將此等候資訊與查詢執行階段相互關聯，表示您可以更深入了解查詢效能特性從何而來。

以下是如何查詢存放區中的等候統計資料，以更多深入了解工作負載的一些範例：

| **觀測** | **Action** |
|---|---|
|高鎖定等候數 | 查看受影響查詢的查詢文字，並找出目標實體。 查看查詢存放區，針對經常執行和/或持續時間很長的實體，尋找修改同一實體的其他查詢。 找出這些查詢之後，請考慮變更應用程式邏輯，改善並行存取，或使用限制較少的隔離等級。 |
|高緩衝區 IO 等候數 | 在查詢存放區中尋找實體讀取次數高的查詢。 如果它們符合高 IO 等候的查詢，請考慮引入基礎實體，執行索引搜尋而不是掃描。 這可將查詢的 IO 額外負荷降到最低。 請檢查 **效能建議** 伺服器的入口網站，以查看是否有此伺服器會最佳化查詢的索引建議。 |
|高記憶體等候數 | 找出查詢存放區中記憶體耗用量名列前茅的查詢。 這些查詢可能會進一步延遲受影響查詢的進度。 請檢查 **效能建議** 伺服器的入口網站，以查看是否有會最佳化這些查詢的索引建議。|

## <a name="configuration-options"></a>組態選項

啟用查詢存放區時，會以每 15 分鐘的彙總時間範圍儲存資料一次，每個範圍內最多可有 500 個相異的查詢。

下列選項可用於設定查詢存放區參數。

| **參數** | **說明** | **預設值** | **Range** |
|---|---|---|---|
| query_store_capture_mode | 開啟 ON/OFF 根據值的查詢存放區功能。 注意：如果 performance_schema 是 OFF，開啟 query_store_capture_mode 會開啟 performance_schema 和這項功能所需的效能架構 instruments 的子集。 | ALL | 無、 所有 |
| query_store_capture_interval | 查詢存放區擷取間隔，以分鐘為單位。 可讓您指定的間隔查詢度量會彙總 | 15 | 5 - 60 |
| query_store_capture_utility_queries | 開啟 ON 或 OFF 來擷取系統中執行的所有公用程式查詢。 | 否 | 是，否 |
| query_store_retention_period_in_days | 時間範圍，以天為保留查詢存放區中的資料。 | 7 | 1 - 30 |

下列選項特別適用於等候統計資料。

| **參數** | **說明** | **預設值** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 可用來開啟 ON / OFF 的等候統計資料。 | 無 | 無、 所有 |
| query_store_wait_sampling_frequency | 改變以秒為單位的等候取樣頻率。 5 到 300 秒。 | 30 | 5-300 |

> [!NOTE]
> 目前**query_store_capture_mode**會取代此組態中，這表示同時**query_store_capture_mode**並**query_store_wait_sampling_capture_mode**必須先啟用所有工作的等候統計資料。 如果**query_store_capture_mode**已關閉，則等候統計資料會關閉也因為等候統計資料會利用已啟用，performance_schema 和查詢存放區擷取 query_text。

使用 [Azure 入口網站](howto-server-parameters.md)来取得或設定參數的不同值。

## <a name="views-and-functions"></a>檢視和函式

使用下列檢視和函式來檢視和管理查詢存放區。 中的任何人[select 的權限 public 角色](howto-create-users.md#create-additional-admin-users)可以使用這些檢視來查看查詢存放區中的資料。 這些檢視所僅適用於**mysql** 資料庫。

移除常值和常數之後，查看查詢結構，其會呈現標準化。 如果兩個查詢完全相同 (但常值除外)，則兩者會有相同的雜湊碼。

### <a name="mysqlquerystore"></a>mysql.query_store

此檢視會傳回查詢存放區中的所有資料。 不同的資料庫識別碼、使用者識別碼及查詢識別碼都會自成一資料列。

| **名稱** | **資料類型** | **IS_NULLABLE** | **說明** |
|---|---|---|---|
| `schema_name`| varchar(64) | 否 | 結構描述名稱 |
| `query_id`| bigint(20) | 否| 如果相同的查詢執行在不同的結構描述，新的識別碼，以特定的查詢，產生的唯一識別碼將會產生 |
| `timestamp_id` | timestamp| 否| 執行查詢的時間戳記。 這根據 query_store_interval 組態|
| `query_digest_text`| longtext| 否| 移除所有的常值後的正規化的查詢文字|
| `query_sample_text` | longtext| 否| 第一個出現的常值與實際的查詢|
| `query_digest_truncated` | bit| 是| 是否已遭到截斷的查詢文字。 如果查詢超過 1 KB 值將會是 [是]|
| `execution_count` | bigint(20)| 否| 查詢此時間戳記識別碼 / 設定的間隔期間執行的次數|
| `warning_count` | bigint(20)| 否| 此查詢期間，內部所產生的警告數目|
| `error_count` | bigint(20)| 否| 此查詢間隔期間所產生的錯誤數目|
| `sum_timer_wait` | double| 是| 此查詢間隔期間的總執行時間|
| `avg_timer_wait` | double| 是| 此查詢間隔期間的平均執行時間|
| `min_timer_wait` | double| 是| 此查詢的最小執行時間|
| `max_timer_wait` | double| 是| 最長執行時間|
| `sum_lock_time` | bigint(20)| 否| 這個查詢執行，這段期間的所有鎖定所花費的總時間量|
| `sum_rows_affected` | bigint(20)| 否| 受影響的資料列數目|
| `sum_rows_sent` | bigint(20)| 否| 傳送至用戶端的資料列數目|
| `sum_rows_examined` | bigint(20)| 否| 檢查的資料列數目|
| `sum_select_full_join` | bigint(20)| 否| 完整聯結的數目|
| `sum_select_scan` | bigint(20)| 否| 選取的掃描次數 |
| `sum_sort_rows` | bigint(20)| 否| 排序的資料列數目|
| `sum_no_index_used` | bigint(20)| 否| 次數時查詢未使用任何索引|
| `sum_no_good_index_used` | bigint(20)| 否| 次數，當查詢執行引擎未使用任何好的索引|
| `sum_created_tmp_tables` | bigint(20)| 否| 建立的暫存資料表的總次數|
| `sum_created_tmp_disk_tables` | bigint(20)| 否| （會產生 I/O） 的磁碟中建立的暫存資料表的總次數|
| `first_seen` | timestamp| 否| 第一次出現 (UTC) 查詢的彙總期間|
| `last_seen` | timestamp| 否| 最後一次出現 (UTC) 的查詢此彙總期間|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

此檢視會傳回查詢存放區中的等候事件資料。 不同的資料庫識別碼、使用者識別碼、查詢識別碼及事件都會自成一資料列。

| **名稱**| **資料類型** | **IS_NULLABLE** | **說明** |
|---|---|---|---|
| `interval_start` | timestamp | 否| 間隔 （15 分鐘增量） 開始的|
| `interval_end` | timestamp | 否| （15 分鐘增量） 在間隔結束|
| `query_id` | bigint(20) | 否| 正規化的查詢 （從查詢存放區） 上產生的唯一識別碼|
| `query_digest_id` | varchar(32) | 否| 移除所有常值 （從查詢存放區） 之後的正規化的查詢文字 |
| `query_digest_text` | longtext | 否| 第一個出現的常值 （從查詢存放區） 與實際的查詢 |
| `event_type` | varchar(32) | 否| 等候事件的類別 |
| `event_name` | varchar(128) | 否| 等候事件的名稱 |
| `count_star` | bigint(20) | 否| 取樣的查詢間隔期間的等候事件數目 |
| `sum_timer_wait_ms` | double | 否| 總等候時間 （以毫秒為單位） 此查詢的間隔時間內在 |

### <a name="functions"></a>函式

| **名稱**| **說明** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 清除所有指定的時間戳記之前的查詢存放區資料 |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 清除其他所有等候指定的時間戳記之前的事件資料 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 會清除其到期之前的指定的時間戳記的建議 |

## <a name="limitations-and-known-issues"></a>限制與已知問題

- 如果 MariaDB 伺服器具有參數`default_transaction_read_only`，查詢存放區無法擷取資料。
- 如果遇到長時間的 Unicode 查詢，查詢存放區功能可能會中斷 (\>= 6000 個位元組)。
- 等候統計資料的保留期限為 24 小時。
- 等候統計資料會使用範例 ti 擷取一小部分事件。 頻率可以使用參數來修改`query_store_wait_sampling_frequency`。

## <a name="next-steps"></a>後續步驟

- 深入了解[查詢效能深入解析](concepts-query-performance-insight.md)
