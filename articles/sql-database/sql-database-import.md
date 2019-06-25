---
title: 匯入 BACPAC 檔案以建立 Azure SQL 資料庫 | Microsoft Docs
description: 匯入 BACPAC 檔案以建立新的 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/20/2019
ms.openlocfilehash: 0b92fb9c9bf022adce4cc0dd3e58ce8e476ed5b7
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303499"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>快速入門：在 Azure SQL Database 中將 BACPAC 檔案匯入資料庫

您可以使用 [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 檔案將 SQL Server 資料庫匯入 Azure SQL Database 中的資料庫。 您可以從儲存在 Azure Blob 儲存體 (僅標準儲存體) 中，或內部部署中的本機儲存體中的 `BACPAC` 檔案匯入資料。 若要藉由提供更多且更快速的資源來最大化匯入速度，請在匯入程序期間，將您的資料庫調整為較高的服務層級與計算大小。 然後，您可以在匯入成功後相應減少。

> [!NOTE]
> 資料庫匯入後的相容性層級會以來源資料庫的相容性層級為基礎。
> [!IMPORTANT]
> 匯入資料庫後，您可以選擇於目前的相容性層級 (針對 AdventureWorks2008R2 資料庫為層級 100) 或更高層級運作資料庫。 如需於特定相容性層級操作資料庫的含意與選項詳細資訊，請參閱 [ALTER DATABASE 相容性層級 (ALTER DATABASE Compatibility Level)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。 如需相容性層級其他相關資料庫等級設定的資訊，另請參閱 [ALTER DATABASE 範圍組態 (ALTER DATABASE SCOPED CONFIGURATION)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>在 Azure 入口網站中從 BACPAC 檔案匯入

[Azure 入口網站](https://portal.azure.com)「僅」  支援在 Azure SQL Database 中建立單一資料庫，且「僅能」  從儲存在 Azure Blob 儲存體中的 BACPAC 檔案建立。

> [!NOTE]
> [受控執行個體](sql-database-managed-instance.md)目前不支援使用 Azure 入口網站從 BACPAC 檔案將資料庫移轉到執行個體資料庫中。 若要匯入至受控執行個體，請使用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 處理匯入/匯出要求透過入口網站或 Powershell 提交的機器需要儲存 bacpac 檔案，以及資料層應用程式架構 (DacFX) 所產生的暫存檔案。 所需的磁碟空間而異大幅資料庫使用相同的大小，並可能需要最多 3 次的資料庫大小。 執行僅限匯入/匯出要求機器有 450 GB 本機的磁碟空間。 因此，某些要求可能會失敗，「 沒有足夠的空間磁碟上 「 錯誤。 在此情況下，因應措施是在本機的磁碟空間不足的電腦上執行 sqlpackage.exe。 匯入/匯出時大於 150 GB 的資料庫，請使用[SqlPackage](#import-from-a-bacpac-file-using-sqlpackage)若要避免這個問題。
 
1. 若要使用 Azure 入口網站從 BACPAC 檔案匯入至單一資料庫，請開啟適當的資料庫伺服器頁面，然後在工具列上選取 [匯入資料庫]  。  

   ![資料庫匯入1](./media/sql-database-import/import1.png)

2. 選取 BACPAC 檔案的儲存體帳戶及容器，然後選取要匯入的 BACPAC 檔案。
3. 指定新資料庫的大小 (通常與原始資料庫相同)，並提供目的地 SQL Server 認證。 如需新 Azure SQL 資料庫可能之值的清單，請參閱[建立資料庫](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。

   ![資料庫匯入2](./media/sql-database-import/import2.png)

4. 按一下 [確定]  。

5. 若要監視匯入進度，請開啟資料庫的伺服器頁面，然後在 [設定]  下選取 [匯入/匯出記錄]  。 匯入成功時，會處於 [已完成]  狀態。

   ![資料庫匯入狀態](./media/sql-database-import/import-status.png)

6. 若要確認資料庫伺服器上的資料庫為線上狀態，請選取 [SQL 資料庫]  ，並確認新的資料庫為 [線上]  狀態。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 從 BACPAC 檔案匯入

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯入 SQL Server 資料庫，請參閱[匯入參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 具有最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和[適用於 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)。 您也可以從 Microsoft 下載中心下載最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

為了規模和效能，我們建議在大部分生產環境中使用 SqlPackage，而不使用 Azure 入口網站。 如需 SQL Server 客戶諮詢小組部落格中有關使用 `BACPAC` 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) \(英文\)。

基於調整能力和效能，建議在大部分的生產環境中均應使用 SqlPackage。 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

下列 SqlPackage 命令會將 **AdventureWorks2008R2** 資料庫從本機儲存體匯入至名為 **mynewserver20170403** 的 Azure SQL Database 伺服器。 此命令會建立名為 **myMigratedDatabase**、且具有**進階**服務層級和 **P6** 服務目標的新資料庫。 請針對您的環境適當變更這些值。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要連線到從公司防火牆後方管理單一資料庫的 SQL Database 伺服器，該防火牆必須開啟連接埠 1433。 若要連線到受控執行個體，您必須有[點對站連線](sql-database-managed-instance-configure-p2s.md)或快速路由連線。
>

此範例說明如何使用 SqlPackage 與 Active Directory 通用驗證來匯入資料庫。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>使用 PowerShell 從 BACPAC 檔案匯入至單一資料庫

> [!NOTE]
> [受管理的執行個體](sql-database-managed-instance.md)目前不支援將資料庫移轉到執行個體資料庫從 BACPAC 檔案，使用 Azure PowerShell。 若要匯入至受控執行個體，請使用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 處理匯入/匯出要求透過入口網站或 Powershell 提交的機器需要儲存 bacpac 檔案，以及資料層應用程式架構 (DacFX) 所產生的暫存檔案。 所需的磁碟空間而異大幅資料庫使用相同的大小，並可能需要最多 3 次的資料庫大小。 執行僅限匯入/匯出要求機器有 450 GB 本機的磁碟空間。 因此，某些要求可能會失敗，「 沒有足夠的空間磁碟上 「 錯誤。 在此情況下，因應措施是在本機的磁碟空間不足的電腦上執行 sqlpackage.exe。 匯入/匯出時大於 150 GB 的資料庫，請使用[SqlPackage](#import-from-a-bacpac-file-using-sqlpackage)若要避免這個問題。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

使用 [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet 向 Azure SQL 数据库服务提交导入数据库请求。 匯入可能需要一些時間才能完成，視資料庫大小而定。

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 可以使用 [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet 检查导入的进度。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status:InProgress**。 當您看見 **Status:Succeeded** 時，即表示匯入已完成。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> 如需其他指令碼範例，請參閱[從 BACPAC 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

不支援匯入至彈性集區中的資料庫。 您可以將資料匯入到單一資料庫，然後將資料庫移到彈性集區。

## <a name="import-using-wizards"></a>使用精靈匯入

您也可以使用這些精靈。

- [SQL Server Management Studio 中的匯入資料層應用程式精靈](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 匯入和匯出精靈](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何連線及查詢已匯入的 SQL Database，請參閱[快速入門：Azure SQL Database：使用 SQL Server Management Studio 連線及查詢資料](sql-database-connect-query-ssms.md)。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)。
- 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-single-database-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
