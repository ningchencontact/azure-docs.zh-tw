---
title: 將單一或集區 Azure SQL 資料庫匯出到 BACPAC 檔案 | Microsoft Docs
description: 使用 Azure 入口網站將 Azure SQL Database 匯出到 BACPAC 檔案
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: 27a65a871264fa13a42acfb5be2d4b5f99d31adc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57758687"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>將 Azure SQL 資料庫匯出到 BACPAC 檔案

當您需要匯出資料庫以封存或移至另一個平台時，可以將資料庫結構描述和資料匯出至 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。 BACPAC 檔案是一種副檔名為 BACPAC 的 ZIP 檔案，其包含來自 SQL Server Database 的中繼資料和資料。 BACPAC 檔案可以儲存在 Azure Blob 儲存體，或在內部部署位置的本機儲存體中，之後再匯入至 Azure SQL Database 或 SQL Server 內部部署安裝。

## <a name="considerations-when-exporting-an-azure-sql-database"></a>匯出 Azure SQL Database 時的考量

- 为保证导出的事务处理方式一致，必须确保导出期间未发生写入活动，或者正在从 Azure SQL 数据库的[事务处理方式一致性副本](sql-database-copy.md)中导出。
- 如果您要匯出至 blob 儲存體，BACPAC 檔案的大小上限為 200 GB。 若要存档更大的 BACPAC 文件，请导出到本地存储。
- 不支援使用本文所討論的方法將 BACPAC 檔案匯出到 Azure 進階儲存體。
- 在防火牆後面的儲存體目前不支援。
- 如果執行從 Azure SQL Database 匯出的作業超過 20 個小時，它可能會被取消。 若要增加匯出期間的效能，您可以︰

  - 暫時提高計算大小。
  - 在匯出期間停止所有讀取及寫入活動。
  - 在所有大型資料表上搭配使用 [叢集索引](https://msdn.microsoft.com/library/ms190457.aspx) 和非 null 值。 若沒有叢集索引，如果要花超過 6-12 小時，匯出可能會失敗。 這是因為匯出服務需要完成資料表掃描，以便嘗試匯出整份資料表。 有一個可判斷資料表是否已針對匯出進行最佳化的好方法，就是執行 **DBCC SHOW_STATISTICS**，並確定 *RANGE_HI_KEY* 不是 null 且其值具有良好的分佈。 如需詳細資料，請參閱 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 並非用於備份和還原作業。 Azure SQL Database 會自動為每個使用者資料庫建立備份。 如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)和 [SQL Database 備份](sql-database-automated-backups.md)。

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>使用 Azure 入口網站匯出到 BACPAC 檔案

> [!NOTE]
> [受控執行個體](sql-database-managed-instance.md)目前不支援使用 Azure 入口網站，將資料庫匯出至 BACPAC 檔案。 若要將受控執行個體匯出到 BACPAC 檔案，請使用 SQL Server Management Studio 或 SQLPackage。

1. 若要使用 [Azure 入口網站](https://portal.azure.com)匯出資料庫，請開啟資料庫頁面，然後按一下工具列上的 [匯出]。

   ![資料庫匯出](./media/sql-database-export/database-export1.png)

2. 指定 BACPAC 檔案名稱、選取要匯出之現有的 Azure 儲存體帳戶和容器，然後提供適當的認證以存取來源資料庫。

    ![資料庫匯出](./media/sql-database-export/database-export2.png)

3. 按一下 [確定]。

4. 若要監視匯出作業的進度，請開啟包含匯出資料庫的 SQL Database 伺服器頁面。 在 [設定] 下，然後按一下 [匯入/匯出記錄]。

   ![匯出記錄](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>使用 SQLPackage 公用程式匯出到 BACPAC 檔案

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯出 SQL 資料庫，請參閱[匯出參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 SQLPackage 实用工具附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

針對大部分生產環境中的延展性和效能，我們建議您使用 SQLPackage 公用程式。 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

此範例會說明如何透過 Active Directory 通用驗證使用 SqlPackage.exe 匯出資料庫：

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 匯出到 BACPAC 檔案

最新版的 SQL Server Management Studio 提供了精靈協助您將 Azure SQL Database 匯出至 BACPAC 檔案。 請參閱[匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)。

## <a name="export-to-a-bacpac-file-using-powershell"></a>使用 PowerShell 匯出到 BACPAC 檔案

> [!NOTE]
> [受控執行個體](sql-database-managed-instance.md)目前不支援使用 Azure PowerShell 將資料庫匯出至 BACPAC 檔案。 若要將受控執行個體匯出到 BACPAC 檔案，請使用 SQL Server Management Studio 或 SQLPackage。

使用[新增 AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet 來提交匯出資料庫要求至 Azure SQL Database 服務。 視資料庫大小而定，匯出作業可能需要一些時間才能完成。

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

若要檢查的匯出要求狀態，請使用[Get AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status :InProgress**。 當您看到 **Status:Succeeded** 時，匯出已完成。

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>後續步驟

- 若要了解單一資料庫和集區資料庫的長期備份保留，以替換匯出的資料庫以達到封存目的，請參閱[長期備份保留](sql-database-long-term-retention.md)。 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代長期備份保留。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。
- 若要了解如何將 BACPAC 匯入 SQL Server 資料庫，請參閱 [將 BACPAC 匯入 SQL Server 資料庫](https://msdn.microsoft.com/library/hh710052.aspx)。
- 若要了解如何將 BACPAC 從 SQL Server 資料庫匯出，請參閱[匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- 若要了解如何使用資料移轉服務來移轉資料庫，請參閱[使用 DMS 在離線狀態下將 SQL Server 移轉至 Azure SQL Database](../dms/tutorial-sql-server-to-azure-sql.md)。
- 如果您要從 SQL Server 匯出以準備移轉至 Azure SQL Database，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
