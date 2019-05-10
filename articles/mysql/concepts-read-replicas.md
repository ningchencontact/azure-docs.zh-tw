---
title: 「適用於 MySQL 的 Azure 資料庫」中的讀取複本。
description: 此文將說明「適用於 MySQL 的 Azure 資料庫」的讀取複本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/30/2019
ms.openlocfilehash: be592cb6bb7c041fab0a2f96a338f4f4bb0ff00a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510932"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>讀取「適用於 MySQL 的 Azure 資料庫」中的複本

讀取的複本 」 功能可讓您從 Azure Database for MySQL 伺服器的資料複寫到唯讀模式的伺服器。 您可以從主要伺服器複寫到最多五個複本。 使用 MySQL 引擎的原生二進位記錄檔 (binlog) 檔案的位置為基礎的複寫技術，以非同步方式更新複本。 若要深入了解 binlog 複寫，請參閱 [MySQL binlog 複寫概觀](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) \(英文\)。

> [!IMPORTANT]
> 在與您的主要伺服器相同的區域，或您選擇的任何其他 Azure 區域中，您可以建立一個讀取的複本。 跨區域複寫目前為公開預覽狀態。

複本會將您管理類似於一般的 Azure Database for MySQL 伺服器的新伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

若要深入了解 MySQL 複寫功能與問題，請參閱 [MySQL 複寫文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) \(英文\)。

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本

讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

讀取的複本 」 功能會使用 MySQL 非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

讀取複本，可以提升您的災害復原計劃。 如果區域性災害，且您的主要伺服器無法使用，您可以將您的工作負載，以另一個區域中的複本。 若要這樣做，首先讓使用停止複寫函式接受寫入的複本。 然後可以將您的應用程式重新導向藉由更新連接字串。 進一步了解[停止複寫](#stop-replication)一節。

## <a name="create-a-replica"></a>建立複本

如果主要伺服器會不有任何現有的複本伺服器，主要會先重新啟動準備本身進行複寫。

當您開始建立複本工作流程時，會建立空白的 Azure Database for MySQL 伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

> [!NOTE]
> 如果您沒有在伺服器上設定儲存體警示，建議您這麼做。 警示會在伺服器接近儲存體限制時向您發出通知，因為此狀況會影響複寫。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本

當您建立複本時，其不會繼承主要伺服器的防火牆規則或 VNet 服務端點。 您必須個別針對複本設定這些規則。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以連接到複本使用其主機名稱和有效的使用者帳戶，如同一般的 Azure Database for MySQL 伺服器上。 針對名為伺服器**myreplica**系統管理員使用者名稱**myadmin**，您可以使用 mysql CLI 連接到複本：

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫

適用於 MySQL 的 azure 資料庫提供**複寫延遲，以秒為單位**中 Azure 監視器計量。 此計量僅適用於複本。

此計量會使用計算`seconds_behind_master`度量用於 MySQL 的`SHOW SLAVE STATUS`命令。

設定警示通知您，當複寫延遲達到的值不接受您的工作負載。

## <a name="stop-replication"></a>停止複寫

您可以停止主要伺服器與複本之間的複寫。 當主要伺服器和讀取複本之間的複寫停止時，複本就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上主要伺服器。

當您選擇停止複寫至複本時，就會失去其先前的主要和其他複本的所有連結。 沒有任何 master 與其複本之間的自動容錯移轉。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。

## <a name="considerations-and-limitations"></a>考量與限制

### <a name="pricing-tiers"></a>定價層

目前只有「一般用途」與「記憶體最佳化」定價層提供讀取複本。

### <a name="master-server-restart"></a>主要伺服器重新啟動

如果为没有现有副本的主服务器创建副本，主服务器将首先重启以便为复制准备自身。 請考慮這一點，並在離峰期間執行這些作業。

### <a name="new-replicas"></a>新複本

讀取的複本建立為新的 Azure Database for MySQL 伺服器。 現有伺服器無法設定為複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定

系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後，數個設定可以變更單獨從主要伺服器： 計算產生，Vcore、 儲存體、 備份保留期限和 MySQL 引擎版本。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

### <a name="stopped-replicas"></a>已停止的複本

如果您停止讀取的複本為主要伺服器之間複寫時，已停止的複本就會變成獨立伺服器可接受讀取和寫入。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器

刪除主要伺服器時，會在所有讀取複本上停止複寫。 這些複本會成為獨立伺服器。 主要伺服器本身會被刪除。

### <a name="user-accounts"></a>使用者帳戶

系統會將主要伺服器上的使用者複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

### <a name="server-parameters"></a>伺服器參數

若要防止資料變得不同步，並避免潛在資料遺失或損毀，某些伺服器參數都會被鎖定時使用讀取複本更新。

下列的 server 參數是在主要和複本伺服器上鎖定：
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

[ `event_scheduler` ](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)參數已被鎖定在複本伺服器上。 

### <a name="other"></a>其他

- 不支援全域交易識別碼 (GTID)。
- 不支援建立複本的複本。
- 記憶體內資料表可能會導致複本不同步。這是 MySQL 複寫技術的限制。 如需詳細資訊，請參閱 [MySQL 參考文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) \(英文\)。
- 請確定主要伺服器資料表有主索引鍵。 缺少主索引鍵可能會造成主體和複本之間產生複寫延遲。
- 檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) \(英文\) 中的完整 MySQL 複寫限制清單

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure 入口網站來建立及管理讀取複本](howto-read-replicas-portal.md)
- 了解如何[使用 Azure CLI 來建立及管理讀取複本](howto-read-replicas-cli.md)
