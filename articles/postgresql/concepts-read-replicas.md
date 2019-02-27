---
title: 適用於 PostgreSQL 的 Azure 資料庫中的讀取複本
description: 本文將說明如何在適用於 PostgreSQL 的 Azure 資料庫中使用讀取複本功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 40b31f166ea97cfce67d3cc386062e32338ffd45
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455512"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的讀取複本

讀取複本功能可讓您將資料從適用於 PostgreSQL 的 Azure 資料庫伺服器複寫到唯讀伺服器。 您可以從主要伺服器複寫到同一個 Azure 區域中的最多五個複本。 複本會使用 PostgreSQL 引擎的原生複寫技術以非同步方式更新。

> [!IMPORTANT]
> 讀取複本功能目前為公開預覽版。

複本是新伺服器，管理方式類似於一般適用於 PostgreSQL 的 Azure 資料庫伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

了解如何[建立及管理複本](howto-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本
讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

讀取複本功能會使用 PostgreSQL 非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="create-a-replica"></a>建立複本
主要伺服器必須將 `azure.replication_support` 參數設定為 **REPLICA**。 變更此參數後，必須重新啟動伺服器，才能讓變更生效。 (`azure.replication_support` 參數僅適用於「一般用途」和「記憶體最佳化」層級)。

當您開始建立複本的工作流程時，系統會建立空白的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

讀取複本功能會使用 PostgreSQL 的實體複寫，而非邏輯複寫。 使用複寫位置的串流複寫是預設作業模式。 必要時，系統會使用記錄傳送來跟上進度。

> [!NOTE]
> 如果您沒有在伺服器上設定儲存體警示，建議您這麼做。 警示會在伺服器接近儲存體限制時向您發出通知，因為此狀況會影響複寫。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本
當您建立複本時，其不會繼承主要伺服器的防火牆規則或 VNet 服務端點。 您必須個別針對複本設定這些規則。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用複本的主機名稱和有效的使用者帳戶來連線到該複本，如同連線到一般適用於 PostgreSQL 的 Azure 資料庫伺服器一樣。 若伺服器名稱為 **myreplica**，且統管理員使用者名稱為 **myadmin**，則您可以使用 psql 來連線到複本：

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫
適用於 PostgreSQL 的 Azure 資料庫會在 Azure 監視器中提供**複本之間的最大延隔時間**計量。 此計量僅適用於主要伺服器。 此計量會顯示主要伺服器和最大延隔複本之間的延隔 (位元組)。 

適用於 PostgreSQL 的 Azure 資料庫也會在 Azure 監視器中提供**複本延隔時間**計量。 此計量僅適用於複本。 

這個計量會從 `pg_stat_wal_receiver` 檢視中計算：

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

[複本延隔時間] 計量會顯示自最後一次重新執行交易已經過的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。

請設定警示，以在複本延隔時間接近您工作負載無法接受的值時通知您。 

如需其他見解，請直接查詢主要伺服器以取得所有複本的複本延隔時間 (以位元組為單位)。

在 PostgreSQL 第 10 版中：

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

在 PostgreSQL 第 9.6 版和較早版本中：

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 如果主要伺服器或讀取複本重新啟動，其重新啟動完畢並跟上進度所花費的時間將會反映在 [複本延隔時間] 計量中。

## <a name="stop-replication"></a>停止複寫
您可以停止主要伺服器與複本之間的複寫。 停止動作會導致複本重新啟動以移除其複寫設定。 當主要伺服器和讀取複本之間的複寫停止時，複本就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上主要伺服器。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。


## <a name="considerations"></a>考量

本節將摘要說明有關讀取複本功能的考量。

### <a name="prerequisites"></a>必要條件
建立讀取複本之前，`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 變更此參數後，必須重新啟動伺服器，才能讓變更生效。 `azure.replication_support` 參數僅適用於「一般用途」和「記憶體最佳化」層級。

### <a name="new-replicas"></a>新複本
讀取複本會建立為最新適用於 PostgreSQL 的 Azure 資料庫伺服器。 現有伺服器無法設定為複本。 只能在和主要伺服器相同的 Azure 區域中建立讀取複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定
系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

PostgreSQL 會要求讀取複本上的 `max_connections` 參數值大於或等於主要伺服器的值，否則讀取複本將不會啟動。 在適用於 PostgreSQL 的 Azure 資料庫中，`max_connections` 參數值會以 SKU 作為基礎。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫限制](concepts-limits.md)。 

如果您嘗試更新伺服器的值，但並未遵循這些限制，則您會收到錯誤。

### <a name="stopped-replicas"></a>已停止的複本
如果您停止主要伺服器和讀取複本之間的複寫，複本將會重新啟動以套用變更。 停止的複本會變成支接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器
刪除主要伺服器時，其所有讀取複本都會變成獨立伺服器。 這些複本將會重新啟動以反映此變更。

## <a name="next-steps"></a>後續步驟
了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。
