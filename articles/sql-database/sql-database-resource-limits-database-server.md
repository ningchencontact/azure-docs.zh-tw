---
title: Azure SQL Database 伺服器資源限制 | Microsoft Docs
description: 此文章提供適用於單一資料庫和彈性集區之 Azure SQL Database 伺服器資源限制概觀。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 4e4c0a6cd25587b33c06526b57e6acdbebb69c8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445636"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL Database 伺服器的 SQL Database 資源限制

此文章針對管理單一資料庫和彈性集區的 SQL Database 伺服器，提供 SQL Database 資源限制的概觀。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。

> [!NOTE]
> 如需受控執行個體限制，請參閱[適用於受控執行個體的 SQL Database 資源限制](sql-database-managed-instance-resource-limits.md)。

## <a name="maximum-resource-limits"></a>資源限制上限

| 資源 | 限制 |
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

### <a name="storage-size"></a>儲存體大小
- 單一資料庫的 rources 請來參考[以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits-single-databases.md)或[vCore 架構的資源限制](sql-database-vcore-resource-limits-single-databases.md)的儲存體大小限制，每個定價層。

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

## <a name="transaction-log-rate-governance"></a>交易記錄檔的速率控管 
用來限制這類大量工作負載高的擷取速率的 Azure SQL Database 中的程序插入，SELECT INTO 和索引建立交易記錄檔的速率控管。 追蹤和記錄檔記錄產生率的子第二個層級強制執行這些限制，可能會對資料檔案發出限制的輸送量，不論多少 IOs。  交易記錄檔產生速率目前以線性方式調整最多有硬體相依的點，最大記錄檔與速率會允許正在 96 MB/秒的購買模型的 vCore。 

> [!NOTE]
> 交易記錄檔的實際實體 Io 不會控管或限制。 

設定記錄檔的速率的方式，可以達成並持續以各種不同的案例中，而整體系統可以維護其功能，且使用者負載的影響降到最低。 記錄檔的速率控管會確保備份維持在已發行的復原能力 Sla 內，交易記錄檔。  此控管也可避免在次要複本上的過多待處理項目。

產生記錄檔記錄時，每個作業評估，並評估是否它應該會延遲，以維持所需的記錄檔最大速率 （MB/秒的秒）。 延遲不會加入記錄檔記錄已排清至儲存體，而不是在記錄檔的速率產生本身時套用記錄速率控管。

實際的記錄檔產生在執行階段加諸的速率可能也會受到意見反應機制，讓系統可以穩定，暫時降低允許的記錄檔的速率。 記錄檔空間管理，避免用盡到記錄檔空間的情況與可用性群組的複寫機制可能暫時降低整體系統限制。 

記錄速率管理員的流量成形透過下列等候類型的方式來呈現 (中公開[sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| 等候類型 | 注意 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 資料庫限制 |
| POOL_LOG_RATE_GOVERNOR | 集區限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 執行個體層級限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 意見反應控制項，Premium/業務關鍵 」 中無法配合可用性群組實體複寫 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 意見反應控制項，限制以避免記錄空間條件的費率 |
|||

當遇到會阻礙所需的延展性記錄速率限制，請考慮下列選項：
- 若要取得最大的 96 MB/秒記錄速率相應增加較大的層。 
- 如果正在載入的資料是暫時性的也就接移資料在 ETL 程序，它可以載入 tempdb （這最低限度記錄）。 
- 分析的情況下，載入至涵蓋叢集資料行存放區資料表。 這會減少必要的記錄速率，因為壓縮的關係。 這項技術會增加 CPU 使用量，而且也只適用於受益於叢集資料行存放區索引的資料集。 

## <a name="next-steps"></a>後續步驟

- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 如需 tempdb 大小限制的相關資訊，請參閱 [Azure SQL Database 中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
