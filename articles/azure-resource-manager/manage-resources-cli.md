---
title: 使用 Azure CLI 管理 Azure 資源 |Microsoft Docs
description: 使用 Azure CLI 和 Azure Resource Manager 來管理您的資源。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3293d08f63cf573c7833fae5dd15bfe3119fd6c7
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206518"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 資源

了解如何使用 Azure CLI 搭配[Azure Resource Manager](resource-group-overview.md)來管理您的 Azure 資源。 如需管理資源群組，請參閱[使用 Azure CLI 管理 Azure 資源群組](./manage-resource-groups-cli.md)。

關於管理資源的其他文章：

- [使用 Azure 入口網站管理 Azure 資源](./manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 資源](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>將資源部署至現有的資源群組

您可以直接透過使用 Azure PowerShell 部署 Azure 資源，或部署 Resource Manager 範本來建立 Azure 資源。

### <a name="deploy-a-resource"></a>部署資源

下列指令碼會建立儲存體帳戶。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>部署範本

下列指令碼會建立部署快速入門範本，以建立儲存體帳戶。 如需詳細資訊，請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](./resource-group-template-deploy-cli.md)。

## <a name="deploy-a-resource-group-and-resources"></a>部署資源群組和資源

您可以建立資源群組，並將資源部署至該群組。 如需詳細資訊，請參閱[建立資源群組並部署資源](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>將資源部署至多個訂用帳戶或資源群組

一般而言，您要將範本中的所有資源部署至單一資源群組。 不過，在某些情況下，您要將一組資源部署在一起，但將它們放在不同的資源群組或訂用帳戶中。 如需詳細資訊，請參閱 <<c0> [ 部署 Azure 資源部署至多個訂用帳戶或資源群組](./resource-manager-cross-resource-group-deployment.md)。

## <a name="delete-resources"></a>刪除資源

下列指令碼會示範如何刪除儲存體帳戶。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

如需 Azure Resource Manager 如何排序資源的刪除作業的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](./resource-group-delete.md)。

## <a name="move-resources"></a>移動資源

下列指令碼會示範如何從一個資源群組中移除儲存體帳戶，另一個資源群組。

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## <a name="lock-resources"></a>鎖定資源

鎖定可防止不小心刪除或修改重要資源，例如 Azure 訂用帳戶、 資源群組或資源組織中的其他使用者。 

下列指令碼會鎖定的儲存體帳戶，因此無法刪除帳戶。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

下列指令碼會取得儲存體帳戶的所有鎖定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

下列指令碼會刪除儲存體帳戶的鎖定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](resource-group-lock-resources.md)。

## <a name="tag-resources"></a>標記資源

標記以邏輯方式組織您的資源群組和資源的協助。 如需資訊，請參閱[使用標記來組織您的 Azure 資源](./resource-group-using-tags.md#azure-cli)。

## <a name="manage-access-to-resources"></a>管理對資源的存取

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 如需詳細資訊，請參閱 <<c0> [ 使用 RBAC 和 Azure CLI 管理存取](../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Resource Manager，請參閱[Azure Resource Manager 概觀](./resource-group-overview.md)。
- 若要深入了解 Resource Manager 範本語法，請參閱[了解的結構和 Azure Resource Manager 範本的語法](./resource-group-authoring-templates.md)。
- 若要了解如何開發的範本，請參閱[逐步教學課程](/azure/azure-resource-manager/)。
- 若要檢視 Azure Resource Manager 範本結構描述，請參閱[範本參考](/azure/templates/)。
