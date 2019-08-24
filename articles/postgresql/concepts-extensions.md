---
title: 在適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中使用於 postgresql 擴充功能
description: 說明使用適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的延伸模組, 擴充資料庫功能的能力。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998068"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的于 postgresql 擴充功能-單一伺服器
PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 延伸模組會將多個相關的 SQL 物件組合在單一封裝中, 您可以使用單一命令從您的資料庫中載入或移除它們。 載入資料庫之後, 擴充功能就像內建功能一樣。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 擴充功能
您必須先在資料庫中安裝 PostgreSQL 擴充功能，然後才能使用它們。 若要安裝特定的擴充功能，請從 psql 工具執行  [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html)  命令，以便將封裝的物件載入至您的資料庫。

適用於 PostgreSQL 的 Azure 資料庫支援如下所列的金鑰延伸子集。 不支援所列的延伸模組。 您無法在適用於 PostgreSQL 的 Azure 資料庫中建立自己的延伸模組。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫支援的擴充功能
下表列出適用於 PostgreSQL 的 Azure 資料庫目前支援的標準 PostgreSQL 擴充功能。 此資訊也可以藉由執行 `SELECT * FROM pg_available_extensions;` 來取得。

### <a name="data-types-extensions"></a>資料類型擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | 為自動加密密碼提供資料類型。 |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 提供不區分大小寫的字元字串類型。 |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 提供多維度 Cube 的資料類型。 |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 提供用來儲存索引鍵/值組之集合的資料類型。 |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 提供國際產品編號標準的資料類型。 |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 提供階層式樹狀結構的資料類型。 |

