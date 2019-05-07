---
title: 適用於 Azure Cosmos DB Cassandra API 的 azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB Cassandra API。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 9878939a461f3ba35d2b6c270de2a965f66204cc
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077541"
---
# <a name="create-azure-cosmos-db-cassandra-api-resources-from-a-resource-manager-template"></a>從 Resource Manager 範本建立 Azure Cosmos DB Cassandra API 資源

了解如何使用 Azure Resource Manager 範本的 Cassandra api 建立 Azure Cosmos 帳戶。 下列範例會建立 Azure Cosmos DB Cassandra API 帳戶[Azure 快速入門範本](https://aka.ms/cassandra-arm-qs)。 此範本會建立 Cassandra API 的 Azure Cosmos 帳戶，具有共用 400 RU/秒的輸送量，keyspace 層級的兩個資料表。 

以下是範本的複本：

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

若要部署使用 Azure CLI Resource Manager 範本**複製**指令碼，然後選取**試試**開啟 Azure Cloud shell 中。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”：

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show`已佈建之後，命令會顯示新建立的 Azure Cosmos 帳戶。 如果您選擇使用在本機安裝的 Azure CLI 的版本，而不是使用 CloudShell，請參閱[Azure 命令列介面 (CLI)](/cli/azure/)文章。

在上述範例中，您已參考儲存在 GitHub 中的範本。 您可以也將範本下載至本機電腦或建立新的範本並指定本機路徑`--template-file`參數。

## <a name="next-steps"></a>後續步驟

以下是一些其他資源：

- [Azure Resource Manager 文件](/azure/azure-resource-manager/)
- [Azure Cosmos DB 資源提供者結構描述](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [針對常見的 Azure Resource Manager 部署錯誤進行疑難排解](../azure-resource-manager/resource-manager-common-deployment-errors.md)