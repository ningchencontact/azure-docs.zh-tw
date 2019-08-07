---
title: Azure Cosmos DB 的 Azure Resource Manager 範本資料表 API
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB 資料表 API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 4dd636be60233beafca8e8680551bd7c711a4ccc
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814865"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本管理 Azure Cosmos DB 資料表 API 資源

## 建立 Azure Cosmos 帳戶和資料表<a id="create-resource"></a>

使用 Azure Resource Manager 範本建立 Azure Cosmos DB 資源。 此範本會為資料表 API 建立 Azure Cosmos 帳戶, 其中有一個資料表的 400 RU/秒輸送量。 複製範本並如下所示部署, 或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/), 並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦, 或使用`--template-file`參數來建立新的範本, 並指定本機路徑。

> [!NOTE]
> 帳戶名稱必須是小寫, 且 < 31 個字元。

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>透過 PowerShell 部署

若要使用 PowerShell 部署 Resource Manager 範本, 請**複製**腳本, 然後選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

如果您選擇使用本機安裝的 PowerShell 版本, 而不是從 Azure Cloud shell, 您必須[安裝](/powershell/azure/install-az-ps)Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。

### <a name="deploy-via-azure-cli"></a>透過 Azure CLI 部署

若要使用 Azure CLI 部署 Resource Manager 範本, 請**複製**腳本, 然後選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

此`az cosmosdb show`命令會在布建完成後, 顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用本機安裝的 Azure CLI 版本, 而不是使用 CloudShell, 請參閱[Azure 命令列介面 (CLI)](/cli/azure/)一文。

## 更新資料表上的輸送量 (RU/秒)<a id="table-ru-update"></a>

下列範本會更新資料表的輸送量。 複製範本並如下所示部署, 或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/), 並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦, 或使用`--template-file`參數來建立新的範本, 並指定本機路徑。

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>透過 PowerShell 部署資料表輸送量

若要使用 PowerShell 部署 Resource Manager 範本, 請**複製**腳本, 然後選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>透過 Azure CLI 部署資料表範本

若要使用 Azure CLI 部署 Resource Manager 範本, 請選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 檔](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/resource-manager-common-deployment-errors.md)