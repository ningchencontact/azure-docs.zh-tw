---
title: 建立和管理使用 PowerShell 的 Azure Cosmos DB
description: 使用 Azure Powershell 管理您 Azure Cosmos DB 帳戶、 資料庫、 容器和輸送量。
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: f720b678f2c7a6e564ef3e8fa9ae071b004ed1a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243396"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>管理使用 PowerShell 的 Azure Cosmos DB SQL API 資源

下列指南說明如何使用 PowerShell 指令碼和自動化管理 Azure Cosmos db，包括帳戶、 資料庫、 容器和輸送量。 管理 Azure Cosmos DB 是不是透過 Azure Cosmos DB 專屬的指令程式，但與資源提供者，直接透過 AzResource cmdlet。 若要檢視的所有屬性，可以使用 PowerShell 為 Azure Cosmos DB 資源提供者來管理，請參閱[Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)

如需跨平台管理 Azure Cosmos DB，您可以使用[Azure CLI](manage-with-cli.md)，則[REST API][rp-rest-api]，或[Azure 入口網站](create-sql-api-dotnet.md#create-account)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>開始使用

請依照下列中的指示[如何安裝和設定 Azure PowerShell] [ powershell-install-configure]安裝並登入您在 Powershell 中的 Azure 帳戶。

* 如果您想要執行下列命令但不需要使用者確認，請在命令附加 `-Force` 旗標。
* 下列所有命令都是同步的。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 帳戶

下列各節示範如何管理 Azure Cosmos 帳戶，包括：

* [建立 Azure Cosmos 帳戶](#create-account)
* [更新 Azure Cosmos 帳戶](#update-account)
* [取得 Azure Cosmos 帳戶](#get-account)
* [刪除 Azure Cosmos 帳戶](#delete-account)
* [Azure Cosmos 帳戶更新標籤](#update-tags)
* [Azure Cosmos 帳戶的清單金鑰](#list-keys)
* [重新產生 Azure Cosmos 帳戶金鑰](#regenerate-keys)
* [列出 Azure Cosmos 帳戶的連接字串](#list-connection-strings)
* [修改 Azure Cosmos 帳戶的容錯移轉優先順序](#modify-failover-priority)

### <a id="create-account"></a> 建立 Azure Cosmos 帳戶

此命令可讓您建立 Azure Cosmos DB 資料庫帳戶。 將新的資料庫帳戶設定為單一區域或有特定[一致性原則](consistency-levels.md)的[多重區域][distribute-data-globally]。

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Azure Cosmos 帳戶的名稱。 必須是小寫，接受 英數字元及 '-' 字元，且介於 3 到 50 個字元之間。
* `$location` Azure Cosmos 帳戶的位置。
* `$locations` 資料庫帳戶之複本區域。 必須要有一個寫入區域，每個資料庫帳戶的容錯移轉優先順序值為 0。
* `$consistencyPolicy` Azure Cosmos 帳戶的預設一致性層級。 如需詳細資訊，請參閱 [Azure Cosmos DB 的一致性層級](consistency-levels.md)。
* `$CosmosDBProperties` 屬性值傳遞給 Cosmos DB Azure Resource Manager 提供者，佈建帳戶。

Azure Cosmos 帳戶可以設定的 IP 防火牆與虛擬網路服務結束點。 如需如何設定適用於 Azure Cosmos DB 的 IP 防火牆的詳細資訊，請參閱[設定的 IP 防火牆](how-to-configure-firewall.md)。  如需有關如何啟用適用於 Azure Cosmos DB 的服務端點的詳細資訊，請參閱 <<c0> [ 可以設定從虛擬網路存取](how-to-configure-vnet-service-endpoint.md)。

### <a id="get-account"></a> 取得 Azure Cosmos 帳戶的屬性

此命令可讓您取得現有的 Azure Cosmos 帳戶的屬性。

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> 更新 Azure Cosmos 帳戶

此命令可讓您更新 Azure Cosmos DB 資料庫帳戶屬性。 可更新的屬性包括：

* 新增或移除區域
* 變更預設的一致性原則
* 變更容錯移轉原則
* 變更 IP 範圍篩選器
* 變更虛擬網路組態
* 啟用多重主機

> [!NOTE]
> 此命令可讓您新增及移除區域，但不允許您修改容錯移轉優先順序。 若要修改容錯移轉優先順序，請參閱[修改 Azure Cosmos 帳戶的容錯移轉優先順序](#modify-failover-priority)。

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> 刪除 Azure Cosmos 帳戶

此命令可讓您刪除現有的 Azure Cosmos 帳戶。

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> 更新 Azure Cosmos 帳戶的標記

下列範例說明如何設定[Azure 資源標記][ azure-resource-tags] Azure Cosmos 帳戶。

> [!NOTE]
> 此命令可以透過附加 `-Tags` 旗標與對應的參數，來結合建立或更新命令。

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> 列出帳戶金鑰

當您建立 Azure Cosmos DB 帳戶時，服務會產生兩個主要存取金鑰，用於存取 Azure Cosmos DB 帳戶時的驗證。 透過提供這兩個存取金鑰，Azure Cosmos DB 讓您可以重新產生金鑰，同時又不需中斷 Azure Cosmos DB 帳戶。 也提供驗證唯讀作業的唯讀金鑰。 有兩個讀寫金鑰 (主要和次要) 和兩個唯讀金鑰 (主要和次要)。

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> 列出連接字串

對於 MongoDB 帳戶，您可以使用下列命令來擷取將您的 MongoDB 應用程式連線到資料庫帳戶的連接字串。

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> 重新產生帳戶金鑰

Azure Cosmos 帳戶存取金鑰應該定期重新產生，才能讓連線更加安全。 主要和次要存取金鑰會指派給帳戶。 這可讓用戶端，以維持存取權，而其他重新產生。 有四種類型的索引鍵 （主要、 次要、 PrimaryReadonly 和 SecondaryReadonly） 的 Azure Cosmos 帳戶

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> 修改容錯移轉優先順序

對於多重區域資料庫帳戶，您可以變更 Azure Cosmos DB 資料庫帳戶存在於不同區域的容錯移轉優先權。 如需有關 Azure Cosmos DB 資料庫帳戶中容錯移轉的詳細資訊，請參閱[使用 Azure Cosmos DB 全域散發資料][distribute-data-globally]。

下列範例中，假設的帳戶具有目前的容錯移轉優先順序的 westus = 0 和 eastus = 1。 下面這個範例會反轉的區域。

> [!CAUTION]
> 這項作業會觸發您的帳戶以新區域的 failoverPriority 為 0 時的手動容錯移轉。

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos Database

下列各節示範如何管理 Azure Cosmos 資料庫，包括：

* [建立 Azure Cosmos 資料庫](#create-db)
* [建立 Azure Cosmos 資料庫共用的輸送量](#create-db-ru)
* [列出帳戶中的所有 Azure Cosmos 資料庫](#get-all-db)
* [取得單一 Azure Cosmos 資料庫](#get-db)
* [刪除 Azure Cosmos 資料庫](#delete-db)

### <a id="create-db"></a>建立 Azure Cosmos 資料庫

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>建立 Azure Cosmos 資料庫共用的輸送量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-all-db"></a>取得帳戶中的所有 Azure Cosmos 資料庫

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>取得單一 Azure Cosmos 資料庫

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>刪除 Azure Cosmos 資料庫

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

下列各節示範如何管理 Azure Cosmos 的容器，包括：

* [建立 Azure Cosmos 容器](#create-container)
* [建立 Azure Cosmos 容器與共用的輸送量](#create-container-ru)
* [建立 Azure Cosmos 容器與自訂編製索引](#create-container-custom-index)
* [建立 Azure Cosmos 容器與編製索引已關閉](#create-container-no-index)
* [建立 Azure Cosmos 容器具有唯一的索引鍵和 TTL](#create-container-unique-key-ttl)
* [建立 Azure Cosmos 容器與衝突解決](#create-container-lww)
* [列出資料庫中的所有 Azure Cosmos 容器](#list-all-container)
* [取得資料庫中的單一 Azure Cosmos 容器](#get-container)
* [刪除 Azure Cosmos 容器](#delete-container)

### <a id="create-container"></a>建立 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-ru"></a>建立 Azure Cosmos 容器與共用的輸送量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>使用自訂索引原則中建立 Azure Cosmos 容器

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>建立 Azure Cosmos 容器與編製索引已關閉

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>建立 Azure Cosmos 容器唯一索引鍵原則與 TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    }; 
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>建立 Azure Cosmos 容器與衝突解決

若要建立使用預存程序的衝突解決原則，設定`"mode"="custom"`並設定為預存程序中，名稱的解析路徑`"conflictResolutionPath"="myResolverStoredProcedure"`。 若要寫入 ConflictsFeed 中的所有衝突，並分別控制代碼，設定`"mode"="custom"`和 `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins"; 
            "conflictResolutionPath"="/myResolutionPath"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-all-container"></a>列出資料庫中的所有 Azure Cosmos 容器

```azurepowershell-interactive
# List all Azure Cosmos containers in a database 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>取得資料庫中的單一 Azure Cosmos 容器

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>刪除 Azure Cosmos 容器

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>後續步驟

* [所有的 PowerShell 範例](powershell-samples.md)
* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [建立 Azure Cosmos 容器](how-to-create-container.md)
* [設定 Azure Cosmos DB 中的 存留時間](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/