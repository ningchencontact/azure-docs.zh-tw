---
title: Azure SQL Database 自動、異地備援備份 | Microsoft Docs
description: SQL Database 每隔幾鐘會自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體來進行異地備援。
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
ms.date: 04/12/2019
ms.openlocfilehash: 8a2a61e821ad41265dc9262064a79a5c44abbc7f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545819"
---
# <a name="automated-backups"></a>自動備份

SQL Database 會自動建立保留 7 到 35 天的資料庫備份，並使用 Azure 讀取權限異地備援儲存體 (RA-GRS)，以確保即使在資料中心都無法使用時可保留它們。 這些備份是自動建立的，且不需額外付費。 您不需要採取任何動作來進行，且您可以[變更備份保留期限](#how-to-change-the-pitr-backup-retention-period)。 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果您的安全性規則需要備份可供使用一段時間 (最長 10 年)，您可以設定[長期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份

針對還原時間點 (PITR) 目的，SQL Database 會使用 SQL Server 技術來建立[完整](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)、[差異](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)及[交易記錄](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)備份。 根據計算大小和資料庫活動量的頻率，交易記錄備份通常每隔 5-10 分鐘會進行一次，而差異備份通常每隔 12 小時進行一次。 完整、差異及交易記錄備份可讓您將資料庫還原到裝載該資料庫之相同伺服器上的特定時間點。 儲存在 RA-GRS 儲存體 Blob 中的備份會複寫到[配對的資料中心](../best-practices-availability-paired-regions.md)，以防止發生資料中心中斷的情況。 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。

您可以使用這些備份︰

- 將資料庫還原至保留期限內的某一個時間點。 這項作業將會在與原始資料庫相同的伺服器中建立新的資料庫。
- 將已刪除的資料庫還原至其被刪除的時間，或保留期限內的任何時間。 已刪除的資料庫只能在當中已建立原始資料庫的相同伺服器中還原。
- 將資料庫還原到另一個地理區域。 異地還原可讓您在無法存取您的伺服器和資料庫時，從地理災害中復原。 在世界各地任何現有的伺服器中建立新的資料庫。
- 如果已使用長期保留原則 (LTR) 設定資料庫，請從特定長期備份還原資料庫。 LTR 可讓您還原舊版的資料庫來符合規範要求，或執行舊版的應用程式。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。
- 若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」指的是保持多個次要資料庫與主要資料庫同步。

## <a name="how-long-are-backups-kept"></a>備份會保留多久的時間

視購買模型和服務層而定，每個 SQL Database 的預設備份保留週期會介於 7 到 35 天。 可以在 SQL 数据库服务器上更新数据库的备份保持期。 如需詳細資訊，請參閱[變更備份保留週期](#how-to-change-the-pitr-backup-retention-period)。

如果您刪除資料庫，則 SQL Database 會以保存線上資料庫備份的相同方式保存備份。 例如，如果您刪除保留期間為七天的基本資料庫，則為期四天的備份還會再儲存三天。

如果您需要保留備份的時間超過保留週期上限，則可以修改備份的屬性，以將一或多個長期保留週期新增至資料庫。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果您刪除裝載 SQL 資料庫的 Azure SQL Server，則也會一併刪除所有屬於該伺服器的彈性集區和資料庫，且無法復原。 您無法還原已刪除的伺服器。 但是，如果您已設定長期保留，則不會刪除具有 LTR 之資料庫的備份，而且可以還原這些資料庫。

### <a name="default-backup-retention-period"></a>預設備份保留期限

#### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

使用以 DTU 為基礎的購買模型所建立之資料庫的預設保留週期取決於服務層：

- 基本服務層為 1 週。
- 標準服務層為 5 週。
- 進階服務層為 5 週。

#### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

如果您是使用[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)，預設的備份保留期限是 7 天 (適用於單一、集區和執行個體資料庫)。 針對所有 Azure SQL 資料庫 (單一、集區和執行個體資料庫)，您可以[將備份保留期限變更為最多 35 天](#how-to-change-the-pitr-backup-retention-period)。

> [!WARNING]
> 如果缩短当前保留期，早于新保留期的所有现有备份将不再可用。 如果您延長目前的保留期間，則 SQL Database 將保留現有備份，直到達到較長的保留期間為止。

## <a name="how-often-do-backups-happen"></a>備份發生頻率為何

### <a name="backups-for-point-in-time-restore"></a>時間點還原的備份

SQL Database 透過自動建立完整備份、差異備份和交易記錄備份，以支援自助式時間點還原 (PITR)。 根據計算大小和資料庫活動量的頻率，完整資料庫備份會每週建立，差異資料庫備份通常每隔 12 小時建立，而交易記錄備份通常每隔 5-10 分鐘建立。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 在完成首次完整备份后，在后台以静默方式自动计划和管理所有后续备份。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 您無法變更或停用備份作業。 

PITR 備份為異地備援，並受到 [Azure 儲存體跨區域複寫](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保護

如需詳細資訊，請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>長期保留的備份

單一和集區資料庫提供選項讓您在 Azure Blob 儲存體中設定完整備份的長期保留 (LTR)，最長可達 10 年。 如果啟用 LTR 原則，則會將每週完整備份自動複製到不同的 RA-GRS 儲存體容器。 為了符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留期限。 儲存體耗用量取決於選取的備份頻率和保留期間。 您可以使用 [LTR 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database)來估算 LTR 儲存體的成本。

與 PITR 類似，LTR 備份為異地備援，並受到 [Azure 儲存體跨區域複寫](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保護。

如需詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

## <a name="storage-costs"></a>儲存成本
根據預設，系統會將您資料庫的 7 天自動備份複製到 RA-GRS 標準 Blob 儲存體。 每週完整備份、每日差異備份以及每 5 分鐘複製一次的交易記錄備份都使用此儲存體。 交易記錄檔的大小取決於資料庫的變動率。 會提供等於資料庫大小 100% 的最低儲存空間，且無額外費用。 備份儲存體的額外使用量會按每月每 GB 來收費。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。 

## <a name="are-backups-encrypted"></a>備份是否已加密

如果您的資料庫使用 TDE 加密，則備份會在靜止時自動加密 (包括 LTR 備份)。 Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL Database 預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft 如何確保備份完整性

Azure SQL Database 工程小組會持續自動地對服務上所有資料庫，進行自動資料庫備份的還原測試。 一旦還原，資料庫也會收到使用 DBCC CHECKDB 的完整性檢查。 在完整性檢查期間找到的任何問題都會對工程小組發出警示。 如需有關 Azure SQL Database 中資料完整性的詳細資訊，請參閱 [Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="how-do-automated-backups-impact-compliance"></a>自動化備份對合規性的影響為何

當您將資料庫從預設 PITR 保留 35 天的 DTU 型服務層遷移至 vCore 型服務層時，會保留 PITR 保留，確保不會危害您應用程式的資料復原原則。 如果預設保留週期不符合合規性需求，您可以使用 PowerShell 或 REST API 變更 PITR 保留週期。 如需詳細資料，請參閱[變更備份保留週期](#how-to-change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>如何變更 PITR 備份保留期間

您可以變更使用 Azure 入口網站、 PowerShell 或 REST API 的預設 PITR 備份保留期限。 支援的值為：7、14、21、28 或 35 天。 下列範例說明如何將 PITR 保留變更為 28 天。

> [!NOTE]
> 這些 API 只會影響 PITR 保留期間。 如果您已將資料庫設定為 LTR，則它不受影響。 如需如何變更 LTR 保留期間的詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>使用 Azure 入口網站變更 PITR 備份保留期間

若要變更使用 Azure 入口網站的 PITR 備份保留期限，請瀏覽至 伺服器物件的保留期限，您想要在入口網站中變更，然後選取適當的選項以您要修改哪一個伺服器物件。

#### <a name="change-pitr-for-a-sql-database-server"></a>變更 SQL Database 伺服器的 PITR

![變更 PITR Azure 入口網站](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>變更受控執行個體的 PITR

![變更 PITR Azure 入口網站](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>使用 PowerShell 變更 PITR 備份保留期間

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

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
- 若要使用 PowerShell 在 Azure Blob 存储中配置和管理自动备份的长期保留，并从中进行还原，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
