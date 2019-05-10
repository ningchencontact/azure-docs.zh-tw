---
title: 從備份還原 Azure SQL 資料庫 | Microsoft Docs
description: 了解還原時間點，其可讓您復原 Azure SQL Database 到先前的時間 (最多 35 天)。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 47bf59adb33f3685b31430c652b31880d383833e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232655"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>使用自動資料庫備份復原 Azure SQL 資料庫

根據預設，SQL Database 備份會儲存在地理複寫的 Blob 儲存體 (RA-GRS) 中。 下列選項可供使用[自動資料庫備份](sql-database-automated-backups.md)進行的資料庫復原使用：

- 在同一部 SQL Database 伺服器上建立新的資料庫，並復原到保留期限內的指定時間點。
- 在同一部 SQL Database 伺服器上建立資料庫，並復原到已刪除資料庫的刪除時間。
- 在相同區域中的任何 SQL Database 伺服器上建立新的資料庫，並復原到最新的備份點。
- 在任何其他區域中的任何 SQL Database 伺服器上建立新的資料庫，並復原到最新複寫的備份點。

如果您設定[備份長期保留](sql-database-long-term-retention.md)，您也可以從任何 SQL Database 伺服器上任何 LTR 備份建立新的資料庫。

> [!IMPORTANT]
> 在還原期間，您無法覆寫現有的資料庫。

使用標準或進階服務層級時，還原的資料庫會在下列情況產生額外的儲存體成本：

- 如果資料庫大小上限大於 500 GB，會將 P11–P15 還原至 S4-S12 或 P1–P6。
- 如果資料庫大小上限大於 250 GB，會將 P1–P6 還原至 S4-S12。

額外的成本是 icurred 時還原之資料庫的大小上限大於包含目標資料庫的服務層和效能層級的儲存體數量。 額外的儲存體佈建超過包含的數量會產生額外費用。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含儲存空間量，您可以避免此額外成本的資料庫大小上限設為包含的數量。

> [!NOTE]
> 當您建立[資料庫複本](sql-database-copy.md)時，會使用[自動資料庫備份](sql-database-automated-backups.md)。

## <a name="recovery-time"></a>復原時間

使用自動資料庫備份還原資料庫的復原時間會受到一些因素所影響：

- 資料庫的大小
- 資料庫的計算大小
- 相關的交易記錄數目
- 需要重新執行以復原到還原點的活動數目
- 還原到不同區域的網路頻寬
- 要在目標區域中處理的並行還原要求數目

針對大型及/或非常活躍的資料庫，還原可能需要數小時的時間。 如果某區域中的中斷延長，其他區域可能要處理大量的異地還原要求。 如果有許多要求，則該區域中資料庫的復原時間可能會增加。 大多數資料庫還原都會在 12 小時內完成。

單一訂用帳戶中，有限制的並行還原要求數目。  這些限制的時間還原、 異地還原和還原點的任意組合從長期保留備份）：

| | **正在處理的並行要求的最大數目** | **正在提交的並行要求的最大數目** |
| :--- | --: | --: |
|單一資料庫 (每個訂閱)|10|60|
|彈性集區 (每個集區)|4|200|
||||

目前沒有內建方法來還原整部伺服器。 [Azure SQL Database:完整伺服器復原](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)指令碼是如何完成這項工作的範例。

> [!IMPORTANT]
> 若要使用自動備份復原，您必須是訂用帳戶中 SQL Server 參與者角色的成員或訂用帳戶擁有者 - 請參閱 [RBAC：內建角色](../role-based-access-control/built-in-roles.md)。 您可以使用 Azure 入口網站、PowerShell 或 REST API 來進行復原。 您無法使用 Transact-SQL。

## <a name="point-in-time-restore"></a>還原時間點

