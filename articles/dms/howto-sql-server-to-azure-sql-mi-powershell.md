---
title: 搭配資料庫移轉服務和 PowerShell 將 SQL Server 移轉至 Azure SQL Database 受控執行個體 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 從內部部署 SQL Server 移轉至 Azure SQL DB 受控執行個體。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 96ee3f5e1b3cfe67cb75e50c6247e41f0d901393
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867909"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>將 SQL Server 內部部署移轉至 Azure SQL Database 受控執行個體使用 Azure PowerShell
在本文中，您將移轉**Adventureworks2016**資料庫還原到內部部署 SQL Server 2005 執行個體，或上述到 Azure SQL Database 受控執行個體使用 Microsoft Azure PowerShell。 您可以從內部部署 SQL Server 執行個體移轉資料庫，Azure SQL Database 受控執行個體，使用`Az.DataMigration`中 Microsoft Azure PowerShell 模組。

在本文中，您將了解：
> [!div class="checklist"]
>
> * 建立資源群組。
> * 建立 Azure 資料庫移轉服務執行的個體。
> * 建立移轉專案中的 Azure 資料庫移轉服務執行個體。
> * 執行移轉。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

這篇文章包含有關如何執行線上和離線移轉的詳細資料。

## <a name="prerequisites"></a>必要條件

若要完成這些步驟，您需要：

