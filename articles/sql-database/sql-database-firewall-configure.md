---
title: IP 防火牆規則
description: Configure server-level IP firewall rules for a SQL database or SQL Data Warehouse firewall. Manage access and configure database-level IP firewall rules for a single or pooled database.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: ed13b5028341637d71dee95f38cc44cc91aa2376
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481435"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database and Azure SQL Data Warehouse IP firewall rules

> [!NOTE]
> This article applies to Azure SQL servers, and to both Azure SQL Database and Azure SQL Data Warehouse databases on an Azure SQL server. For simplicity, *SQL Database* is used to refer to both SQL Database and SQL Data Warehouse.

> [!IMPORTANT]
> 本文「不」適用於 *Azure SQL Database 受控執行個體*。 For information about network configuration, see [Connect your application to Azure SQL Database Managed Instance](sql-database-managed-instance-connect-app.md).

When you create a new Azure SQL server named *mysqlserver*, for example, the SQL Database firewall blocks all access to the public endpoint for the server (which is accessible at *mysqlserver.database.windows.net*).

> [!IMPORTANT]
> SQL Data Warehouse only supports server-level IP firewall rules. It doesn't support database-level IP firewall rules.

## <a name="how-the-firewall-works"></a>How the firewall works
Connection attempts from the internet and Azure must pass through the firewall before they reach your SQL server or SQL database, as the following diagram shows.

   ![Firewall configuration diagram][1]

### <a name="server-level-ip-firewall-rules"></a>伺服器層級 IP 防火牆規則

  這些規則可讓用戶端存取整個 Azure SQL Server，也就是相同 SQL Database 伺服器內的所有資料庫。 The rules are stored in the *master* database. You can have a maximum of 128 server-level IP firewall rules for an Azure SQL Server.
  
  You can configure server-level IP firewall rules by using the Azure portal, PowerShell, or Transact-SQL statements.
  - To use the portal or PowerShell, you must be the subscription owner or a subscription contributor.
  - To use Transact-SQL, you must connect to the SQL Database instance as the server-level principal login or as the Azure Active Directory administrator. (A server-level IP firewall rule must first be created by a user who has Azure-level permissions.)

