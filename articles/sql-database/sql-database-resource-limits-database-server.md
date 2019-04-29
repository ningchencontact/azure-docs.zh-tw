---
title: Azure SQL Database 伺服器資源限制 | Microsoft Docs
description: 此文章提供適用於單一資料庫和彈性集區之 Azure SQL Database 伺服器資源限制概觀。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 04a5b98daf94275c6a95503c518248abeaeaeaa6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482035"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL Database 伺服器的 SQL Database 資源限制

此文章針對管理單一資料庫和彈性集區的 SQL Database 伺服器，提供 SQL Database 資源限制的概觀。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。

> [!NOTE]
> 如需受控執行個體限制，請參閱[適用於受控執行個體的 SQL Database 資源限制](sql-database-managed-instance-resource-limits.md)。

## <a name="maximum-resource-limits"></a>資源限制上限

| Resource | 限制 |
| :--- | :--- |
| 每一伺服器的資料庫 | 5000 |
| 任何區域中每個訂用帳戶的伺服器預設數目 | 20 |
| 任何區域中每個訂用帳戶的伺服器最大數目 | 200 |  
| 每一伺服器的 DTU/eDTU 配額 | 54,000 |  
| 每一伺服器/執行個體的 vCore 配額 | 540 |
| 每一伺服器的集區數目上限 | 受限於 DTU 或 vCore 數目。 例如，如果每個集區是 1000 DTU，則伺服器可以支援 54 集區。|
|||

> [!NOTE]
> 若要獲得大於預設數量的 DTU/eDTU 配額、vCore 配額或伺服器，可以在 Azure 入口網站中，針對訂用帳戶使用問題類型「配額」提交新的支援要求。 每一伺服器的 DTU/eDTU 配額和資料庫限制會限制每一部伺服器的彈性集區數目。
> [!IMPORTANT]
> 每當資料庫數量接近每台 SQL Database 伺服器的限制時，可能會出現下列情況：
> - 使用 master 資料庫執行查詢時，延遲狀況增加。  這包含資源使用率統計資料的檢視，例如 sys.resource_stats。
> - 管理作業以及涉及列舉伺服器中資料庫入口網站檢視點的轉譯作業，皆增加延遲狀況。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>達到資料庫資源限制時，會發生什麼事？

### <a name="compute-dtus-and-edtus--vcores"></a>計算 (DTU 和 eDTU / 虛擬核心)

當資料庫計算使用率 (根據 DTU 和 eDTU 或虛擬核心測量) 變高時，查詢延遲會增加，甚至可能逾時。在下列情況下，查詢可能會透過服務排入佇列，並在資源變成可用時，提供資源以供執行。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的計算大小，以提供更多計算資源給資料庫。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 將查詢最佳化，以降低每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>儲存體

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](sql-database-develop-error-messages.md)。 資料庫 SELECTS 與 DELETES 會繼續執行下去。

遇到高空間使用率時，緩和選項包括：

- 提高資料庫或彈性集區的大小上限，或增加更多儲存空間。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 如果資料庫在彈性集區，也可以將資料庫移出集區，如此便不會與其他資料庫共用儲存空間。
- 壓縮資料庫以回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求)

工作階段與背景工作角色的數量上限取決於服務層級和計算大小 (DTU 與 eDTU)。 達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 特別是在尖峰負載期間，資料庫資源達到上限，背景工作角色也因為長時間執行查詢而不斷累積。

當工作階段或背景工作角色出現高使用率時，緩和選項包括：

- 提高資料庫或彈性集區的服務層級或計算大小。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="transaction-log-rate-governance"></a>事务日志速率调控 
事务日志速率调控是 Azure SQL 数据库中的一个进程，用于限制批量插入、SELECT INTO 和索引生成等工作负荷的高引入速率。 无论针对数据文件发出多少 IO，系统都会对日志记录生成速率跟踪并实施这些限制，使其保持在亚秒级以下，并限制吞吐量。  交易記錄檔產生速率目前以線性方式調整最多有硬體相依的點，最大記錄檔與速率會允許正在 96 MB/秒的購買模型的 vCore。 

> [!NOTE]
> 向事务日志文件发出的实际物理 IO 不会受到调控或限制。 

日志速率的设置应该做到可在各种场合下实现并保持该速率，同时，整个系统可以在尽量减轻对用户负载造成的影响的前提下保持其功能。 日志速率调控可确保事务日志备份保留在已发布的可恢复性 SLA 范围内。  这种调控还可以防止次要副本带来过多的积压工作。

生成日志记录后，将评估每个操作，以确定是否要将其延迟，从而保持最大所需日志速率（MB/秒）。 将日志记录刷新到存储时不会增大延迟，日志速率调控是在日志速率生成期间应用的。

在运行时实施的实际日志生成速率还可能受到反馈机制（暂时降低允许的日志速率，使系统保持稳定）的影响。 日志文件空间管理可避免遇到日志空间不间的情况，可用性组复制机制可以暂时降低总体系统限制。 

可通过以下 wait 类型（在 [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV 中公开）查看日志速率调控器流量的形状：

| Wait 类型 | 注意 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 数据库限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 实例级限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 反馈控制。高级/业务关键型工作负荷中的可用性组物理复制不会保持 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 反馈控制。限制速率可以避免出现日志空间不足的情况 |
|||

当日志速率限制阻碍实现所需的可伸缩性时，请考虑以下选项：
- 若要取得最大的 96 MB/秒記錄速率相應增加較大的層。 
- 如果加载的数据是暂时性的（即 ETL 过程中的暂存数据），可将其载入 tempdb（记录最少量的数据）。 
- 对于分析方案，可将数据载入聚集列存储涵盖的表中。 这样，可以通过压缩来降低所需的日志速率。 此方法确实会增大 CPU 利用率，并且仅适用于可从聚集列存储索引受益的数据集。 

## <a name="next-steps"></a>後續步驟

- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 如需 tempdb 大小限制的相關資訊，請參閱 [Azure SQL Database 中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
