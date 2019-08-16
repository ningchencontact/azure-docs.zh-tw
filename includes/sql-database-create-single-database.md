---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "68444678"
---
在此步驟中，您將建立資源群組和 Azure SQL Database 單一資料庫。 

> [!IMPORTANT]
> 務必設定防火牆規則，使用您在本文中執行步驟的電腦所用的公用 IP 位址。 資料庫層級防火牆規則將會自動複寫到次要伺服器。
>
> 如需資訊，請參閱[建立資料庫層級防火牆規則](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)，或者，若要判斷對於電腦的伺服器層級防火牆規則所用的 IP 位址，請參閱[建立伺服器層級防火牆](../articles/sql-database/sql-database-server-level-firewall-rule.md)。  

# <a name="azure-portaltabazure-portal"></a>[Azure 入口網站](#tab/azure-portal)
使用 Azure 入口網站建立您的資源群組和單一資料庫。 

1. 選取 Azure 入口網站左上角的 [建立資源]。
2. 選取 [資料庫]，然後選取 [SQL Database] 以開啟 [建立 SQL Database] 頁面。

   ![建立單一資料庫](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. 在 [基本資料] 索引標籤的 [專案詳細資料] 區段中，輸入或選取下列值：

   - 訂用帳戶：下拉並選取正確的訂用帳戶 (若未出現)。
   - **资源组**：選取 [新建]，輸入 `myResourceGroup`，然後選取 [確定]。

     ![新的 SQL 資料庫 - 基本資料索引標籤](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. 在 [資料庫詳細資料] 區段中，輸入或選取下列值：

   - **資料庫名稱**：輸入 `mySampleDatabase` 。
   - **伺服器**：選取 [新建] 並輸入下列值，然後選取 [選取]。
       - **伺服器名稱**：`mysqlserver`類別；伴隨一些表示唯一性的數字。
       - **伺服器管理員登入**：輸入 `azureuser`。
       - **密碼**：輸入複雜密碼以符合密碼需求。
       - **位置**：從下拉式清單中選擇位置，例如 `West US 2`。

         ![新增伺服器](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > 請記得記錄下伺服器管理員登入和密碼，以便在進行這個和其他快速入門時能夠登入伺服器和資料庫。 如果您忘記登入或密碼，您可以在 [SQL Server] 頁面上取得登入名稱或重設密碼。 若要開啟 [SQL Server] 頁面，請在資料庫建立後，選取資料庫 [概觀] 頁面上的伺服器名稱。

        ![SQL Database 詳細資料](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **您要使用 SQL 彈性集區嗎**：選取 [否] 選項。
   - **計算 + 儲存體**：選取 [設定資料庫]，然後在本快速入門中，選取 [以虛擬核心為基礎的購買選項]

     ![V 核心形式的購買選項](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - 選取 [已布**建**] 和 [**第4代**]。

     ![無伺服器計算層](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - 檢閱 [虛擬核心數上限]、[虛擬核心數下限]、[自動暫停延遲] 和 [資料大小上限] 的設定。 視需要變更這些設定。
   - 接受預覽條款，然後按一下 [確定]。
   - 選取 [套用]。

5. 選取 [其他設定] 索引標籤。 
6. 在 [資料來源] 區段的 [使用現有資料] 下方，選取 `Sample`。 

   ![其他 SQL DB 設定](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > 請務必選取 [範例 (AdventureWorksLT)] 資料，以便依照本快速入門和使用這項資料的其他 Azure SQL Database 快速入門的指示操作。

7. 將其餘的值保留為預設值，然後在表單底部選取 [檢閱 + 建立]。
8. 檢閱最終設定，然後選取 [建立]。

9. 在 [SQL Database] 表單中，選取 [建立] 以部署和佈建資源群組、伺服器和資料庫。

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

使用 PowerShell 建立您的資源群組和單一資料庫。 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
使用 AZ CLI 建立您的資源群組和單一資料庫。 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---