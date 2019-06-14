---
title: Azure Cosmos DB API for MongoDB 的 azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立及設定適用於 MongoDB 的 Azure Cosmos DB API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968880"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>管理 Azure Cosmos DB MongoDB API 使用 Azure Resource Manager 範本的資源

## 建立 Azure Cosmos DB API for MongoDB 帳戶、 資料庫和集合 <a id="create-resource"></a>

建立使用 Azure Resource Manager 範本的 Azure Cosmos DB 資源。 此範本會建立 Azure Cosmos 帳戶適用於 MongoDB API 使用兩個共用 400 RU/秒的輸送量，在資料庫層級的集合。 複製範本和部署，如下所示，或瀏覽[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/)並從 Azure 入口網站部署。 您可以也將範本下載至本機電腦或建立新的範本並指定本機路徑`--template-file`參數。

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>透過 Azure CLI 進行部署

若要部署使用 Azure CLI Resource Manager 範本**複製**指令碼，然後選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

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

`az cosmosdb show`已佈建之後，命令會顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用在本機安裝的 Azure CLI 的版本，而不是使用 CloudShell，請參閱[Azure 命令列介面 (CLI)](/cli/azure/)文章。

## 更新資料庫上的輸送量 （RU/秒） <a id="database-ru-update"></a>

下列範本會更新資料庫的輸送量。 複製範本和部署，如下所示，或瀏覽[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/)並從 Azure 入口網站部署。 您可以也將範本下載至本機電腦或建立新的範本並指定本機路徑`--template-file`參數。

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>部署資料庫範本，透過 Azure CLI

若要部署使用 Azure CLI Resource Manager 範本，請選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## 更新集合上的輸送量 （RU/秒） <a id="collection-ru-update"></a>

下列範本會更新集合的輸送量。 複製範本和部署，如下所示，或瀏覽[Azure 快速入門資源庫](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/)並從 Azure 入口網站部署。 您可以也將範本下載至本機電腦或建立新的範本並指定本機路徑`--template-file`參數。

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>將集合範本透過 Azure CLI 部署

若要部署使用 Azure CLI Resource Manager 範本，請選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

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

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見的 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/resource-manager-common-deployment-errors.md)