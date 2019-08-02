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
ms.date: 04/18/2019
ms.openlocfilehash: 175f694cbe46f871349136c9ce91888b6de48d21
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566861"
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

### <a name="storage-size"></a>儲存體大小
- 針對單一資料庫 rources, 請參閱以[DTU 為基礎的資源限制](sql-database-dtu-resource-limits-single-databases.md)或以[vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md), 以瞭解每個定價層的儲存體大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>達到資料庫資源限制時，會發生什麼事？

### <a name="compute-dtus-and-edtus--vcores"></a>計算 (DTU 和 eDTU / 虛擬核心)

當資料庫計算使用率 (根據 DTU 和 eDTU 或虛擬核心測量) 變高時，查詢延遲會增加，甚至可能逾時。在下列情況下，查詢可能會透過服務排入佇列，並在資源變成可用時，提供資源以供執行。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的計算大小，以提供更多計算資源給資料庫。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 將查詢最佳化，以降低每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存放區

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

## <a name="transaction-log-rate-governance"></a>交易記錄速率治理 
交易記錄速率治理是 Azure SQL Database 中的程式, 用來限制大量插入、選取 INTO 和索引組建等工作負載的高內嵌速率。 這些限制會在子秒層級追蹤並強制執行, 以產生記錄檔記錄的速率, 而不論可能針對資料檔案發出多少 Io, 都能限制輸送量。  交易記錄產生速率目前已線性相應增加至與硬體相依的點, 且最大記錄速率允許使用 vCore 購買模型 96 MB/s。 

> [!NOTE]
> 實際的實體 IOs 到交易記錄檔不受管理或限制。 

記錄速率的設定可讓您在各種情況下達成和持續, 而整體系統可以維持其功能, 並將對使用者負載的影響降至最低。 記錄速率治理可確保交易記錄備份保留在已發佈的復原能力 Sla 內。  這種治理也會防止次要複本上有過多的待處理專案。

產生記錄檔記錄時, 系統會評估每個作業並評估是否應延遲, 以維持最大所需的記錄速率 (每秒 MB/秒)。 當記錄檔記錄排清至儲存體時, 不會新增延遲, 而是在產生記錄速率時套用記錄速率治理。

在執行時間加諸的實際記錄產生速率可能也會受到意見反應機制的影響, 暫時減少允許的記錄檔速率, 讓系統能夠穩定。 記錄檔空間管理, 避免遇到記錄空間不足的情況, 以及可用性群組複寫機制, 可以暫時降低整體系統限制。 

記錄速率管理員流量成形是透過下列等候類型 (在[_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV 中公開) 來呈現:

| 等候類型 | 注意 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 資料庫限制 |
| POOL_LOG_RATE_GOVERNOR | 集區限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 實例層級限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 意見反應控制, Premium/Business Critical 中的可用性群組實體複寫未趕上 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 意見反應控制, 限制速率以避免發生記錄空間不足的狀況 |
|||

當遇到阻礙所需擴充性的記錄速率限制時, 請考慮下列選項:
- 相應增加至較大的層級, 以取得最大 96 MB/秒的記錄速率。 
- 如果載入的資料是暫時性的, 也就是在 ETL 進程中暫存資料, 則可以將其載入至 tempdb (這是最低限度記錄)。 
- 針對分析案例, 載入叢集資料行存放區涵蓋的資料表。 這會減少因為壓縮而需要的記錄速率。 這項技術會增加 CPU 使用率, 而且僅適用于從叢集資料行存放區索引獲益的資料集。 

## <a name="next-steps"></a>後續步驟

- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 如需 tempdb 大小限制的相關資訊，請參閱 [Azure SQL Database 中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
