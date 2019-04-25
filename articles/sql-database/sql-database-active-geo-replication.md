---
title: 作用中異地複寫 - Azure SQL Database | Microsoft Docs
description: 使用作用中異地複寫，在相同或不同資料中心 (區域) 中建立個別資料庫之可讀取的次要資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: ca53f4bfa80d6fdead24dc7d562c2240bb3fa86d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387418"
---
# <a name="creating-and-using-active-geo-replication"></a>创建并使用活动异地复制

作用中異地複寫是 Azure SQL Database 的功能，可讓您在相同或不同資料中心 (區域) 中的 SQL Database 伺服器上建立個別資料庫的可讀取次要資料庫。

> [!NOTE]
> 受控執行個體不支援作用中異地複寫。 針對受控執行個體的異地容錯移轉，請使用[自動容錯移轉群組](sql-database-auto-failover-group.md)。

作用中異地複寫是設計為商務持續性解決方案，可在發生區域災害或大規模中斷時，讓應用程式執行個別資料庫的快速災害復原。 如果已啟用異地複寫，則應用程式可以在不同的 Azure 區域中起始容錯移轉至次要資料庫。 在相同或不同的區域中，最多可支援四個次要資料庫，次要資料庫也可以用於唯讀存取查詢。 容錯移轉必須由應用程式或使用者手動起始。 容錯移轉之後，新的主要資料庫會有不同的連接端點。 下圖說明使用作用中異地複寫之異地備援雲端應用程式的一般設定。

