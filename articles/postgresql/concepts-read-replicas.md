---
title: 適用於 PostgreSQL 的 Azure 資料庫中的讀取複本
description: 本文將說明適用於 PostgreSQL 的 Azure 資料庫中的讀取複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 270231b2ad7d94789595cfa4e681cf6c2b0f0541
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657870"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的讀取複本

> [!IMPORTANT]
> 讀取複本功能目前為「公開預覽」版。

讀取複本功能可讓您將資料從適用於 PostgreSQL 的 Azure 資料庫伺服器(主要) 複寫到相同 Azure 區域內最多五部的唯讀伺服器 (讀取複本)。 讀取複本會使用 PostgreSQL 引擎的原生複寫技術以非同步方式更新。

複本將會成為新的伺服器，並能以類似於一般獨立適用於 PostgreSQL 的 Azure 資料庫伺服器的方式管理。 針對每個讀取複本，系統每月會針對在 vCore 中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

瀏覽[操作說明頁面以了解如何建立及管理複本](howto-read-replicas-portal.md)。

## <a name="when-to-use-read-replicas"></a>何時應該使用讀取複本
讀取複本功能的目的是為了協助改善需大量讀取之工作負載的效能及調整能力。 例如，讀取工作負載可與複本隔離，而寫入工作負載則可以被導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀的，因此它們無法直接減輕主要伺服器上的寫入容量負擔，所以此功能不會以需大量寫入的工作負載為目標。

讀取複本功能會使用 PostgreSQL 的非同步複寫，所以不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="creating-a-replica"></a>建立複本
主要伺服器上的 **azure.replication_support** 必須設定為 [REPLICA]。 需要重新啟動伺服器，此參數的變更才會生效。 (**Azure.replication_support** 參數僅適用於「一般用途」和「記憶體最佳化」層級)。

當您起始建立複本工作流程時，系統會建立空白的適用於 PostgreSQL 的 Azure 資料庫伺服器。 新的伺服器會具有主要伺服器上的資料。 建立新複本所需的時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 這可能會是幾分鐘，也有可能需要花上幾小時的時間。

讀取複本功能會使用 PostgreSQL 的實體複寫 (非邏輯複寫)。 使用複寫位置的串流複寫是預設作業模式。 必要時，系統會使用記錄傳送來跟上進度。

> [!NOTE]
> 如果您尚未在伺服器上設定儲存體警示，我們建議您這麼做，以在伺服器接近其儲存體限制時通知您，因為這會影響到複寫。

[了解如何在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connecting-to-a-replica"></a>連線到複本
當您建立複本時，它並不會繼承主要伺服器的防火牆規則或 VNet 服務端點。 您必須個別針對複本設定這些規則。

複本會從主要伺服器繼承其系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用複本的主機名稱和有效的使用者帳戶來連線到該複本，如同連線到一般的適用於 PostgreSQL 的 Azure 資料庫伺服器一樣。 例如，如果伺服器名稱是 myreplica，而系統管理員使用者名稱是 myadmin，您便能以下列方式從 psql 連線到它：

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
並在出現提示時輸入使用者帳戶的密碼。

## <a name="monitoring-replication"></a>監視複寫
Azure 監視器中提供 [複本之間的最大延隔時間] 計量。 此計量僅適用於主要伺服器。 此計量會顯示主要伺服器和最大延隔複本之間的延隔 (位元組)。 

我們也在 Azure 監視器中提供 [複本延隔時間] 計量。 此計量僅適用於複本。 

此計量是從 pg_stat_wal_receiver 檢視計算：

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
請記得 [複本延隔時間] 計量會顯示在自最後一次重新執行交易已經過的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。

我們建議您設定警示，以在複本延隔時間接近您工作負載無法接受的值時通知您。 

如需其他見解，請直接查詢主要伺服器以取得所有複本的複本延隔時間 (以位元組為單位)。
在 PG 10 上：
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

在 PG 9.6 和較舊版本上：
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 如果主要伺服器或複本伺服器重新啟動，其重新啟動完畢並跟上進度所花費的時間將會反映在 [複本延隔時間] 計量中。

## <a name="stopping-replication-to-a-replica"></a>停止複寫至複本
您可以選擇停止主要伺服器與複本之間的複寫。 這會導致複本重新啟動以移除其複寫設定。 當主要伺服器和複本伺服器之間的複寫停止時，複本伺服器就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會試著跟上主要伺服器的進度。

此伺服器無法再次設定為複本伺服器。

在您停止複寫之前，請確定該伺服器上已經有您所需要的所有資料。

您可以[在操作指南文件中了解停止複本的方式](howto-read-replicas-portal.md)。


## <a name="considerations"></a>考量

### <a name="preparing-for-replica"></a>針對複本進行準備
在您可以建立複本之前，必須將主要伺服器上的 **azure.replication_support** 設定為 [REPLICA]。 需要重新啟動伺服器，此參數的變更才會生效。 (此參數僅適用於「一般用途」和「記憶體最佳化」層級)。

### <a name="stopped-replicas"></a>已停止的複本
如果您選擇停止主要伺服器和複本之間的複寫時，複本將會重新啟動以套用這些變更。 然後，複本將成為讀寫伺服器。 在此之後，您將無法再次將它設定為複本伺服器。

### <a name="replicas-are-new-servers"></a>複本是新伺服器
複本會被建立為新的適用於 PostgreSQL 的 Azure 資料庫伺服器。 現有的伺服器無法設定為複本。

### <a name="replica-server-configuration"></a>複本伺服器設定
複本伺服器是使用與主要伺服器相同的伺服器設定所建立，包括下列設定：
- 定價層
- 計算世代
- 虛擬核心
- 儲存體
- 備份保留期限
- 備份備援選項
- PostgreSQL 引擎版本

建立複本之後，其定價層 (基本層除外)、計算世代、vCore、儲存體，以及備份保留期間可以獨立於主要伺服器進行變更。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，複本的設定應該更新為等於或大於的值。 這可確保複本伺服器能夠跟上對主要伺服器所做的變更。

其中，Postgres 要求複本伺服器的 max_connections 參數值需大於或等於主要伺服器的值，否則複本將不會啟動。 在適用於 PostgreSQL 的 Azure 資料庫中，max_connections 值會根據 SKU 進行設定。 如需詳細資訊，請參閱[限制文件](concepts-limits.md)。 

嘗試進行違反此規則的更新將會導致錯誤。


### <a name="deleting-the-master"></a>刪除主要伺服器
刪除主要伺服器時，其所有讀取複本都會變成獨立伺服器。 這些複本都會重新啟動以反映此變更。

### <a name="other"></a>其他
- 只能在和主要伺服器相同的 Azure 區域中建立讀取複本。
- 不支援建立複本的複本。

## <a name="next-steps"></a>後續步驟
- [如何在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。
