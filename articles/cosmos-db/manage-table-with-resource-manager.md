---
title: 適用於 Azure Cosmos DB 資料表 API 的 azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB 資料表 API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968818"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>管理 Azure Cosmos DB 資料表 API 使用 Azure Resource Manager 範本的資源

## 建立 Azure Cosmos 帳戶和資料表 <a id="create-resource"></a>

建立使用 Azure Resource Manager 範本的 Azure Cosmos DB 資源。 此範本會建立 Azure Cosmos 帳戶，與在 400 RU/秒的輸送量的一個資料表的資料表 api。 複製範本和部署，如下所示，或瀏覽[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/)並從 Azure 入口網站部署。 您可以也將範本下載至本機電腦或建立新的範本並指定本機路徑`--template-file`參數。

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>透過 PowerShell 進行部署

若要部署使用 PowerShell、 Resource Manager 範本**複製**指令碼，然後選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

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

如果您選擇要使用的 PowerShell，而不是從 Azure Cloud shell 在本機安裝的版本，您必須[安裝](/powershell/azure/install-az-ps)Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。

### <a name="deploy-via-azure-cli"></a>透過 Azure CLI 進行部署

若要部署使用 Azure CLI Resource Manager 範本**複製**指令碼，然後選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

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

`az cosmosdb show`已佈建之後，命令會顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用在本機安裝的 Azure CLI 的版本，而不是使用 CloudShell，請參閱[Azure 命令列介面 (CLI)](/cli/azure/)文章。

## 更新資料表上的輸送量 （RU/秒） <a id="table-ru-update"></a>

下列範本會更新資料表的輸送量。 複製範本和部署，如下所示，或瀏覽[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/)並從 Azure 入口網站部署。 您可以也將範本下載至本機電腦或建立新的範本並指定本機路徑`--template-file`參數。

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>將資料表的輸送量，透過 PowerShell 部署

若要部署使用 PowerShell、 Resource Manager 範本**複製**指令碼，然後選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

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

### <a name="deploy-table-template-via-azure-cli"></a>部署 透過 Azure CLI 的資料表範本

若要部署使用 Azure CLI Resource Manager 範本，請選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

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

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見的 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/resource-manager-common-deployment-errors.md)