您可以還原獨立、 集區，或執行個體中使用 Azure 入口網站中，時間的較早時間點的資料庫[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)，或有[REST API](https://docs.microsoft.com/rest/api/sql/databases)。 要求可以指定任何服務層，或計算已還原的資料庫的大小。 請確定在您要還原資料庫的目標伺服器上具有足夠的資源。 完成後，就會建立新的資料庫與原始資料庫相同的伺服器上。 還原的資料庫將會收費根據其服務層和計算大小的一般費率計費。 在完成資料庫還原之前，不會產生任何費用。

基於復原目的，您通常會將資料庫還原到較早的時間點。 您可以還原的資料庫視為原始資料庫的替代品，或使用它做為來源資料來更新原始的資料庫。

- **資料庫取代**

  如果還原的資料庫要做為原始資料庫的替代品，您應該指定 orinal 資料庫的計算大小和服務層級。 您可以重新命名原始資料庫並讓原來的名稱使用的已還原的資料庫[ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL 命令。

- **資料復原**

  如果您打算從還原從使用者或應用程式錯誤中復原的資料庫擷取資料時，您要撰寫及執行資料復原指令碼會從還原的資料庫中擷取資料，並套用到原始資料庫。 雖然還原作業可能要花很長的時間才能完成，但還原中的資料庫在整個還原過程中都會顯示在資料庫清單上。 如果您在還原期間刪除資料庫，還原作業將會取消，且您不需要支付未完成還原的資料庫。

若要使用 Azure 入口網站來將單一、集區或執行個體資料庫復原至某個時間點，請開啟資料庫頁面，然後按一下工具列上的 [還原]。

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> 若要以程式設計方式從備份還原資料庫，請參閱[使用自動備份以程式設計方式執行復原](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>還原已刪除的資料庫

您可以刪除時間或較早時間點還原已刪除的資料庫，在使用 Azure 入口網站中，相同的 SQL Database 伺服器上的時間內[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)，或有[REST (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。 您可以[使用 PowerShell 在受控執行個體上還原已刪除的資料庫](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance)。 

> [!TIP]
> 如需示範如何還原已刪除資料庫的 PowerShell 指令碼範例，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。
> [!IMPORTANT]
> 如果您刪除 Azure SQL Database 伺服器執行個體，其所有資料庫也會一併刪除且無法加以復原。 目前不支援還原已刪除的伺服器。

### <a name="deleted-database-restore-using-the-azure-portal"></a>使用 Azure 入口網站還原已刪除的資料庫

若要復原已刪除的資料庫，使用 Azure 入口網站中，開啟您的伺服器，並在 [作業] 區域中的頁面中，按一下**已刪除的資料庫**。

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> 若要以程式設計方式從備份還原已刪除的資料庫，請參閱[使用自動備份以程式設計方式執行復原](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>異地還原

您可以從最新的異地複寫備份，在任何 Azure 區域中的任何伺服器上還原 SQL Database。 異地還原使用異地備援備份作為其來源。 它可以在即使資料庫或資料中心是因為中斷而無法存取要求。

當您的資料庫因裝載區域中的事件而無法使用異地還原會是預設的復原選項。 您可以將資料庫還原到任何其他區域中的伺服器。 在建立備份時以及對它進行異地複寫到不同區域中的 Azure Blob 之間會有延遲。 如此一來，已還原的資料庫可以是一小時的時間落後於原始資料庫。 下圖顯示從另一個區域中的上次可用備份來還原資料庫。

![異地還原](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> 如需示範如何執行異地還原的 PowerShell 指令碼範例，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。

目前不支援異地次要資料庫上的時間點復原。 只有在主要資料庫上才可進行時間點復原。 如需使用異地還原來從中斷復原的詳細資訊，請參閱[從中斷復原](sql-database-disaster-recovery.md)。

> [!IMPORTANT]
> 異地還原是最基本的災害復原解決方案-SQL Database 中可用。 它會依賴自動建立異地複寫備份的 RPO = 1 小時和 12 小時的預估的復原時間。 它並不保證目標區域，必須在區域 ourage 之後還原資料庫，因為有可能會有清晰的增加需求的容量。 對於非商務關鍵應用程式使用相對較小的資料庫，異地還原會是適當的災害復原解決方案。 Busniess 重要應用程式，使用大型資料庫，且必須確保商務持續性，您應該使用[自動容錯移轉群組](sql-database-auto-failover-group.md)。 它提供較低的 RPO 和 RTO，，而且一律保證容量。 如需商務持續性選項的詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

### <a name="geo-restore-using-the-azure-portal"></a>使用 Azure 入口網站進行異地還原

若要使用 Azure 入口網站異地還原處於其[以 DTU 為基礎的模型保留期限](sql-database-service-tiers-dtu.md)或[以虛擬核心為基礎的模型保留期限](sql-database-service-tiers-vcore.md)的資料庫，請開啟 [SQL Database] 頁面，然後按一下 [新增]。 在 [選取來源] 文字方塊中，選取 [備份]。 指定要在區域和您選擇的伺服器上，執行復原的來源備份。

> [!Note]
> 使用 Azure 入口網站進行異地還原不適用於受控執行個體。 請改用 PowerShell。

## <a name="programmatically-performing-recovery-using-automated-backups"></a>使用自動備份以程式設計方式執行復原

如先前所述，除了 Azure 入口網站之外，還可使用 Azure PowerShell 或 REST API，以程式設計方式執行資料庫復原。 下表描述可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

- 若要還原的獨立或集區的資料庫，請參閱[還原 AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |取得一或多個資料庫。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 取得可還原的已刪除資料庫。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |取得資料庫的異地備援備份。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |還原 SQL Database。 |

  > [!TIP]
  > 如需示範如何執行資料庫還原時間點的 PowerShell 指令碼範例，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。

- 若要將受控執行個體的資料庫還原，請參閱[還原 AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |取得一或多個受管理的執行個體。 |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 取得執行個體的資料庫。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |將執行個體的資料庫還原。 |

### <a name="rest-api"></a>REST API

使用 REST API 來還原單一或集區資料庫：

| API | 描述 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |還原資料庫 |
| [取得建立或更新資料庫狀態](https://docs.microsoft.com/rest/api/sql/operations) |在還原作業期間傳回狀態 |

### <a name="azure-cli"></a>Azure CLI

- 若要使用 Azure CLI 來還原單一或集區資料庫，請參閱 [az sql db restore](/cli/azure/sql/db#az-sql-db-restore) \(英文\)。
- 若要還原的受管理的執行個體，使用 Azure CLI，請參閱[az sql midb 還原](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>總結

自動備份可在發生使用者和應用程式錯誤、意外刪除資料庫和長時間中斷時保護您的資料庫。 所有服務層級和計算大小都可以取得此內建功能。

## <a name="next-steps"></a>後續步驟

- 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
- 若要深入了解長期保留，請參閱[長期保留](sql-database-long-term-retention.md)。
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-active-geo-replication.md)或[自動容錯移轉群組](sql-database-auto-failover-group.md)。
