---
title: 從備份還原 Azure SQL Database | Microsoft Docs
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
ms.date: 03/12/2019
ms.openlocfilehash: ca54ae11390b388c3158bd220ee5c7829172a5c3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620473"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>使用自動資料庫備份復原 Azure SQL Database

根據預設，SQL Database 備份會儲存在地理複寫的 Blob 儲存體 (RA-GRS) 中。 下列選項可供使用[自動資料庫備份](sql-database-automated-backups.md)進行的資料庫復原使用：

- 在同一部 SQL Database 伺服器上建立新的資料庫，並復原到保留期限內的指定時間點。
- 在同一部 SQL Database 伺服器上建立資料庫，並復原到已刪除資料庫的刪除時間。
- 在相同區域中的任何 SQL Database 伺服器上建立新的資料庫，並復原到最新的備份點。
- 在任何其他區域中的任何 SQL Database 伺服器上建立新的資料庫，並復原到最新複寫的備份點。

如果您已設定[備份長期保留](sql-database-long-term-retention.md)，您也可以從任何區域中之任何 SQL Database 伺服器上的任何 LTR 備份建立新的資料庫。

> [!IMPORTANT]
> 在還原期間，您無法覆寫現有的資料庫。

使用標準或進階服務層時，還原的資料庫會在下列情況產生額外的儲存體成本：

- 如果資料庫大小上限大於 500 GB，會將 P11–P15 還原至 S4-S12 或 P1–P6。
- 如果資料庫大小上限大於 250 GB，會將 P1–P6 還原至 S4-S12。

額外成本是因為還原的資料庫大小上限大於該計算大小所含的儲存體數量，而佈建的任何額外儲存體若大於內含量則會產生額外費用。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。

> [!NOTE]
> 當您建立[資料庫複本](sql-database-copy.md)時，會使用[自動資料庫備份](sql-database-automated-backups.md)。

## <a name="recovery-time"></a>復原時間

使用自動資料庫備份還原資料庫的復原時間會受到一些因素所影響：

- 資料庫的大小
- 資料庫的計算大小
- 相關的交易記錄檔數目
- 需要重新執行以復原到還原點的活動數目
- 還原到不同區域的網路頻寬
- 要在目標區域中處理的並行還原要求數目

針對非常大型及/或作用中的資料庫，還原可能需要數小時。 如果某區域中的中斷延長，其他區域可能要處理大量的異地還原要求。 如果有許多要求，則該區域中資料庫的復原時間可能會增加。 大多數資料庫還原都會在 12 小時內完成。

針對單一訂用帳戶，有關正在提交及處理的並行還原要求 (包含時間點還原、異地還原與自長期保留備份還原) 數目有一些限制：

| | **正在處理的並行要求的最大數目** | **正在提交的並行要求的最大數目** |
| :--- | --: | --: |
|單一資料庫 (每個訂閱)|10|60|
|彈性集區 (每個集區)|4|200|
||||

沒有可執行大量還原的內建功能。 [Azure SQL Database:Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) 指令碼是其中一種完成這項工作的範例。

> [!IMPORTANT]
> 若要使用自動備份復原，您必須是訂用帳戶中 SQL Server 參與者角色的成員或訂用帳戶擁有者 - 請參閱 [RBAC：內建角色](../role-based-access-control/built-in-roles.md)。 您可以使用 Azure 入口網站、PowerShell 或 REST API 來進行復原。 您無法使用 Transact-SQL。

## <a name="point-in-time-restore"></a>還原時間點

您可以使用 Azure 入口網站、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases)，將獨立、集區或執行個體資料庫還原到先前的時間點，以作為相同伺服器上的新資料庫。 資料庫可以還原至任何服務層或計算大小。 請確定在您要還原資料庫的目標伺服器上具有足夠的資源。 完成之後，還原的資料庫會是一般、完全可供存取的線上資料庫。 還原的資料庫會根據其服務層和計算大小依一般費率計費。 在完成資料庫還原之前，不會產生任何費用。

基於復原目的，您通常會將資料庫還原到較早的時間點。 當您執行此動作時，可以將還原的資料庫視為原始資料庫的替代品，或用它來從原始資料庫擷取資料並將其更新。

- **資料庫取代**

  如果要使用已還原的資料庫作為原始資料庫的替代品，您應該確認計算大小和/或服務層是適當的，並在必要時調整資料庫大小。 您可以重新命名原始資料庫，然後使用 T-SQL 中的 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) 命令提供原始名稱給還原的資料庫。

- **資料復原**

  如果您打算從還原的資料庫擷取資料，以便從使用者或應用程式錯誤中復原，您就必須撰寫並執行所需的資料復原指令碼，以從還原的資料庫中擷取資料到原始資料庫。 雖然還原作業可能要花很長的時間才能完成，但還原中的資料庫在整個還原過程中都會顯示在資料庫清單上。 如果您在還原期間刪除該資料庫，系統便會取消還原作業，而且不會針對未完成還原的資料庫向您收費。

