---
title: automatic, geo-redundant backups
description: SQL Database 每隔幾鐘會自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體來進行異地備援。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: 77442eda6c8b2aae71c5d647127ead9f851ec485
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421431"
---
# <a name="automated-backups"></a>自動備份

SQL Database automatically creates the database backups that are kept between 7 and 35 days, and uses Azure [read-access geo-redundant storage (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) to ensure that they are preserved even if the data center is unavailable. These backups are created automatically. 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 If your security rules require that your backups are available for an extended period of time (up to 10 years), you can configure a [long-term retention](sql-database-long-term-retention.md) on Singleton databases and Elastic pools.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份

SQL Database uses SQL Server technology to create [full backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) every week, [differential backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) every 12 hours, and [transaction log backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) every 5-10 minutes. The backups are stored in [RA-GRS storage blobs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) that are replicated to a [paired data center](../best-practices-availability-paired-regions.md) for protection against a data center outage. 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。

您可以使用這些備份︰

- **Restore an existing database to a point-in-time in the past** within the retention period using the Azure portal, Azure PowerShell, Azure CLI, or REST API. In Single database and Elastic pools, this operation will create a new database in the same server as the original database. In Managed Instance, this operation can create a copy of the database or same or different Managed Instance under the same subscription.
  - **[Change Backup Retention Period](#how-to-change-the-pitr-backup-retention-period)** between 7 to 35 days to configure your backup policy.
  - **Change long-term retention policy up to 10 years** on Single Database and Elastic Pools using [the Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell).
- **Restore a deleted database to the time it was deleted** or anytime within the retention period. The deleted database can only be restored in the same logical server or Managed Instance where the original database was created.
- **Restore a database to another geographical region**. 異地還原可讓您在無法存取您的伺服器和資料庫時，從地理災害中復原。 在世界各地任何現有的伺服器中建立新的資料庫。
- **Restore a database from a specific long-term backup** on Single Database or Elastic Pool if the database has been configured with a long-term retention policy (LTR). LTR allows you to restore an old version of the database using [the Azure portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) to satisfy a compliance request or to run an old version of the application. 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。
- 若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」指的是保持多個次要資料庫與主要資料庫同步。

You can try some of these operations using the following examples:

| | Azure 入口網站 | Azure PowerShell |
|---|---|---|
| Change backup retention | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [受控執行個體](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Change Long-term backup retention | [單一資料庫](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance - N/A  | [Single Database](sql-database-long-term-backup-retention-configure.md)<br/>Managed Instance - N/A  |
| Restore database from point-in-time | [單一資料庫](sql-database-recovery-using-backups.md#point-in-time-restore) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 還原已刪除的資料庫 | [單一資料庫](sql-database-recovery-using-backups.md) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restore database from Azure Blob Storage | Single database - N/A <br/>Managed Instance - N/A  | Single database - N/A <br/>[受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>備份會保留多久的時間

All Azure SQL databases (single, pooled, and managed instance databases) have a default backup retention period of  **seven** days. You can [change backup retention period up to 35 days](#how-to-change-the-pitr-backup-retention-period).

如果您刪除資料庫，則 SQL Database 會以保存線上資料庫備份的相同方式保存備份。 例如，如果您刪除保留期間為七天的基本資料庫，則為期四天的備份還會再儲存三天。

如果您需要保留備份的時間超過保留週期上限，則可以修改備份的屬性，以將一或多個長期保留週期新增至資料庫。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果您刪除裝載 SQL 資料庫的 Azure SQL Server，則也會一併刪除所有屬於該伺服器的彈性集區和資料庫，且無法復原。 您無法還原已刪除的伺服器。 但是，如果您已設定長期保留，則不會刪除具有 LTR 之資料庫的備份，而且可以還原這些資料庫。

## <a name="how-often-do-backups-happen"></a>備份發生頻率為何

### <a name="backups-for-point-in-time-restore"></a>時間點還原的備份

SQL Database 透過自動建立完整備份、差異備份和交易記錄備份，以支援自助式時間點還原 (PITR)。 根據計算大小和資料庫活動量的頻率，完整資料庫備份會每週建立，差異資料庫備份通常每隔 12 小時建立，而交易記錄備份通常每隔 5-10 分鐘建立。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 You cannot change or disable the backup jobs. 

PITR 備份為異地備援，並受到 [Azure 儲存體跨區域複寫](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保護

如需詳細資訊，請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>長期保留的備份

單一和集區資料庫提供選項讓您在 Azure Blob 儲存體中設定完整備份的長期保留 (LTR)，最長可達 10 年。 如果啟用 LTR 原則，則會將每週完整備份自動複製到不同的 RA-GRS 儲存體容器。 為了符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留期限。 儲存體耗用量取決於選取的備份頻率和保留期間。 您可以使用 [LTR 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database)來估算 LTR 儲存體的成本。

與 PITR 類似，LTR 備份為異地備援，並受到 [Azure 儲存體跨區域複寫](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保護。

如需詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

## <a name="storage-costs"></a>儲存體成本
For single databases and managed instances, a minimum backup storage amount equal to 100% of database size is provided at no extra charge. For elastic pools, a minimum backup storage amount equal to 100% of the allocated data storage for the pool is provided at no extra charge. 備份儲存體的額外使用量會按每月每 GB 來收費。 This additional consumption will depend on the workload and size of the individual databases.

You can use Azure subscription cost analysis to determine your current spending on backup storage.

![Backup storage cost analysis](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

If you go to your subscription and open Cost Analysis blade, you can select meter subcategory **mi pitr backup storage** to see your current backup cost and charge forecast. You can also include other meter subcategories such as **managed instance general purpose - storage** or **managed instance general purpose - compute gen5** to compare backup storage cost with other cost categories.

> [!Note]
> You can [change retention period to 7 days](#change-pitr-backup-retention-period-using-azure-portal) to reduce the backup storage cost.

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。 

## <a name="are-backups-encrypted"></a>備份是否已加密

如果您的資料庫使用 TDE 加密，則備份會在靜止時自動加密 (包括 LTR 備份)。 Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL 資料庫預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft 如何確保備份完整性

On an ongoing basis, the Azure SQL Database engineering team automatically tests the restore of automated database backups of databases placed in Logical servers and Elastic pools (this is not available in Managed Instance). Upon point-in-time restore, databases also receive integrity checks using DBCC CHECKDB.

Managed Instance takes automatic initial backup with `CHECKSUM` of the databases restored using native `RESTORE` command or Data Migration Service once the migration is completed.

在完整性檢查期間找到的任何問題都會對工程小組發出警示。 如需有關 Azure SQL Database 中資料完整性的詳細資訊，請參閱 [Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="how-do-automated-backups-impact-compliance"></a>自動化備份對合規性的影響為何

當您將資料庫從預設 PITR 保留 35 天的以 DTU 為基礎的服務層級遷移至以虛擬核心為基礎的服務層級時，會保留 PITR 保留，確保不會危害您應用程式的資料復原原則。 如果預設保留週期不符合合規性需求，您可以使用 PowerShell 或 REST API 變更 PITR 保留週期。 如需詳細資訊，請參閱[變更備份保留週期](#how-to-change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>如何變更 PITR 備份保留期間

You can change the default PITR backup retention period using the Azure portal, PowerShell, or the REST API. 支援的值為：7、14、21、28 或 35 天。 下列範例說明如何將 PITR 保留變更為 28 天。

> [!WARNING]
> If you reduce the current retention period, all existing backups older than the new retention period are no longer available. 如果您延長目前的保留期間，則 SQL Database 將保留現有備份，直到達到較長的保留期間為止。

> [!NOTE]
> 這些 API 只會影響 PITR 保留期間。 如果您已將資料庫設定為 LTR，則它不受影響。 如需如何變更 LTR 保留期間的詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Change PITR backup retention period using Azure portal

To change the PITR backup retention period using the Azure portal, navigate to the server object whose retention period you wish to change within the portal and then select the appropriate option based on which server object you're modifying.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[單一資料庫與彈性集區](#tab/single-database)

Change of PITR backup retention for single Azure SQL Databases is performed at the server level. Change made at the server level applies to databases on that server. To change PITR for Azure SQL Database server from Azure portal, navigate to the server overview blade, click on Manage Backups on the navigation menu, and then click on Configure retention at the navigation bar.

![變更 PITR Azure 入口網站](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[受控執行個體](#tab/managed-instance)

Change of PITR backup retention for SQL Database managed instance is performed at an individual database level. To change PITR backup retention for an instance database from Azure portal, navigate to the individual database overview blade, and then click on Configure backup retention at the navigation bar.

![變更 PITR Azure 入口網站](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>使用 PowerShell 變更 PITR 備份保留期間

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>使用 REST API 變更 PITR 保留期間

#### <a name="sample-request"></a>範例要求

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>範例回應

狀態碼：200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

如需詳細資訊，請參閱[備份保留 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)。

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。
- 若要使用 Azure 入口網站還原至某個時間點，請參閱[使用 Azure 入口網站將資料庫還原至時間點](sql-database-recovery-using-backups.md)。
- 若要使用 PowerShell 還原至某個時間點，請參閱[使用 PowerShell 將資料庫還原至時間點](scripts/sql-database-restore-database-powershell.md)。
- 若要使用 Azure 入口網站在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從該保留還原，請參閱[使用 Azure 入口網站來管理長期備份保留 (英文)](sql-database-long-term-backup-retention-configure.md)。
- To configure, manage, and restore from long-term retention of automated backups in Azure Blob storage using PowerShell, see [Manage long-term backup retention using PowerShell](sql-database-long-term-backup-retention-configure.md).
