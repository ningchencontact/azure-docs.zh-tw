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
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: a8cf17ab3eab31d4ac6113437f55d73f96425e4e
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843293"
---
# <a name="automated-backups"></a>自動備份

SQL Database 會自動建立保留7到35天的資料庫備份, 並使用 Azure[讀取權限異地冗余儲存體 (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)來確保即使資料中心無法使用, 也會保留它們。 這些備份會自動建立。 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果您的安全性規則需要備份可供使用一段時間 (最多10年), 您可以在單一資料庫和彈性集區上設定[長期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份

SQL Database 使用 SQL Server 的技術, 每週建立[完整備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)、每12小時進行[差異備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), 以及每5-10 分鐘建立一次[交易記錄備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 這些備份會儲存在[GRS 儲存體 blob](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)中, 並複寫到配對的[資料中心](../best-practices-availability-paired-regions.md), 以防止資料中心中斷。 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。

您可以使用這些備份︰

- 使用 [Azure 入口網站]、[Azure PowerShell]、[Azure CLI] 或 [REST API], 將**現有的資料庫還原到**保留期限內的過去時間點。 在單一資料庫和彈性集區中, 這項作業會在與原始資料庫相同的伺服器中建立新的資料庫。 在受控執行個體中, 這項作業可以在相同的訂用帳戶下建立資料庫複本或相同或不同的受控執行個體。
  - **[將備份保留期間變更](#how-to-change-the-pitr-backup-retention-period)** 為7到35天, 以設定您的備份原則。
  - 使用[Azure 入口網站](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups), 在單一資料庫和彈性集區上**變更長期保留原則最多10年**。
- 將**已刪除的資料庫還原至其刪除**或保留期間內的任何時間。 已刪除的資料庫只能在原始資料庫建立所在的相同邏輯伺服器或受控執行個體中進行還原。
- **將資料庫還原到另一個地理區域**。 異地還原可讓您在無法存取您的伺服器和資料庫時，從地理災害中復原。 在世界各地任何現有的伺服器中建立新的資料庫。
- 如果資料庫已設定長期保留原則 (LTR), 請在單一資料庫或彈性集區上**從特定長期備份還原資料庫**。 LTR 可讓您使用[Azure 入口網站](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)來還原舊版本的資料庫, 以滿足合規性要求或執行舊版的應用程式。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。
- 若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」指的是保持多個次要資料庫與主要資料庫同步。

您可以使用下列範例來嘗試其中一些作業:

| | Azure 入口網站 | Azure PowerShell |
|---|---|---|
| 變更備份保留期 | [單一資料庫](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-azure-portal) <br/> [受控執行個體](sql-database-automated-backups.md#managed-instance-database) | [單一資料庫](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 變更長期備份保留期 | [單一資料庫](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>受控執行個體-N/A  | [單一資料庫](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>受控執行個體-N/A  |
| 從時間點還原資料庫 | [單一資料庫](sql-database-recovery-using-backups.md#point-in-time-restore) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 還原已刪除的資料庫 | [單一資料庫](sql-database-recovery-using-backups.md) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 從 Azure Blob 儲存體還原資料庫 | 單一資料庫-N/A <br/>受控執行個體-N/A  | 單一資料庫-N/A <br/>[受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>備份會保留多久的時間

所有 Azure SQL 資料庫 (單一、集區和受控實例資料庫) 的預設備份保留期限為**7**天。 您可以[將備份保留期限變更為最多35天](#how-to-change-the-pitr-backup-retention-period)。

如果您刪除資料庫，則 SQL Database 會以保存線上資料庫備份的相同方式保存備份。 例如，如果您刪除保留期間為七天的基本資料庫，則為期四天的備份還會再儲存三天。

如果您需要保留備份的時間超過保留週期上限，則可以修改備份的屬性，以將一或多個長期保留週期新增至資料庫。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果您刪除裝載 SQL 資料庫的 Azure SQL Server，則也會一併刪除所有屬於該伺服器的彈性集區和資料庫，且無法復原。 您無法還原已刪除的伺服器。 但是，如果您已設定長期保留，則不會刪除具有 LTR 之資料庫的備份，而且可以還原這些資料庫。

## <a name="how-often-do-backups-happen"></a>備份發生頻率為何

### <a name="backups-for-point-in-time-restore"></a>時間點還原的備份

SQL Database 透過自動建立完整備份、差異備份和交易記錄備份，以支援自助式時間點還原 (PITR)。 根據計算大小和資料庫活動量的頻率，完整資料庫備份會每週建立，差異資料庫備份通常每隔 12 小時建立，而交易記錄備份通常每隔 5-10 分鐘建立。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 您無法變更或停用備份作業。 

PITR 備份為異地備援，並受到 [Azure 儲存體跨區域複寫](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保護

如需詳細資訊，請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>長期保留的備份

單一和集區資料庫提供選項讓您在 Azure Blob 儲存體中設定完整備份的長期保留 (LTR)，最長可達 10 年。 如果啟用 LTR 原則，則會將每週完整備份自動複製到不同的 RA-GRS 儲存體容器。 為了符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留期限。 儲存體耗用量取決於選取的備份頻率和保留期間。 您可以使用 [LTR 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database)來估算 LTR 儲存體的成本。

與 PITR 類似，LTR 備份為異地備援，並受到 [Azure 儲存體跨區域複寫](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保護。

如需詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

## <a name="storage-costs"></a>儲存成本
針對單一資料庫，會免費提供等於 100% 資料庫大小的備份儲存體數量下限。 若為彈性集區，則會免費提供等於為集區配置之資料儲存體的 100% 的最小備份儲存體數量。 備份儲存體的額外使用量會按每月每 GB 來收費。 這項額外的耗用量將取決於個別資料庫的工作負載和大小。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。 

## <a name="are-backups-encrypted"></a>備份是否已加密

如果您的資料庫使用 TDE 加密，則備份會在靜止時自動加密 (包括 LTR 備份)。 Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL 資料庫預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft 如何確保備份完整性

Azure SQL Database 工程小組會持續自動測試在邏輯伺服器和彈性集區中的資料庫之自動資料庫備份的還原 (這在受控執行個體中無法使用)。 在還原時間點時, 資料庫也會使用 DBCC CHECKDB 來接收完整性檢查。

當遷移完成後, 受控執行個體`CHECKSUM`會使用原生`RESTORE`命令或資料移轉服務來自動進行初始備份, 以還原資料庫。

在完整性檢查期間找到的任何問題都會對工程小組發出警示。 如需有關 Azure SQL Database 中資料完整性的詳細資訊，請參閱 [Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="how-do-automated-backups-impact-compliance"></a>自動化備份對合規性的影響為何

當您將資料庫從預設 PITR 保留 35 天的以 DTU 為基礎的服務層級遷移至以虛擬核心為基礎的服務層級時，會保留 PITR 保留，確保不會危害您應用程式的資料復原原則。 如果預設保留週期不符合合規性需求，您可以使用 PowerShell 或 REST API 變更 PITR 保留週期。 如需詳細資訊，請參閱[變更備份保留週期](#how-to-change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>如何變更 PITR 備份保留期間

您可以使用 Azure 入口網站、PowerShell 或 REST API 來變更預設的 PITR 備份保留期限。 支援的值為：7、14、21、28 或 35 天。 下列範例說明如何將 PITR 保留變更為 28 天。

> [!WARNING]
> 如果您降低目前的保留期間, 則無法再使用新保留期間之前的所有現有備份。 如果您延長目前的保留期間，則 SQL Database 將保留現有備份，直到達到較長的保留期間為止。

> [!NOTE]
> 這些 API 只會影響 PITR 保留期間。 如果您已將資料庫設定為 LTR，則它不受影響。 如需如何變更 LTR 保留期間的詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>使用 Azure 入口網站變更 PITR 備份保留期限

若要使用 Azure 入口網站來變更 PITR 備份保留期限, 請流覽至您想要在入口網站中變更其保留期限的伺服器物件, 然後根據您要修改的伺服器物件, 選取適當的選項。

#### <a name="single-azure-sql-database"></a>單一 Azure SQL Database

單一 Azure SQL 資料庫的 PITR 備份保留變更會在伺服器層級執行。 在伺服器層級進行的變更會套用至該伺服器上的資料庫。 若要從 Azure 入口網站變更 Azure SQL Database server 的 PITR，請流覽至 [伺服器總覽] 分頁，按一下導覽功能表上的 [管理備份]，然後按一下巡覽列上的 [設定保留]。

![變更 PITR Azure 入口網站](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance-database"></a>受控實例資料庫

SQL Database 受控實例的 PITR 備份保留變更會在個別資料庫層級執行。 若要從 Azure 入口網站變更實例資料庫的 PITR 備份保留，請流覽至 [個別資料庫總覽] 分頁，然後按一下巡覽列上的 [設定備份保留]。

![變更 PITR Azure 入口網站](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>使用 PowerShell 變更 PITR 備份保留期間

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組, 但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet, 請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

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
- 若要使用 PowerShell 在 Azure Blob 儲存體中設定、管理自動備份的長期保留, 並從中還原, 請參閱[使用 Powershell 管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。
