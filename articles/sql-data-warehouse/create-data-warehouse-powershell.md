---
title: 快速入門：建立 Azure SQL 資料倉儲 - Azure Powershell | Microsoft Docs
description: 快速使用 Azure PowerShell 建立 SQL Database 邏輯伺服器、伺服器層級防火牆規則及資料倉儲。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 11/16/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 01a1c1fef5dd2dabf99677d59126caf41e1f6885
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900602"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>快速入門：如何使用 Azure PowerShell 建立和查詢 Azure SQL 資料倉儲

快速使用 Azure PowerShell 建立 Azure SQL 資料倉儲。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

> [!NOTE]
> 建立 SQL 資料倉儲可能會導致新的可計費服務。  如需詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登入 Azure 訂用帳戶並遵循畫面上的指示進行。

```powershell
Connect-AzAccount
```

若要查看您正在使用的訂用帳戶，請執行 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)。

```powershell
Get-AzSubscription
```

如果需要使用不同於預設值的訂用帳戶，請執行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>建立變數

定義此快速入門中的指令碼所使用的變數。

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>建立邏輯伺服器

使用 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) 命令建立 [Azure SQL 邏輯伺服器](../sql-database/sql-database-logical-servers.md)。 邏輯伺服器包含一組當作群組管理的資料庫。 下列範例會使用名為 `ServerAdmin` 的系統管理員使用者與密碼 `ChangeYourAdminPassword1` 在資源群組中建立隨機命名的伺服器。 視需要取代這些預先定義的值。

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>設定伺服器防火牆規則

使用 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) 命令建立 [Azure SQL 伺服器層級防火牆規則](../sql-database/sql-database-firewall-configure.md)。 伺服器層級防火牆規則可讓外部應用程式 (例如 SQL Server Management Studio 或 SQLCMD 公用程式) 穿過 SQL 資料倉儲服務防火牆連線到 SQL 資料倉儲。 在下列範例中，只會針對其他 Azure 資源開啟防火牆。 若要啟用外部連線，請將 IP 位址變更為適合您環境的地址。 若要開啟所有 IP 位址，請使用 0.0.0.0 作為起始 IP 位址，並使用 255.255.255.255 作為結束位址。

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database 和 SQL 資料倉儲透過連接埠 1433 進行通訊。 如果您嘗試從公司網路連線，您網路的防火牆可能不允許透過連接埠 1433 的連出流量。 如果是這樣，除非 IT 部門開放連接埠 1433，否則您將無法連線到您的 Azure SQL 伺服器。
>


## <a name="create-a-data-warehouse-with-sample-data"></a>使用範例資料建立資料倉儲
這個範例會使用先前定義的變數建立資料倉儲。  它會將服務目標指定為成本較低的資料倉儲起點 DW400。 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

必要參數如下：

* **RequestedServiceObjectiveName**：您要求的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)數量。 增加此數量會增加計算成本。 如需支援值的清單，請參閱[記憶體和並行存取限制](memory-and-concurrency-limits.md)。
* **DatabaseName**：您要建立之 SQL 資料倉儲的名稱。
* **ServerName**：您用來建立的伺服器名稱。
* **ResourceGroupName**：您使用的資源群組。 若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
* **版本**：必須是 "DataWarehouse"，才能建立 SQL 資料倉儲。

選擇性參數如下：

- **CollationName**：未指定定序時的預設值為 SQL_Latin1_General_CP1_CI_AS。 無法變更資料庫的定序。
- **MaxSizeBytes**：資料庫的預設大小上限為 10 GB。

如需有關參數選項的詳細資訊，請參閱 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)。


## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門教學課程會以此快速入門為基礎。 

> [!TIP]
> 如果您打算繼續進行後續的快速入門教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除此快速入門所建立的所有資源。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>後續步驟

您現在已建立資料倉儲、建立防火牆規則、連線到您的資料倉儲，並執行了一些查詢。 若要深入了解 Azure SQL 資料倉儲，請繼續進行載入資料的教學課程。
> [!div class="nextstepaction"]
>[將資料載入 SQL 資料倉儲](load-data-from-azure-blob-storage-using-polybase.md)