* [SQL Server 2016 或更新版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任何版本）。
* 本機副本**AdventureWorks2016**資料庫，這是可供下載[這裡](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)。
* 啟用 SQL Server Express 安裝預設已停用的 TCP/IP 通訊協定。 依照[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文的說明，啟用 TCP/IP 通訊協定。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
* Azure SQL Database 受控執行個體。 您可以建立 Azure SQL Database 受控執行個體中的發行項的詳細資料[建立 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。
* 若要下載並安裝[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更新版本。
* 建立使用 Azure Resource Manager 部署模型，提供給您的內部部署來源伺服器的站對站連線的 Azure 資料庫移轉服務使用 Azure 虛擬網路 (VNet) [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或是[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。
* 已完成的評定內部部署資料庫和結構描述移轉的文章中所述，使用 Data Migration Assistant[執行 SQL Server 移轉評估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)。
* 若要下載並安裝`Az.DataMigration`模組 (0.7.2 版或更新版本) 從使用 「 PowerShell 資源庫[Install-module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)。
* 若要確保用來連接至來源 SQL Server 執行個體的認證具有[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)權限。
* 為了確保用來連接到目標 Azure SQL Database 的認證受控執行個體，請對目標 Azure SQL Database 受控執行個體資料庫 CONTROL DATABASE 權限。

    > [!IMPORTANT]
    > 線上移轉，您必須已設定好您的 Azure Active Directory 認證。 如需詳細資訊，請參閱文章[使用入口網站建立 Azure AD 應用程式和服務主體可存取資源](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登入 Microsoft Azure 訂用帳戶

使用 PowerShell 來登入您的 Azure 訂用帳戶。 如需詳細資訊，請參閱文章[使用 Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用建立資源群組[ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令。

下列範例會建立名為的資源群組*myResourceGroup*中*美國東部*區域。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>建立 Azure 資料庫移轉服務執行的個體

您可以使用 `New-AzDataMigrationService` Cmdlet，來建立新的 Azure 資料庫移轉服務執行個體。
此 Cmdlet 預期有下列必要參數：

* Azure 資源群組名稱。 您可以使用[ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令來建立 Azure 資源群組，如先前所示，並將其做為參數的名稱。
* 服務名稱。 對應至 Azure 資料庫移轉服務所需的唯一服務名稱的字串。
* *位置*。 指定服務的位置。 指定 Azure 資料中心位置，例如美國西部或東南亞。
* SKU。 此參數會對應至 DMS SKU 名稱。 目前支援的 Sku 名稱*為 Basic_1vCore*， *Basic_2vCores*， *GeneralPurpose_4vCores*。
* 虛擬子網路識別碼。 您可以使用 cmdlet [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig)建立子網路。

下列範例會建立稱為*MyDMS*資源群組中*MyDMSResourceGroup*位於*美國東部*區域使用虛擬網路，名為*MyVNET*和名為的子網路*MySubnet*。

> [!IMPORTANT]
> 下列程式碼片段是離線移轉，不需要的進階 SKU 為基礎的 Azure 資料庫移轉服務執行個體。 如需線上移轉，-Sku 參數的值必須包含進階 SKU。

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>建立移轉專案

建立 Azure 資料庫移轉服務執行個體之後，建立移轉專案。 Azure 資料庫移轉服務專案需要來源和目標執行個體的連線資訊，以及您想要移轉的資料庫清單。

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>建立來源和目標連線的資料庫連接資訊物件

您可以使用，以建立資料庫連接資訊物件`New-AzDmsConnInfo`cmdlet，這會需要下列參數：

* ServerType。 要求的資料庫連接類型，例如 SQL、Oracle 或 MySQL。 使用 SQL Server 的 SQL 和 Azure SQL。
* DataSource。 名稱或 SQL Server 執行個體或 Azure SQL Database 執行個體的 IP。
* AuthType。 連線的驗證類型，可以是 SqlAuthentication 或 WindowsAuthentication。
* *TrustServerCertificate*。 此參數設定值，指出通道是否會加密，同時略過驗證信任的憑證鏈結查核。 此值可以是`$true`或`$false`。

下列範例會建立名為 SQL Server 來源的連接資訊物件*MySourceSQLServer*使用 sql 驗證：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一個範例顯示名為 'targetmanagedinstance.database.windows.net' 使用 sql 驗證的 Azure SQL Database 受控執行個體伺服器建立連線資訊：

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>提供移轉專案所用的資料庫

建立一份`AzDataMigrationDatabaseInfo`物件，指定資料庫的 Azure 資料庫移轉服務專案中，可以當成參數提供建立專案的一部分。 您可以使用 cmdlet`New-AzDataMigrationDatabaseInfo`來建立`AzDataMigrationDatabaseInfo`。

下列範例會建立`AzDataMigrationDatabaseInfo`專案**AdventureWorks2016**資料庫，並將它加入至清單，以建立專案，做為參數提供。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>建立專案物件

最後，您可以建立稱為的 Azure 資料庫移轉服務專案*且名為 MyDMSProject*位於*美國東部*使用`New-AzDataMigrationProject`並新增先前建立的來源和目標連線，要移轉之資料庫的清單。

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>建立並啟動移轉工作

接下來，建立並啟動 Azure 資料庫移轉服務工作。 這項工作需要連線認證資訊的來源和目標，以及要移轉的資料庫資料表的清單和已提供做為必要條件中建立專案資訊。

### <a name="create-credential-parameters-for-source-and-target"></a>建立來源和目標的認證參數

建立連線安全性認證[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)物件。

下列範例顯示如何建立*PSCredential*物件的來源和目標連線，密碼提供為字串變數 *$sourcePassword*和 *$targetPassword*。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>建立備份的檔案共用物件

現在，建立檔案共用物件，表示本機 SMB 網路共用的 Azure 資料庫移轉服務可放置來源資料庫備份使用`New-AzDmsFileShare`cmdlet。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>建立選取的資料庫物件

下一個步驟是選取的來源和目標資料庫使用`New-AzDmsSelectedDB`cmdlet。

下列範例是從 SQL Server 的單一資料庫移轉至 Azure SQL Database 受控執行個體：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整個 SQL Server 執行個體需要隨即轉移至 Azure SQL Database 受控執行個體，然後如下所示的迴圈，讓所有的資料庫來源的資料。 在下列範例中，針對 $Server、 $SourceUserName 和 $SourcePassword，提供您的來源 SQL Server 詳細資料。

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure 儲存體容器的 SAS URI

建立包含 SAS URI 的變數，讓 Azure 資料庫管理服務能夠存取此服務將備份檔案上傳到的儲存體帳戶容器。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>其他組態需求

有幾個額外的需求，您需要解決，但取決於您是否執行離線或線上移轉它們之間的差異。

#### <a name="offline-migrations"></a>離線移轉

只有離線移轉，執行下列額外設定工作。

* **選取 登入**。 建立一份登入，以便移轉在下列範例所示：

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > 目前，Azure 資料庫移轉服務僅支援移轉的 SQL 登入。

* **選取代理程式作業**。 建立代理程式的清單作業，會移轉，如下列範例所示：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 資料庫移轉服務僅支援 T-SQL 子系統的作業步驟的作業。

#### <a name="online-migrations"></a>線上移轉

僅線上移轉，執行下列額外設定工作。

* **設定 Azure Active Directory 應用程式**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **設定儲存體資源**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>建立並啟動移轉工作

使用 `New-AzDataMigrationTask` Cmdlet 建立並啟動移轉工作。

#### <a name="specify-parameters"></a>指定參數

##### <a name="common-parameters"></a>一般參數

不論執行離線或線上移轉， `New-AzDataMigrationTask` cmdlet 預期有下列參數：

* TaskType。 要為從 SQL Server 到 Azure SQL Database 受控執行個體的移轉建立的移轉工作類型，預期為 MigrateSqlServerSqlDbMi。 
* 資源群組名稱。 要在其中建立工作的資源群組名稱。
* ServiceName。 要在其中建立工作的 Azure 資料庫移轉服務執行個體。
* ProjectName。 要在其中建立工作的 Azure 資料庫移轉服務專案名稱。 
* TaskName。 要建立的工作名稱。 
* *SourceConnection*。 AzDmsConnInfo 物件，代表來源 SQL Server 連接。
* *TargetConnection*。 AzDmsConnInfo 物件，表示目標 Azure SQL Database 受控執行個體的連接。
* SourceCred。 連線至來源伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* TargetCred。 連線至目標伺服器所用的 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 物件。
* SelectedDatabase。 AzDataMigrationSelectedDB 物件，代表來源和目標資料庫對應。
* *BackupFileShare*。 此為 FileShare 物件，代表 Azure 資料庫移轉服務可移入來源資料庫備份的本機網路共用。
* *BackupBlobSasUri*。 此為 SAS URI，可讓 Azure 資料庫移轉服務存取此服務會將備份檔案上傳到的儲存體帳戶容器。 了解如何取得 Blob 容器的 SAS URI。
* *SelectedLogins*。 已選取要移轉的登入清單。
* *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。

##### <a name="additional-parameters"></a>其他參數

`New-AzDataMigrationTask` Cmdlet 也預期收到的唯一移轉離線或上線，您要執行的型別參數。

* **離線移轉**。 離線移轉， `New-AzDataMigrationTask` cmdlet 也預期要有下列參數：

  * *SelectedLogins*。 已選取要移轉的登入清單。
  * *SelectedAgentJobs*。 已選取要移轉的代理程式作業清單。

* **線上移轉**。 線上移轉， `New-AzDataMigrationTask` cmdlet 也預期要有下列參數。

* *AzureActiveDirectoryApp*。 Active Directory 應用程式。
* *StorageResourceID*。 儲存體帳戶資源識別碼。

#### <a name="create-and-start-an-offline-migration-task"></a>建立並啟動離線移轉工作

下列範例會建立並啟動名為 「 離線移轉任務**myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>建立並啟動線上移轉工作

下列範例會建立並啟動名為 「 線上移轉任務**myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>監視移轉

若要監視的移轉，執行下列工作。

1. 將所有移轉詳細資料都合併成一個名為 $CheckTask 變數中。

    若要結合移轉詳細資料，例如屬性、 狀態和移轉時，相關聯的資料庫資訊，請使用下列程式碼片段：

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 使用`$CheckTask`變數以取得移轉工作的目前狀態。

    若要使用`$CheckTask`變數來取得移轉工作的目前狀態，您可以監視移轉工作執行的查詢工作的 state 屬性，如下列範例所示：

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>執行完全移轉 （線上移轉作業只有）

透過線上移轉，執行完整備份和還原資料庫時，，然後工作上還原交易記錄檔儲存在 BackupFileShare。

當 Azure SQL Database 受控執行個體中的資料庫已包含最新的資料，並會與來源資料庫的同步處理時，您可以執行轉換。

下列範例將會完成 cutover\migration。 使用者叫用此命令，自行斟酌。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>刪除 Azure 資料庫移轉服務執行的個體

完成移轉之後，您可以刪除的 Azure 資料庫移轉服務執行個體：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他資源

如需其他移轉案例 （來源/目標配對） 的詳細資訊，請參閱 Microsoft[資料庫移轉指南](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>後續步驟

深入了解 Azure 資料庫移轉服務文中所[什麼是 Azure 資料庫移轉服務？](https://docs.microsoft.com/azure/dms/dms-overview)。