### <a name="functions-extensions"></a>函數擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 提供方法來計算地球表面上的大圓距離。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 提供數個函數來判斷字串之間的相似性與距離。 |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | 提供函數和運算子來操作無 null 的整數陣列。 |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 提供密碼編譯函式。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 依時間或識別碼管理分割的資料表。 |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 提供可根據三併詞比對判斷英數文字相似度的函式和運算子。 |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | 提供操縱整個資料表 (包括交叉資料表) 的函式。 |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 產生通用唯一識別碼 (UUID)。 (請參閱以下關於此延伸模組的注意事項)。 |
> | [orafce](https://github.com/orafce/orafce) | 提供從商務資料庫模擬的函數和封裝子集。 |

### <a name="full-text-search-extensions"></a>全文檢索搜尋擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 提供整數的文字搜尋字典範本。 |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | 文字搜尋字典，可從詞彙中移除重音符號 (變音符號)。 |

### <a name="index-types-extensions"></a>索引類型擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 提供範例 GIN 運算子類別，可針對特定資料類型實作類似 B 型樹狀結構的行為。 |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | 提供可實作 B 型樹狀結構的 GiST 索引運算子類別。 |

### <a name="language-extensions"></a>語言擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 可載入的程序性語言。 |
> | [plv8](https://plv8.github.io/) | 可用於預存程序、觸發程序等的 PostgreSQL Javascript 語言擴充功能。 |

### <a name="miscellaneous-extensions"></a>其他擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 提供一種方法，即時檢查共用緩衝區快取中發生的狀況。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 提供一種方式，來將關聯資料載入至緩衝區快取。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 提供一種方法，來追蹤伺服器所執行之所有 SQL 陳述式的執行統計資料。 (請參閱以下關於此延伸模組的注意事項)。 |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 提供方法來顯示資料列層級鎖定的資訊。 |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 提供方法來顯示 Tuple 層級統計資料。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 可用來存取儲存於外部 PostgreSQL 伺服器之資料的外部資料包裝函式。 (請參閱以下關於此延伸模組的注意事項)。|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | 提供方法建立不使用 CPU 或磁碟的假設索引。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 支援從資料庫工作階段連線至其他 PostgreSQL 資料庫的模組。 (請參閱以下關於此延伸模組的注意事項)。 |


### <a name="postgis-extensions"></a>PostGIS 擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [PostGIS](https://www.postgis.net/)、postgis\_topology、postgis\_tiger\_geocoder、postgis\_sfcgal | 適用於 PostgreSQL 的空間與地理物件。 |
> | address\_standardizer、address\_standardizer\_data\_us | 用來將位址剖析為組成項目。 用來支援對位址進行地理編碼的正規化步驟。 |
> | [pgrouting](https://pgrouting.org/) | 擴充 PostGIS / PostgreSQL 地理空間資料庫，以提供地理空間路由功能。 |


### <a name="time-series-extensions"></a>時間序列擴充功能

> [!div class="mx-tableFixed"]
> | **擴充功能** | **描述** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | 支援自動資料分割的時間序列 SQL 資料庫, 以加快內嵌和查詢的速度。 針對時間序列工作負載提供時間導向的分析功能、優化和調整于 postgresql。 TimescaleDB 是由所開發, 並已註冊的[時間為, inc.](https://www.timescale.com/)的商標。(請參閱以下關於此延伸模組的注意事項)。 |

## <a name="postgres-11-extensions"></a>Postgres 11 延伸模組

在 Postgres 版本11的適用於 PostgreSQL 的 Azure 資料庫伺服器中, 可以使用下列延伸模組。

> [!div class="mx-tableFixed"]
> | **擴充功能**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支援 GIN 中的一般資料類型編制索引|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支援 GiST 中的一般資料類型編制索引|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1.5             | 不區分大小寫之字元字串的資料類型|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 從資料庫內連接到其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | 在地球表面上計算大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似和距離|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1.5             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | 一維整數陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 模擬商業 RDBMS 的函式和封裝子集的函數和運算子|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | 顯示資料列層級的鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 依時間或識別碼管理資料分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | 追蹤所有已執行之 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) 信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder 和 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | 操作整個資料表的函數, 包括交叉資料表|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | 產生通用唯一識別碼 (Uuid)|


## <a name="pg_stat_statements"></a>pg_stat_statements
系統會在每個適用於 PostgreSQL 的 Azure 資料庫伺服器上預先載入 pg_stat_statements 延伸模組, 以提供您追蹤 SQL 語句執行統計資料的方法。
控制延伸模組計入哪些陳述式的設定 `pg_stat_statements.track` 預設為 `top`，這表示會追蹤用戶端直接發出的所有陳述式。 其他兩個會追蹤層級 `none` 和 `all`。 這項設定是可透過 [Azure 入口網站](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 設定的伺服器參數。

pg_stat_statements 提供查詢執行資訊，不過會對於伺服器效能造成影響，因為它會記錄每個 SQL 陳述式。 如果您不主動使用 pg_stat_statements 延伸模組，我們建議您將 `pg_stat_statements.track` 設定為 `none`。 請注意，某些協力廠商監視服務可能會需要 pg_stat_statements 傳遞查詢效能深入解析，因此請確認這是否為您的情況。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
dblink 和 postgres_fdw 可讓您從一個 PostgreSQL 伺服器連線至另一個伺服器，或連線至相同伺服器中的另一個資料庫。 接收端伺服器必須允許來自傳送端伺服器通過其防火牆的連線。 將這些延伸模組用於「適用於 PostgreSQL 的 Azure 資料庫」伺服器之間的連線時，可藉由將 [允許存取 Azure 服務] 設為 [開啟] 來執行此動作。 如果您想要使用延伸模組來回路到相同的伺服器，也需要執行此動作。 [允許存取 Azure 服務] 設定可在「適用於 PostgreSQL 的 Azure 資料庫」伺服器的 Azure 入口網站頁面中找到，位於 [連線安全性] 下方。 開啟 [允許存取 Azure 服務] 時, 會將所有 Azure Ip 放在允許清單上。

目前不支援來自適用於 PostgreSQL 的 Azure 資料庫的輸出連線, 但與其他適用於 PostgreSQL 的 Azure 資料庫伺服器的連接除外。

## <a name="uuid"></a>uuid
如果您打算從 uuid- `uuid_generate_v4()` ossp 擴充功能中使用, 請考慮與`gen_random_uuid()` pgcrypto 延伸模組的比較, 以取得效能優勢。


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB 是一種時間序列資料庫, 封裝為于 postgresql 的延伸模組。 TimescaleDB 針對時間序列工作負載提供時間導向的分析功能、優化和調整 Postgres。

[深入瞭解 TimescaleDB](https://docs.timescale.com/latest), 這是一份已註冊的[時間刻度, inc.](https://www.timescale.com/)的商標

### <a name="installing-timescaledb"></a>安裝 TimescaleDB
若要安裝 TimescaleDB, 您必須將它包含在伺服器的共用預先載入程式庫中。 變更 Postgres 的`shared_preload_libraries`參數需要**伺服器重新開機**才會生效。 您可以使用[Azure 入口網站](howto-configure-server-parameters-using-portal.md)或[Azure CLI](howto-configure-server-parameters-using-cli.md)來變更參數。

> [!NOTE]
> 適用於 PostgreSQL 的 Azure 資料庫版本9.6 和10上可以啟用 TimescaleDB

使用[Azure 入口網站](https://portal.azure.com/):

1. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在提要欄位上, 選取 [**伺服器參數**]。

3. 搜尋 `shared_preload_libraries` 參數。

4. 選取 [ **TimescaleDB**]。

5. 選取 [**儲存**] 以保留您的變更。 儲存變更之後, 您會收到通知。 

6. 在通知之後, 請**重新開機**伺服器以套用這些變更。 若要了解如何重新啟動伺服器，請參閱[重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器](howto-restart-server-portal.md)。


您現在可以在 Postgres 資料庫中啟用 TimescaleDB。 連接到資料庫, 併發出下列命令:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果您看到錯誤, 請確認您在儲存 shared_preload_libraries 後[重新開機伺服器](howto-restart-server-portal.md)。 

您現在可以[從頭](https://docs.timescale.com/getting-started/creating-hypertables)開始建立 TimescaleDB hypertable, 或[在於 postgresql 中遷移現有的時間序列資料](https://docs.timescale.com/getting-started/migrating-data)。


## <a name="next-steps"></a>後續步驟
如果您未看見想要使用的擴充功能，請讓我們知道。 在我們的[意見反應論壇](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中投票現有要求或建立新的意見反應要求。