### <a name="database-level-ip-firewall-rules"></a>資料庫層級 IP 防火牆規則

  這些規則可讓用戶端存取同部 SQL Database 伺服器內的特定 (安全) 資料庫。 You create the rules for each database (including the *master* database), and they're stored in the individual database.
  
  You can only create and manage database-level IP firewall rules for master and user databases by using Transact-SQL statements and only after you configure the first server-level firewall.
  
  If you specify an IP address range in the database-level IP firewall rule that's outside the range in the server-level IP firewall rule, only those clients that have IP addresses in the database-level range can access the database.
  
  對於資料庫，您最多可以有 128 個資料庫層級 IP 防火牆規則。 For more information about configuring database-level IP firewall rules, see the example later in this article and see [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Recommendations for how to set firewall rules

We recommend that you use database-level IP firewall rules whenever possible. This practice enhances security and makes your database more portable. Use server-level IP firewall rules for administrators. Also use them when you have many databases that have the same access requirements, and you don't want to configure each database individually.

> [!NOTE]
> 如需可讓業務持續運作的可攜式資料庫資訊，請參閱[災害復原的驗證需求](sql-database-geo-replication-security-config.md)。

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>伺服器層級與資料庫層級 IP 防火牆規則

*Should users of one database be fully isolated from another database?*

If *yes*, use database-level IP firewall rules to grant access. This method avoids using server-level IP firewall rules, which permit access through the firewall to all databases. That would reduce the depth of your defenses.

*Do users at the IP addresses need access to all databases?*

If *yes*, use server-level IP firewall rules to reduce the number of times that you have to configure IP firewall rules.

*Does the person or team who configures the IP firewall rules only have access through the Azure portal, PowerShell, or the REST API?*

If so, you must use server-level IP firewall rules. Database-level IP firewall rules can only be configured through Transact-SQL.  

*Is the person or team who configures the IP firewall rules prohibited from having high-level permission at the database level?*

If so, use server-level IP firewall rules. You need at least *CONTROL DATABASE* permission at the database level to configure database-level IP firewall rules through Transact-SQL.  

*Does the person or team who configures or audits the IP firewall rules centrally manage IP firewall rules for many (perhaps hundreds) of databases?*

In this scenario, best practices are determined by your needs and environment. 伺服器層級 IP 防火牆規則可能會比較容易設定，但是指令碼可以設定資料庫層級的規則。 And even if you use server-level IP firewall rules, you might need to audit database-level IP firewall rules to see if users with *CONTROL* permission on the database  create database-level IP firewall rules.

*Can I use a mix of server-level and database-level IP firewall rules?*

可以。 Some users, such as administrators, might need server-level IP firewall rules. 其他使用者 (例如資料庫應用程式的使用者) 可能需要資料庫層級 IP 防火牆規則。

### <a name="connections-from-the-internet"></a>從網際網路的連接

When a computer tries to connect to your database server from the internet, the firewall first checks the originating IP address of the request against the database-level IP firewall rules for the database that the connection requests.

- If the address is within a range that's specified in the database-level IP firewall rules, the connection is granted to the SQL database that contains the rule.
- If the address isn't within a range in the database-level IP firewall rules, the firewall checks the server-level IP firewall rules. If the address is within a range that's in the server-level IP firewall rules, the connection is granted. 伺服器層級 IP 防火牆規則適用於 Azure SQL Server 上的所有 SQL Database。  
- If the address isn't within a range that's in any of the database-level or server-level IP firewall rules, the connection request fails.

> [!NOTE]
> To access SQL Database from your local computer, ensure that the firewall on your network and local computer allow outgoing communication on TCP port 1433.

### <a name="connections-from-inside-azure"></a>Connections from inside Azure

To allow applications hosted inside Azure to connect to your SQL server, Azure connections must be enabled. When an application from Azure tries to connect to your database server, the firewall verifies that Azure connections are allowed. A firewall setting that has starting and ending IP addresses equal to *0.0.0.0* indicates that Azure connections are allowed. If the connection isn't allowed, the request doesn't reach the SQL Database server.

> [!IMPORTANT]
> This option configures the firewall to allow all connections from Azure, including connections from the subscriptions of other customers. If you select this option, make sure that your login and user permissions limit access to authorized users only.

## <a name="create-and-manage-ip-firewall-rules"></a>Create and manage IP firewall rules

You create the first server-level firewall setting by using the [Azure portal](https://portal.azure.com/) or programmatically by using [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule), or an Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). You create and manage additional server-level IP firewall rules by using these methods or Transact-SQL.

> [!IMPORTANT]
> Database-level IP firewall rules can only be created and managed by using Transact-SQL.

為提升效能，系統會暫時在資料庫層級快取伺服器層級 IP 防火牆規則。 若應重新整理快取，請參閱 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。

> [!TIP]
> 您可以使用 [SQL Database 稽核](sql-database-auditing.md)來稽核伺服器等級和資料庫等級防火牆的變更。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Use the Azure portal to manage server-level IP firewall rules

To set a server-level IP firewall rule in the Azure portal, go to the overview page for your Azure SQL database or your SQL Database server.

> [!TIP]
> 如需教學課程，請參閱[使用 Azure 入口網站建立 DB](sql-database-single-database-get-started.md)。

#### <a name="from-the-database-overview-page"></a>From the database overview page

1. To set a server-level IP firewall rule from the database overview page, select **Set server firewall** on the toolbar, as the following image shows. SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。

      ![Server IP firewall rule](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Select **Add client IP** on the toolbar to add the IP address of the computer that you're using, and then select **Save**. 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

      ![Set server-level IP firewall rule](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

#### <a name="from-the-server-overview-page"></a>From the server overview page

The overview page for your server opens. It shows the fully qualified server name (such as *mynewserver20170403.database.windows.net*) and provides options for further configuration.

1. To set a server-level rule from this page, select **Firewall** from the **Settings** menu on the left side.

2. Select **Add client IP** on the toolbar to add the IP address of the computer that you're using, and then select **Save**. 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Use Transact-SQL to manage IP firewall rules

| Catalog view or stored procedure | 層級 | 描述 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |資料庫 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |資料庫 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |資料庫 |移除資料庫層級 IP 防火牆規則 |

The following example reviews the existing rules, enables a range of IP addresses on the server *Contoso*, and deletes an IP firewall rule:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

接下來，新增伺服器層級 IP 防火牆規則。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

To delete a server-level IP firewall rule, execute the *sp_delete_firewall_rule* stored procedure. The following example deletes the rule *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Use PowerShell to manage server-level IP firewall rules 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all development is now for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az and AzureRm modules are substantially identical.

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |伺服器 |返回目前的伺服器層級防火牆規則 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |伺服器 |建立新的伺服器層級防火牆規則 |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |伺服器 |更新現有伺服器層級防火牆規則的屬性 |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |伺服器 |移除伺服器層級防火牆規則 |

The following example uses PowerShell to set a server-level IP firewall rule:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> For $servername specify the server name and not the fully qualified DNS name e.g. specify **mysqldbserver** instead of **mysqldbserver.database.windows.net**

> [!TIP]
> For PowerShell examples in the context of a quickstart, see [Create DB - PowerShell](sql-database-powershell-samples.md) and [Create a single database and configure a SQL Database server-level IP firewall rule using PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Use CLI to manage server-level IP firewall rules

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|伺服器|建立伺服器 IP 防火牆規則|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|伺服器|列出伺服器上的 IP 防火牆規則|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|伺服器|Shows the detail of an IP firewall rule|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|伺服器|Updates an IP firewall rule|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|伺服器|Deletes an IP firewall rule|

The following example uses CLI to set a server-level IP firewall rule:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> For $servername specify the server name and not the fully qualified DNS name e.g. specify **mysqldbserver** instead of **mysqldbserver.database.windows.net**

> [!TIP]
> For a CLI example in the context of a quickstart, see [Create DB - Azure CLI](sql-database-cli-samples.md) and [Create a single database and configure a SQL Database IP firewall rule using the Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Use a REST API to manage server-level IP firewall rules

| API | 層級 | 描述 |
| --- | --- | --- |
| [List firewall rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [Create or update firewall rules](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [Delete firewall rules](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [Get firewall rules](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 伺服器 | 取得伺服器層級 IP 防火牆規則 |

## <a name="troubleshoot-the-database-firewall"></a>Troubleshoot the database firewall

Consider the following points when access to the SQL Database service doesn't behave as you expect.

- **本機防火牆組態：**

  Before your computer can access SQL Database, you may need to create a firewall exception on your computer for TCP port 1433. To make connections inside the Azure cloud boundary, you may have to open additional ports. For more information, see the "SQL Database: Outside vs inside" section of [Ports beyond 1433 for ADO.NET 4.5 and SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Network address translation:**

  Because of network address translation (NAT), the IP address that's used by your computer to connect to SQL Database may be different than the IP address in your computer's IP configuration settings. To view the IP address that your computer is using to connect to Azure:
    1. 登入入口網站。
    1. Go to the **Configure** tab on the server that hosts your database.
    1. The **Current Client IP Address** is displayed in the **Allowed IP Addresses** section. Select **Add** for **Allowed IP Addresses** to allow this computer to access the server.

- **Changes to the allow list haven't taken effect yet:**

  There may be up to a five-minute delay for changes to the SQL Database firewall configuration to take effect.

- **The login isn't authorized, or an incorrect password was used:**

  If a login doesn't have permissions on the SQL Database server or the password is incorrect, the connection to the server is denied. Creating a firewall setting only gives clients an *opportunity* to try to connect to your server. The client must still provide the necessary security credentials. For more information about preparing logins, see [Controlling and granting database access to SQL Database and SQL Data Warehouse](sql-database-manage-logins.md).

- **動態 IP 位址：**

  If you have an internet connection that uses dynamic IP addressing and you have trouble getting through the firewall, try one of the following solutions:
  
  - Ask your internet service provider for the IP address range that's assigned to your client computers that access the SQL Database server. Add that IP address range as an IP firewall rule.
  - Get static IP addressing instead for your client computers. Add the IP addresses as IP firewall rules.

## <a name="next-steps"></a>後續步驟

- Confirm that your corporate network environment allows inbound communication from the compute IP address ranges (including SQL ranges) that are used by the Azure datacenters. You might have to add those IP addresses to the allow list. See [Microsoft Azure datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653).  
- For a quickstart about creating a server-level IP firewall rule, see [Create an Azure SQL database](sql-database-single-database-get-started.md).
- For help with connecting to an Azure SQL database from open-source or third-party applications, see [Client quickstart code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- For information about additional ports that you may need to open, see the "SQL Database: Outside vs inside" section of [Ports beyond 1433 for ADO.NET 4.5 and SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- For an overview of Azure SQL Database security, see [Securing your database](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