![作用中異地複寫](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database 也支援自動容錯移轉群組。 如需詳細資訊，請參閱使用[自動容錯移轉群組](sql-database-auto-failover-group.md)。 此外，在受控執行個體中建立的資料庫不支援作用中異地複寫。 請考慮使用[容錯移轉叢集](sql-database-auto-failover-group.md)搭配受控執行個體。

若您的主要資料庫因為任何原因而失敗，或只需要離線，您可以起始容錯移轉至任何次要資料庫。 容錯移轉至其中一個次要資料庫啟動時，所有其他次要複本會自動連結至新的主要複本。

您可以使用主動式異地複寫管理伺服器上或彈性集區中個別資料庫或一組資料庫的複寫和容錯移轉。 若要進行相關作業，您可以使用：

- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell：單一資料庫](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell：彈性集區](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL：單一資料庫或彈性集區](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API：單一資料庫](https://docs.microsoft.com/rest/api/sql/replicationlinks)

容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。

主動式異地複寫會利用 SQL Server 的 [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 技術，使用快照隔離，以非同步方式將主要資料庫上認可的交易複寫到次要資料庫。 自動容錯移轉群組在主動式異地複寫之上提供群組語意，但使用相同的非同步複寫機制。 雖然次要資料可能會在任何指定時間點稍微落後主要資料庫，但是次要資料保證絕對不會含有部分交易。 跨區域備援可讓應用程式從天然災害、災難性人為錯誤或惡意行為所造成的全部或部分資料中心永久遺失快速復原。 在 [商務持續性概觀](sql-database-business-continuity.md)中可以找到特定的 RPO 資料。

> [!NOTE]
> 如果兩個區域之間的網路失敗，我們會每隔 10 秒重試，以便重新建立連線。
> [!IMPORTANT]
> 若要保證主要資料庫上的重大變更在容錯移轉之前會複寫至次要資料庫，您可以強制同步處理，以確保複寫重大變更 (例如，密碼更新)。 強制進行同步處理會對效能產生影響，因為它會封鎖呼叫執行緒，直到所有認可的交易都完成複寫為止。 如需詳細資訊，請參閱 [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)。 若要監視主要資料庫和地區資料庫之間的複寫延遲，請參閱 [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)。

下圖顯示以美國中北部區域的主要資料庫和美國中南部區域的次要資料庫所設定的作用中異地複寫範例。

![異地複寫關聯性](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

因為次要資料庫可讀取，所以可用來卸載唯讀工作負載，例如報告作業。 如果您使用主動式異地複寫，可以在具有主要資料庫的相同區域中建立次要資料庫，但這樣不會增加應用程式發生嚴重失敗時的恢復能力。 如果您使用自動容錯移轉群組，則一律要在不同區域中建立次要資料庫。

除了災害復原之外，主動式異地複寫還可用於下列案例︰

- **資料庫移轉**：您可以使用作用中異地複寫，以最少的停機時間將資料庫從一部伺服器移轉到另一個線上伺服器。
- **應用程式升級**：您可以在應用程式升級期間建立額外的次要資料庫做為容錯回復複本。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 如需有關設計災害復原解決方案的詳細資訊，請參閱[使用主動式異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="active-geo-replication-terminology-and-capabilities"></a>作用中異地複寫的術語和功能

- **自動非同步複寫**

  您只能藉由新增至現有資料庫來建立次要資料庫。 您可以在任何 Azure SQL Database 伺服器建立次要資料庫。 创建完成之后，使用从主数据库复制的数据填充辅助数据库。 這個程序稱為植入。 建立並植入次要資料庫之後，主要資料庫的更新會以非同步方式自動複製到次要資料庫。 非同步複寫表示交易會先在主要資料庫上受到認可，才會複製到次要資料庫。

- **可讀取的次要資料庫**

  應用程式可以存取次要資料庫，使用用於存取主要資料庫的相同安全性主體或不同安全性主體進行唯讀作業。 在快照集隔離模式中執行次要資料庫，以確保主要資料庫更新的複寫 (記錄重播) 不會被次要資料庫上執行的查詢延遲。

> [!NOTE]
> 如果主要資料庫上有結構描述更新，次要資料庫上的記錄重播會延遲， 因為後者需要次要資料庫上的結構描述鎖定。
> [!IMPORTANT]
> 可以使用异地复制在与主数据库相同的区域中创建辅助数据库。 可以使用此辅助数据库对同一区域中的只读工作负荷进行负载均衡。 但是，同一区域中的辅助数据库不能提供额外的故障恢复能力，因此不适合用作灾难恢复的故障转移目标。 它也不保證會 avaialability 區域隔離。 使用商務關鍵性或使用 Premium 服務層[區域備援設定](sql-database-high-availability.md#zone-redundant-configuration)以達到 avaialability 區域隔離。   
>

- **計劃性容錯移轉**

  計劃性容錯移轉會在主要資料庫和次要資料庫之間執行完整同步處理，然後將次要資料庫切換為主要角色。 這可確保不會遺失資料。 計劃性容錯移轉用於下列案例：(a) 在生產環境中執行 DR 鑽研而不接受遺失資料的時候；(b) 將資料庫重新放置到不同區域；(c) 在中斷已經緩和 (容錯回復) 之後，將資料庫歸回至主要區域。

- **非計劃性容錯移轉**

  非計劃性或強制容錯移轉會立即將次要資料庫切換為主要角色，而不會與主要資料庫進行任何同步處理。 這項作業會導致資料遺失。 非計劃性容錯移轉是主要資料庫無法存取時，用來作為中斷期間的復原方法。 當原始的主要資料庫重新上線時，它會自動重新連線而不進行同步處理，並且變為新的次要資料庫。

- **多個次要資料庫**

  每個主要資料庫最多可建立 4 個次要資料庫。 如果只有一個次要資料庫卻失敗了，應用程式會暴露在更高的風險中，直到建立新的次要資料庫。 如果有多個次要資料庫存在，即使其中一個次要資料庫失敗，應用程式仍會受到保護。 其他的次要資料庫也可用來擴充唯讀工作負載

  > [!NOTE]
  > 如果您使用主動式異地複寫建置分散在世界各地的應用程式，而且必須在四個以上的區域中提供唯讀的資料存取，您可以建立次要資料庫的次要資料庫 (稱為鏈結的程序)。 如此一來您就可以達到幾乎無限擴充的資料庫複寫。 此外，鏈結可減少來自主要資料庫的複寫額外負荷。 缺點是會增加分葉最尾端之次要資料庫上的複寫延遲。

- **彈性集區中的資料庫異地複寫**

  每個次要資料庫都可以分別參與彈性集區，或完全不在任何彈性集區中。 每個次要資料庫的集區選擇都不同，而且不相依於任何其他次要資料庫 (不論是主要還是次要) 的設定。 每個彈性集區都包含在單一區域內，因此相同拓撲中的多個次要資料庫永遠不會共用彈性集區。

- **次要資料庫的可設定計算大小**

  主要和次要資料庫必須有相同的服務層。 此外也強烈建議您使用與主要資料庫相同的計算大小 (DTU 或虛擬核心) 來建立次要資料庫。 計算大小較低的次要資料庫可能會有複寫延遲增加和無法使用的風險，進而有在容錯移轉之後遺失重要資料的風險。 因此，將無法保證已發佈的 RPO = 5 秒。 另一個風險是，在容錯移轉之後，應用程式的效能會因為新主要資料庫的計算容量不足而受到影響，除非該資料庫升級至較高的計算大小。 升級所需時間視資料庫大小而定。 此外，目前此類升級要求主要與次要資料庫都必須在線上，因此在中斷情況趨緩之前無法完成。 如果您決定建立具有較低計算大小的次要資料庫，您可以利用 Azure 入口網站上的記錄 IO 百分比圖表，來預估次要資料庫承受複寫負載所需的最低計算大小。 例如，如果您的主要資料庫是 P6 (1000 DTU) 和其記錄 IO 百分比為 50%，則次要資料庫必須至少是 P4 (500 DTU)。 您也可以使用 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 或 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 資料庫檢視來擷取記錄 IO 資料。  如需 SQL Database 計算大小的詳細資訊，請參閱 [SQL Database 服務層是什麼](sql-database-purchase-models.md)。

- **使用者控制的容錯移轉和容錯回復**

  應用程式或使用者可以隨時明確也將次要資料庫切換到主要角色。 在實際的中斷期間，應該使用「非計劃性」選項，這樣會立即將次要升級為主要。 當失敗的主要資料庫復原，並且可再次使用時，系統會自動將復原的主要資料庫標示為次要資料庫，並讓它與新的主要資料庫保持更新。 由於複寫的非同步本質，如果主要資料庫在將最新的變更複寫至次要資料庫之前失敗，則非計劃的容錯移轉期間會有少量的資料遺失。 當具有多個次要資料庫的主要資料庫容錯移轉時，系統會自動重新設定複寫關聯性，並且將剩餘的次要資料庫連結至新升級的主要資料庫，而不需要任何使用者介入。 解決造成容錯移轉的中斷之後，可能想要讓應用程式返回主要區域。 若要這麼做，應該使用「計劃」選項叫用容錯移轉命令。

- **保持認證和防火牆規則同步**

建议对异地复制数据库使用[数据库级 IP 防火墙规则](sql-database-firewall-configure.md)，以便这些规则可与数据库一起复制，确保所有辅助数据库具有与主数据库相同的 IP 防火墙规则。 此方法不需要客戶手動設定及維護同時裝載主要和次要資料庫之伺服器上的防火牆規則。 同樣地，針對資料存取使用 [自主資料庫使用者](sql-database-manage-logins.md) ，確保主要與次要資料庫永遠具有相同的使用者認證，在容錯移轉時不會因為登入和密碼不相符而有任何中斷。 使用額外的 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，客戶可以管理主要和次要資料庫的使用者存取，並且排除在資料庫中管理認證的需求。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級主要資料庫

您可以將主要資料庫升級或降級至不同的計算大小 (在相同的服務層內，而不是一般用途和業務關鍵之間)，而不需要將任何次要資料庫中斷連線。 升級時，我們建議您先升級次要資料庫，然後再升級主要資料庫。 降級時，順序相反︰先降級主要資料庫，然後再降級次要資料庫。 當您將資料庫升級或降級到不同的服務層時，會強制執行這項建議。

> [!NOTE]
> 如果您已在容錯移轉群組設定中建立次要資料庫，則不建議降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。

> [!IMPORTANT]
> 不能将故障转移组中的主数据库扩展到更高的层，除非已先将辅助数据库扩展到该层。 如果尝试在扩展辅助数据库之前扩展主数据库，可能会收到以下错误：
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失

由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 但是，某些应用程序可能要求不能有数据丢失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 呼叫 **sp_wait_for_database_copy_sync** 會封鎖呼叫執行緒，直到最後認可的交易傳輸到次要資料庫。 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 **sp_wait_for_database_copy_sync** 以特定的連續複製連結為範圍。 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> **sp_wait_for_database_copy_sync** 可避免在容錯移轉之後資料遺失，但是不保證讀取權限會完整同步。 **sp_wait_for_database_copy_sync** 程序呼叫所造成的延遲可能會相當明顯，且取決於呼叫時的交易記錄大小。

## <a name="monitoring-geo-replication-lag"></a>监视异地复制延迟

若要监视与 RPO 相关的延迟，请使用主数据库中 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 的 *replication_lag_sec* 列。 它显示在主数据库上提交的事务与在辅助数据库上保留的事务之间的延迟（以秒为单位）。 例如 如果延迟值为 1 秒，则意味着如果主数据库现在受到某个中断的影响并启动了故障转移，则不会保存最近 1 秒执行的事务。 

若要以在主数据库上所做的更改应用到辅助数据库（即可以从辅助数据库读取）所需的时间来衡量延迟，请将辅助数据库上的 *last_commit* 时间与主数据库上的同一值进行比较。

> [!NOTE]
> 有时候，主数据库上的 *replication_lag_sec* 的值为 NULL，这意味着主数据库目前不知道辅助数据库辅助数据库有多远。   这通常发生在进程重启之后，应该是一个暂时情况。 如果 *replication_lag_sec* 在长时间内一直返回 NULL，考虑向应用程序报警。 这表示辅助数据库因永久连接故障而无法与主数据库通信。 此外还有情况可能会导致辅助数据库上的 *last_commit* 时间与主数据库上的该时间的差异变得很大。 例如 如果在长期没有进行更改的情况下进行提交，则该差异会突然变成一个很大的值，然后快速回到 0。 如果这两个值之间的差异长时间保持很大，可将其视为一种错误情况。


## <a name="programmatically-managing-active-geo-replication"></a>以程式設計方式管理主動式異地複寫

如前所述，作用中異地複寫可使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。 主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../role-based-access-control/overview.md)。

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL：管理單一和集區資料庫的容錯移轉

> [!IMPORTANT]
> 這些 Transact-SQL 命令僅適用於作用中異地複寫，不適用於容錯移轉群組。 因此它們也不適用於受控執行個體，因為受控執行個體僅支援容錯移轉群組。

| 命令 | 描述 |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |使用 ADD SECONDARY ON SERVER 引數，針對現有資料庫建立次要資料庫並開始資料複寫 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |使用 FAILOVER 或 FORCE_FAILOVER_ALLOW_DATA_LOSS，將次要資料庫切換為主要資料庫以便開始容錯移轉 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |使用 REMOVE SECONDARY ON SERVER，來終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |針對 Azure SQL Database 伺服器上的每個資料庫，傳回所有現有複寫連結的相關資訊。 |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |針對指定的 SQL Database，取得上次複寫時間、上次複寫延遲，以及複寫連結的其他相關資訊。 |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |顯示所有資料庫作業的狀態，包括複寫連結的狀態。 |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |導致應用程式等候，直到作用中次要資料庫複寫並認可所有認可的交易為止。 |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell：管理單一和集區資料庫的容錯移轉

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

| Cmdlet | 描述 |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |取得一或多個資料庫。 |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |針對現有資料庫建立次要資料庫並開始資料複寫。 |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |將次要資料庫切換為主要資料庫以開始容錯移轉。 |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |終止 SQL Database 和指定次要資料庫間的資料複寫。 |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |取得 Azure SQL Database 和資源群組或 SQL Server 之間的異地複寫連結。 |
|  | |

> [!IMPORTANT]
> 如需範例指令碼，請參閱[使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)和[使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)。

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API：管理單一和集區資料庫的容錯移轉

| API | 描述 |
| --- | --- |
| [Create or Update Database (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |建立、更新或還原主要或次要資料庫。 |
| [获取创建或更新数据库状态](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |在建立作業期間傳回狀態。 |
| [將次要資料庫設定為主要資料庫 (計劃性容錯移轉)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |從目前主要資料庫進行容錯移轉，以設定主要的次要資料庫。 **此選項不支援受控執行個體。**|
| [將次要資料庫設定為主要資料庫 (非計劃的容錯移轉)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |從目前主要資料庫進行容錯移轉，以設定主要的次要資料庫。 這項作業可能會導致資料遺失。 **此選項不支援受控執行個體。**|
| [取得複寫連結](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |取得異地複寫關聯性中指定 SQL Database 的特定複寫連結。 它會擷取 sys.geo_replication_links 目錄檢視中顯示的資訊。 **此選項不支援受控執行個體。**|
| [複寫連結 - 依資料庫列示](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | 取得異地複寫關聯性中指定 SQL Database 的所有複寫連結。 它會擷取 sys.geo_replication_links 目錄檢視中顯示的資訊。 |
| [刪除複寫連結](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | 刪除資料庫複寫連結。 無法在容錯移轉期間進行。 |
|  | |

## <a name="next-steps"></a>後續步驟

- 如需範例指令碼，請參閱：
  - [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database 也支援自動容錯移轉群組。 如需詳細資訊，請參閱使用[自動容錯移轉群組](sql-database-auto-failover-group.md)。
- 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
- 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
- 若要了解新主服务器和数据库的身份验证要求，请参阅[灾难恢复后的 SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