若要使用 Azure 入口網站來將單一、集區或執行個體資料庫復原至某個時間點，請開啟資料庫頁面，然後按一下工具列上的 [還原]。

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> 若要以程式設計方式從備份還原資料庫，請參閱[使用自動備份以程式設計方式執行復原](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>還原已刪除的資料庫

您可以使用 Azure 入口網站、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) 或 [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)，將已刪除的資料庫還原到相同 SQL Database 伺服器上已刪除資料庫的刪除時間。 您可以[使用 PowerShell 在受控執行個體上還原已刪除的資料庫](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance)。 您可以使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)，將已刪除的資料庫還原到保留期間內的較早時間點。

> [!TIP]
> 如需示範如何還原已刪除資料庫的 PowerShell 指令碼範例，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。
> [!IMPORTANT]
> 如果您刪除 Azure SQL Database 伺服器執行個體，其所有資料庫也會一併刪除且無法加以復原。 目前不支援還原已刪除的伺服器。

### <a name="deleted-database-restore-using-the-azure-portal"></a>使用 Azure 入口網站還原已刪除的資料庫

若要使用 Azure 入口網站來復原處於 [DTU 形式的模型保留期間](sql-database-service-tiers-dtu.md)或 [V 核心形式的模型保留期間](sql-database-service-tiers-vcore.md)的已刪除資料庫，請開啟伺服器頁面，然後在 [作業] 區域中按一下 [已刪除的資料庫]。

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> 若要以程式設計方式從備份還原已刪除的資料庫，請參閱[使用自動備份以程式設計方式執行復原](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>異地還原

您可以從最新的異地複寫備份，在任何 Azure 區域中的任何伺服器上還原 SQL Database。 異地還原使用異地備援備份做為其來源，即使因為中斷而無法存取資料庫或資料中心，也能用來復原資料庫。

當您的資料庫因為裝載資料庫區域中的事件而無法使用時，異地還原就是預設的復原選項。 如果區域中的大規模意外導致您無法使用資料庫應用程式，則您可以從異地複寫備份，將資料庫還原到任何其他區域中的伺服器。 在建立備份時以及對它進行異地複寫到不同區域中的 Azure Blob 之間會有延遲。 此延遲可能最長達一小時，因此當發生災害時，最多可能會遺失最長達一小時的資料。 下圖顯示從另一個區域中的上次可用備份來還原資料庫。

![異地還原](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> 如需示範如何執行異地還原的 PowerShell 指令碼範例，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。

目前不支援異地次要資料庫上的時間點復原。 只有在主要資料庫上才可進行時間點復原。 如需使用異地還原來從中斷復原的詳細資訊，請參閱[從中斷復原](sql-database-disaster-recovery.md)。

> [!IMPORTANT]
> 從備份復原是 SQL Database 中最基本的災害復原解決方案，具備最長的復原點目標 (RPO) 和預估復原時間 (ERT)。 對於使用小型資料庫 (例如，基本服務層或彈性集區中的小型租用戶資料庫) 的解決方案，異地還原是常見且合理的 DR 解決方案，且其 ERT 最多為 12 小時 (通常少於 12 小時)。 針對使用大型資料庫且需要較短復原時間的解決方案，您應該考慮使用[主動式異地複寫](sql-database-active-geo-replication.md)或[自動容錯移轉群組](sql-database-auto-failover-group.md)。 主動式異地複寫提供明顯較低的 RPO 和 ERT，因為它只需要您起始對連續複寫次要資料庫的容錯移轉。 自動容錯移轉群組為一組資料庫啟用自動故障轉移。 如需商務持續性選項的詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

### <a name="geo-restore-using-the-azure-portal"></a>使用 Azure 入口網站進行異地還原

若要使用 Azure 入口網站異地還原處於其[以 DTU 為基礎的模型保留期限](sql-database-service-tiers-dtu.md)或[以虛擬核心為基礎的模型保留期限](sql-database-service-tiers-vcore.md)的資料庫，請開啟 [SQL Database] 頁面，然後按一下 [新增]。 在 [選取來源] 文字方塊中，選取 [備份]。 指定要在區域和您選擇的伺服器上，執行復原的來源備份。

> [!Note]
> 使用 Azure 入口網站進行異地還原不適用於受控執行個體。 請改用 PowerShell。

## <a name="programmatically-performing-recovery-using-automated-backups"></a>使用自動備份以程式設計方式執行復原

如先前所述，除了 Azure 入口網站之外，還可使用 Azure PowerShell 或 REST API，以程式設計方式執行資料庫復原。 下表描述可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database，仍然支援 PowerShell 的 Azure Resource Manager 模組，但所有未來的開發是 Az.Sql 模組。 這些指令程式，請參閱 < [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 在 Az 模組和 AzureRm 模組中命令的引數是本質上相同的。

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

自動備份可在發生使用者和應用程式錯誤、意外刪除資料庫和長時間中斷時保護您的資料庫。 所有服務層和計算大小都可以取得此內建功能。

## <a name="next-steps"></a>後續步驟

- 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
- 若要深入了解長期保留，請參閱[長期保留](sql-database-long-term-retention.md)。
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-active-geo-replication.md)或[自動容錯移轉群組](sql-database-auto-failover-group.md)。
