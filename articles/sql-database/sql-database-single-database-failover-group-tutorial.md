---
title: 教學課程：將 Azure SQL Database 單一資料庫新增至容錯移轉群組 |Microsoft Docs
description: 使用 Azure 入口網站、PowerShell 或 Azure CLI, 將 Azure SQL Database 單一資料庫新增到容錯移轉群組。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 6cf688750ac73763c7f0da4eea152cf6bf0c8285
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935037"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>教學課程：將 Azure SQL Database 單一資料庫新增至容錯移轉群組

設定 Azure SQL Database 單一資料庫的容錯移轉群組, 並使用 Azure 入口網站、PowerShell 或 Azure CLI 測試容錯移轉。  在本教學課程中，您將了解如何：

> [!div class="checklist"]
> - 建立 Azure SQL Database 單一資料庫。
> - 在兩個邏輯 SQL server 之間建立單一資料庫的[容錯移轉群組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

## <a name="prerequisites"></a>先決條件

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
若要完成本教學課程，請確定您具有下列項目︰ 

- Azure 訂用帳戶。 如果您還沒有帳戶, 請[建立一個免費帳戶](https://azure.microsoft.com/free/)。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
若要完成本教學課程, 請確定您有下列專案:

- Azure 訂用帳戶。 如果您還沒有帳戶, 請[建立一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
若要完成本教學課程, 請確定您有下列專案:

- Azure 訂用帳戶。 如果您還沒有帳戶, 請[建立一個免費帳戶](https://azure.microsoft.com/free/)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)的最新版本。 

---

## <a name="1---create-a-single-database"></a>1-建立單一資料庫 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-建立容錯移轉群組 
在此步驟中, 您將在現有的 Azure SQL server 與另一個區域中的新 Azure SQL server 之間建立[容錯移轉群組](sql-database-auto-failover-group.md)。 然後將範例資料庫新增到容錯移轉群組。 

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
建立您的容錯移轉群組, 並使用 Azure 入口網站將您的單一資料庫新增至其中。 


1. 選取[Azure 入口網站](https://portal.azure.com)左上角的 [**所有服務**]。 
1. 在`sql servers` [搜尋] 方塊中輸入。 
1. 選擇性選取 [SQL Server 到我的最愛**sql server** ] 旁的星號圖示, 然後將它新增至左側導覽窗格。 
    
    ![尋找 SQL Server](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. 選取 [ **SQL server** ], 然後選擇您在第1節中建立`mysqlserver`的伺服器, 例如。
1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**], 然後選取 [**新增群組**] 以建立新的容錯移轉群組。 

    ![加入新的容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在 [**容錯移轉群組**] 頁面上, 輸入或選取下列值, 然後選取 [**建立**]:
    - **容錯移轉組名**:輸入唯一的容錯移轉組名, 例如`failovergrouptutorial`。 
    - **次要伺服器**:選取 [*設定必要設定*] 選項, 然後選擇 [**建立新的伺服器**]。 或者, 您也可以選擇已經存在的伺服器做為次要伺服器。 輸入下列值之後, 請選取 [**選取**]。 
        - **伺服器名稱**：輸入次要伺服器的唯一名稱, 例如`mysqlsecondary`。 
        - **伺服器管理員登入**：型`azureuser`
        - **密碼**：輸入複雜密碼以符合密碼需求。
        - **位置**：從下拉式選單中選擇 [位置], 例如 [美國東部 2]。 這個位置不能與您的主伺服器位於相同的位置。

    > [!NOTE]
    > 伺服器登入和防火牆設定必須符合您的主伺服器。 
    
      ![建立容錯移轉群組的次要伺服器](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **群組內的資料庫**:選取次要伺服器之後, 這個選項就會變成 [解除鎖定]。 選取此項以**選取要新增的資料庫**, 然後選擇您在第1節中建立的資料庫。 將資料庫新增到容錯移轉群組, 將會自動啟動異地複寫程式。 
        
    ![將 SQL DB 新增到容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
建立您的容錯移轉群組, 並使用 PowerShell 將您的單一資料庫新增至其中。 

   > [!NOTE]
   > 伺服器登入和防火牆設定必須符合您的主伺服器。 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
使用 AZ CLI 建立您的容錯移轉群組, 並將您的單一資料庫新增至其中。 

   > [!NOTE]
   > 伺服器登入和防火牆設定必須符合您的主伺服器。 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3-測試容錯移轉 
在此步驟中, 您會將容錯移轉群組容錯移轉到次要伺服器, 然後使用 Azure 入口網站進行容錯回復。 

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 入口網站測試容錯移轉。 

1. 在[Azure 入口網站](https://portal.azure.com)中, 流覽至您的**SQL** server 伺服器。 
1. 在 [**設定**] 窗格下選取 [**容錯移轉群組**], 然後選擇您在第2節中建立的容錯移轉群組。 
  
   ![從入口網站選取容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. 檢查哪一個伺服器是主要伺服器, 哪一個是次要伺服器。 
1. 從工作窗格中選取 [**容錯移轉**], 以容錯移轉包含範例單一資料庫的容錯移轉群組。 
1. 在通知您將會中斷 TDS 會話的警告上, 選取 **[是]** 。 

   ![容錯移轉包含 SQL 資料庫的容錯移轉群組](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. 檢查哪一個伺服器現在是主要的, 哪個伺服器是次要的。 如果故障通過成功, 這兩部伺服器應該會有已交換的角色。 
1. 再次選取 [**容錯移轉**], 讓伺服器無法回到原先的角色。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 測試容錯移轉。 


檢查次要複本的角色: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```


故障切換到次要伺服器: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to sucessfully to" $drServerName 
   ```

將容錯移轉群組還原至主要伺服器：

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
使用 AZ CLI 測試容錯移轉。 


確認哪一個伺服器是次要複本:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

故障切換到次要伺服器: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

將容錯移轉群組還原至主要伺服器：

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>清除資源 
藉由刪除資源群組來清除資源。 

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
使用 Azure 入口網站刪除資源群組。 


1. 在[Azure 入口網站](https://portal.azure.com)中, 流覽至您的資源群組。
1. 選取 [**刪除資源群組**] 以刪除群組中的所有資源, 以及資源群組本身。 
1. 在文字方塊中輸入資源群組`myResourceGroup`的名稱, 然後選取 [**刪除**] 以刪除資源群組。  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 刪除資源群組。 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
使用 AZ CLI 刪除資源群組。 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>完整腳本

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
沒有可供 Azure 入口網站使用的腳本。
 
---

您可以在這裡找到其他 Azure SQL Database 腳本:[Azure PowerShell](sql-database-powershell-samples.md)和[Azure CLI](sql-database-cli-samples.md)。 

## <a name="next-steps"></a>後續步驟

在本教學課程中, 您已將 Azure SQL Database 單一資料庫新增至容錯移轉群組, 並測試容錯移轉。 您已了解如何︰

> [!div class="checklist"]
> - 建立 Azure SQL Database 單一資料庫。 
> - 在兩個邏輯 SQL server 之間建立單一資料庫的[容錯移轉群組](sql-database-auto-failover-group.md)。
> - 測試容錯移轉。

前進到下一個教學課程, 以瞭解如何將彈性集區新增至容錯移轉群組。 

> [!div class="nextstepaction"]
> [教學課程：將 Azure SQL Database 彈性集區新增至容錯移轉群組](sql-database-elastic-pool-failover-group-tutorial.md)
