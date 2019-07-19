---
title: 讀取適用於 MariaDB 的 Azure 資料庫中的複本
description: 本文說明適用於 MariaDB 的 Azure 資料庫的讀取複本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: e6bbe15727a6f989d8c16c67591d39d7870d5708
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874903"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>讀取適用於 MariaDB 的 Azure 資料庫中的複本

[讀取複本] 功能可讓您將適用於 MariaDB 的 Azure 資料庫伺服器的資料複寫到唯讀伺服器。 您可以從主伺服器複寫到最多五個複本。 複本會使用適用于 mariadb 引擎的二進位記錄檔 (binlog) 以檔案位置為基礎的複寫技術 (具有全域交易識別碼 (GTID)) 以非同步方式更新。 若要深入了解 binlog 複寫，請參閱 [binlog 複寫概觀](https://mariadb.com/kb/en/library/replication-overview/) \(英文\)。

> [!IMPORTANT]
> 您可以在與主伺服器相同的區域中, 或在您選擇的任何其他 Azure 區域中建立讀取複本。 讀取複本 (相同區域和跨區域) 目前為公開預覽狀態。

複本是您所管理的新伺服器, 類似于一般適用於 MariaDB 的 Azure 資料庫伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

若要深入瞭解 GTID 複寫, 請參閱[適用于 mariadb 複寫檔](https://mariadb.com/kb/en/library/gtid/)。

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本

讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

「讀取複本」功能會使用非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

讀取複本可以增強您的嚴重損壞修復計畫。 如果發生區域性嚴重損壞, 而您的主伺服器無法使用, 您可以將工作負載導向另一個區域中的複本。 若要這麼做, 請先讓複本接受使用停止複寫功能的寫入。 然後, 您可以藉由更新連接字串來重新導向應用程式。 若要深入瞭解, 請到[stop replication](#stop-replication)一節。

## <a name="create-a-replica"></a>建立複本

如果主伺服器沒有任何現有的複本伺服器, 則主機會先重新開機以準備進行複寫。

當您啟動建立複本工作流程時, 會建立空白的適用於 MariaDB 的 Azure 資料庫伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

> [!NOTE]
> 如果您沒有在伺服器上設定儲存體警示，建議您這麼做。 警示會在伺服器接近儲存體限制時向您發出通知，因為此狀況會影響複寫。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本

當您建立複本時，其不會繼承主要伺服器的防火牆規則或 VNet 服務端點。 您必須個別針對複本設定這些規則。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用主機名稱和有效的使用者帳戶連接到複本, 如同在一般適用於 MariaDB 的 Azure 資料庫伺服器上所做的一樣。 對於名為**myreplica**且具有管理員使用者名稱**myadmin**的伺服器, 您可以使用 mysql CLI 來連線到複本:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫

適用於 MariaDB 的 Azure 資料庫在 Azure 監視器中提供複寫**延遲 (以秒為單位)** 度量。 此計量僅適用於複本。

此計量是使用適用于 mariadb 的`seconds_behind_master` `SHOW SLAVE STATUS`命令所提供的計量來計算。

設定警示, 以在複寫延遲到達您的工作負載無法接受的值時通知您。

## <a name="stop-replication"></a>停止複寫

您可以停止主要伺服器與複本之間的複寫。 當主要伺服器和讀取複本之間的複寫停止時，複本就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上主要伺服器。

當您選擇停止複寫至複本時, 它會失去先前主要和其他複本的所有連結。 主要及其複本之間沒有自動容錯移轉。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。

## <a name="considerations-and-limitations"></a>考量與限制

### <a name="pricing-tiers"></a>定價層

目前只有「一般用途」與「記憶體最佳化」定價層提供讀取複本。

### <a name="master-server-restart"></a>主要伺服器重新啟動

當您為沒有現有複本的主伺服器建立複本時, 主伺服器會先重新開機, 以準備好進行複寫。 請考慮這一點，並在離峰期間執行這些作業。

### <a name="new-replicas"></a>新複本

讀取複本會建立為新的適用於 MariaDB 的 Azure 資料庫伺服器。 現有伺服器無法設定為複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定

系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後, 您可以從主伺服器獨立變更數個設定: 計算世代、虛擬核心、儲存體、備份保留期限和適用于 mariadb 引擎版本。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

### <a name="stopped-replicas"></a>已停止的複本

如果您停止主伺服器和讀取複本之間的複寫, 已停止的複本會成為可接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器

刪除主要伺服器時，會在所有讀取複本上停止複寫。 這些複本會成為獨立伺服器。 主要伺服器本身會被刪除。

### <a name="user-accounts"></a>使用者帳戶

系統會將主要伺服器上的使用者複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

### <a name="server-parameters"></a>伺服器參數

若要避免資料不同步, 並避免潛在的資料遺失或損毀, 則在使用讀取複本時, 某些伺服器參數會被鎖定而無法更新。

主要和複本伺服器上的下列伺服器參數都會被鎖定:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

已在複本伺服器上鎖定[參數。`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler)

### <a name="other"></a>其他

- 不支援建立複本的複本。
- 記憶體內資料表可能會導致複本不同步。這是適用于 mariadb 複寫技術的限制。
- 請確定主要伺服器資料表有主索引鍵。 缺少主索引鍵可能會造成主體和複本之間產生複寫延遲。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure 入口網站來建立及管理讀取複本](howto-read-replicas-portal.md)
