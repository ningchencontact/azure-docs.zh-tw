---
title: 匯入 BACPAC 檔案以建立 Azure SQL Database | Microsoft Docs
description: 匯入 BACPAC 檔案以建立新的 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: 9e79aa2315118bcd9ce4328e74d51d7a22ea6247
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744543"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>快速入門：將 BACPAC 檔案匯入到新的 Azure SQL Database

您可以使用 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案 (副檔名為 `.bacpac` 的 zip 檔案，其中擁有資料庫的中繼資料和資料) 將 SQL Server 資料庫移轉至 Azure SQL 資料庫。 您可以從 Azure Blob 儲存體匯入 BACPAC 檔案 (僅限標準儲存體)，或從內部部署位置中的本機儲存體匯入。 若要最大化匯入速度，您可以指定較高的服務層並計算大小 (例如 P6)。 然後，您可以在匯入成功後相應減少。 資料庫匯入後的相容性層級會以來源資料庫的相容性層級為基礎。

> [!IMPORTANT]
> 匯入資料庫後，您可以選擇於目前的相容性層級 (針對 AdventureWorks2008R2 資料庫為層級 100) 或更高層級運作資料庫。 如需於特定相容性層級操作資料庫的含意與選項詳細資訊，請參閱 [ALTER DATABASE 相容性層級 (ALTER DATABASE Compatibility Level)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。 如需相容性層級其他相關資料庫等級設定的資訊，另請參閱 [ALTER DATABASE 範圍組態 (ALTER DATABASE SCOPED CONFIGURATION)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>在 Azure 入口網站中從 BACPAC 檔案匯入

本節說明如何使用 [Azure 入口網站](https://portal.azure.com)從儲存於 Azure Blob 儲存體中的 BACPAC 檔案建立 Azure SQL 資料庫。 此入口網站*僅*支援從 Azure Blob 儲存體匯入 BACPAC 檔案。

> [!NOTE]
> [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)支援使用本文中的其他方法從 BACPAC 檔案匯入，但目前不支援使用 Azure 入口網站移轉。

若要在 Azure 入口網站中匯入資料庫，請針對將裝載匯入項目的邏輯伺服器開啟頁面，然後在工具列上選取 [匯入資料庫]。  

   ![資料庫匯入](./media/sql-database-import/import.png)

選取儲存體帳戶、容器，和您要匯入的 BACPAC 檔案。 指定新資料庫的大小 (通常與原始資料庫相同)，並提供目的地 SQL Server 認證。 

### <a name="monitor-imports-progress"></a>監視匯入進度

若要監視匯入進度，請為匯入的資料庫開啟邏輯伺服器頁面，然後在 [設定] 下選取 [匯入/匯出記錄]。 匯入成功時，會處於 [已完成] 狀態。

若要確認伺服器上的資料庫為線上狀態，請選取 [SQL 資料庫]，並確認新的資料庫為 [線上] 狀態。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 從 BACPAC 檔案匯入

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令列公用程式匯入 SQL Database，請參閱[匯入參數和屬性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 具有最新的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[適用於 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)。 您也可以從 Microsoft 下載中心下載最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

基於調整能力和效能，建議在大部分的生產環境中均應使用 SqlPackage。 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

下列 SqlPackage 命令會將 **AdventureWorks2008R2** 資料庫從本機儲存體匯入至名為 **mynewserver20170403** 的 Azure SQL Database 邏輯伺服器。 此命令會建立名為 **myMigratedDatabase**、且具有**進階**服務層和 **P6** 服務目標的新資料庫。 請針對您的環境適當變更這些值。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Azure SQL Database 邏輯伺服器會接聽連接埠 1433。 若要連線到公司防火牆後方的邏輯伺服器，該防火牆必須開啟此連接埠。
>

此範例說明如何使用 SqlPackage 與 Active Directory 通用驗證來匯入資料庫。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>使用 PowerShell 從 BACPAC 檔案匯入

使用 [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) Cmdlet 來提交匯入資料庫要求至 Azure SQL Database 服務。 匯入可能需要一些時間才能完成，視資料庫大小而定。

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 您可以使用 [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) Cmdlet 來查看匯入的進度。 如果在要求後立即執行此 Cmdlet，通常會傳回 **Status:InProgress**。 當您看見 **Status:Succeeded** 時，即表示匯入已完成。

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
如需其他指令碼範例，請參閱[從 BACPAC 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

不支援匯入至彈性集區中的資料庫。 您可以將資料匯入到單一資料庫，然後將資料庫移到集區。

## <a name="import-using-wizards"></a>使用精靈匯入

您也可以使用這些精靈。

- [SQL Server Management Studio 中的匯入資料層應用程式精靈](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 匯入和匯出精靈](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何連線及查詢已匯入的 SQL Database，請參閱[快速入門：Azure SQL Database：使用 SQL Server Management Studio 連線及查詢資料](sql-database-connect-query-ssms.md)。
- 如需 SQL Server 客戶諮詢小組部落格中有關使用 BACPAC 檔案進行移轉的主題，請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。
- 如需有關整個 SQL Server 資料庫移轉程序的討論，包括效能建議，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。
- 若要了解如何管理和共用儲存體金鑰，以及安全地共用存取簽章，請參閱 [Azure 儲存體安全性指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
