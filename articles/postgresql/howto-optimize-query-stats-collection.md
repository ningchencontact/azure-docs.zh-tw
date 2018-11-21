---
title: 在適用於 PostgreSQL 的 Azure 資料庫伺服器查詢統計資料集合中最佳化查詢統計資料集合
description: 本文說明如在「適用於 PostgreSQL 的 Azure 資料庫」伺服器上最佳化查詢統計資料集合。
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8b8d1a5a16a948953838f51edca091121dc14df1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628436"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>在適用於 PostgreSQL 的 Azure 資料庫伺服器中最佳化查詢統計資料集合 
本文說明如在「適用於 PostgreSQL 的 Azure 資料庫」伺服器中最佳化查詢統計資料集合。

## <a name="using-pgstatsstatements"></a>使用 pg_stats_statements
**Pg_stat_statements** 是適用於 PostgreSQL 的 Azure 資料庫中依預設啟用的 PostgreSQL 擴充功能。 此擴充功能提供了相關機制，可對伺服器執行的所有 SQL 陳述式追蹤執行統計資料。 不過，此模組會在每次執行查詢連結，而產生頗高的效能成本。 啟用 **pg_stat_statements** 會強制將查詢文字寫入磁碟上的檔案。

若客戶有使用了較長查詢文字或未主動監視 **pg_stat_statements** 的特殊查詢，我們建議藉由設定 `pg_stat_statements.track = NONE` 將 **pg_stat_statements** 停用，以達到最佳效能。

在某些客戶工作負載上，我們發現停用 **pg_stat_statements** 最多可提升 50% 的效能。 不過，停用 pg_stat_statements 的缺點之一，是無法對效能問題進行疑難排解。

若要設定 `pg_stat_statements.track = NONE`：

- 在 Azure 入口網站中瀏覽至 [PostgreSQL 資源管理頁面，然後選取伺服器參數刀鋒視窗](howto-configure-server-parameters-using-portal.md)。

![PostgreSQL 伺服器參數刀鋒視窗](.\media\howto-optimize-query-stats-collection\pg_stats_statements_portal.png)

- 使用 [Azure CLI](howto-configure-server-parameters-using-cli.md) az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`。

## <a name="using-query-store"></a>使用查詢存放區 
適用於 PostgreSQL 的 Azure 資料庫中的[查詢存放區](concepts-query-store.md)功能提供了更能有效追蹤查詢統計資料的方法，建議您將其作為使用 *pg_stats_statements* 的替代方案。 

## <a name="next-steps"></a>後續步驟
考慮在 [Azure 入口網站](howto-configure-server-parameters-using-portal.md)中或使用 [Azure CLI](howto-configure-server-parameters-using-cli.md) 來設定 `pg_stat_statements.track = NONE`。

如需詳細資訊，請參閱[查詢存放區使用案例](concepts-query-store-scenarios.md)和[查詢存放區的最佳做法](concepts-query-store-best-practices.md)。 
