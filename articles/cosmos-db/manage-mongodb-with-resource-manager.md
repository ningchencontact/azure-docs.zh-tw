---
title: 適用于 MongoDB 的 Azure Cosmos DB API Azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定適用于 MongoDB 的 Azure Cosmos DB API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 683da62ad78cde1a4f72b2ac0554e90d78b3ac6e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815023"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Azure Cosmos DB MongoDB API 資源

## 建立適用于 MongoDB 帳戶、資料庫和集合的 Azure Cosmos DB API<a id="create-resource"></a>

使用 Azure Resource Manager 範本建立 Azure Cosmos DB 資源。 此範本會建立適用于 MongoDB API 的 Azure Cosmos 帳戶, 其中包含兩個在資料庫層級共用 400 RU/秒輸送量的集合。 複製範本並如下所示部署, 或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/), 並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦, 或使用`--template-file`參數來建立新的範本, 並指定本機路徑。

> [!NOTE]
> 帳戶名稱必須是小寫, 且 < 31 個字元。

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>透過 Azure CLI 部署

若要使用 Azure CLI 部署 Resource Manager 範本, 請**複製**腳本, 然後選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

此`az cosmosdb show`命令會在布建完成後, 顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用本機安裝的 Azure CLI 版本, 而不是使用 CloudShell, 請參閱[Azure 命令列介面 (CLI)](/cli/azure/)一文。

## 更新資料庫上的輸送量 (RU/秒)<a id="database-ru-update"></a>

下列範本將會更新資料庫的輸送量。 複製範本並如下所示部署, 或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/), 並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦, 或使用`--template-file`參數來建立新的範本, 並指定本機路徑。

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>透過 Azure CLI 部署資料庫範本

若要使用 Azure CLI 部署 Resource Manager 範本, 請選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## 更新集合上的輸送量 (RU/秒)<a id="collection-ru-update"></a>

下列範本會更新集合的輸送量。 複製範本並如下所示部署, 或造訪[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/), 並從 Azure 入口網站進行部署。 您也可以將範本下載到本機電腦, 或使用`--template-file`參數來建立新的範本, 並指定本機路徑。

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>透過 Azure CLI 部署集合範本

若要使用 Azure CLI 部署 Resource Manager 範本, 請選取 [**試試看**] 以開啟 Azure Cloud shell。 若要貼上腳本, 請以滑鼠右鍵按一下 shell, 然後選取 [**貼**上]:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 檔](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者架構](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/resource-manager-common-deployment-errors.md)