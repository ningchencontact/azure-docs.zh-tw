---
title: 使用 Azure CLI 管理 Azure 資源管理員群組 |Microsoft Docs
description: 使用 Azure CLI 管理 Azure 资源管理器组。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c9e6cc68cbd629642849d323d4271722f3232aa3
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824933"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 资源管理器资源组

了解如何将 Azure CLI 与 [Azure 资源管理器](resource-group-overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure CLI 管理 Azure 资源](./manage-resources-cli.md)。

有关资源组管理的其他文章：

- [使用 Azure 门户管理 Azure 资源组](./manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 资源组](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

資源群組是存放 Azure 方案相關資源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 指定资源组的位置时，也会指定元数据的存储位置。

## <a name="create-resource-groups"></a>建立資源群組

以下 CLI 脚本将创建一个资源组，然后显示该资源组。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>列出資源群組

以下 CLI 脚本将列出订阅下的资源组。

```azurecli-interactive
az group list
```

若要获取某个资源组，请执行以下操作：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>删除资源组

以下 CLI 脚本删除某个资源组：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

若要详细了解 Azure 资源管理器如何控制资源的删除，请参阅 [Azure 资源管理器资源组的删除](./resource-group-delete.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有的资源组

请参阅[将资源部署到现有的资源组](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以使用资源管理器模板创建一个资源组并将资源部署到该组。 如需詳細資訊，請參閱[建立資源群組並部署資源](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)。

## <a name="move-to-another-resource-group-or-subscription"></a>移到另一个资源组或订阅

可将组中的资源移到另一个资源组。 有关详细信息，请参阅[移动资源](./manage-resources-cli.md#move-resources)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可以防止组织中的其他用户意外删除或修改关键资源，例如 Azure 订阅、资源组或资源。 

以下脚本锁定了一个资源组，因此无法删除该资源组。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

以下脚本获取资源组的所有锁：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

以下脚本将删除锁：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

如需詳細資訊，請參閱[使用 Azure Resource Manager 來鎖定資源](resource-group-lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 有关信息，请参阅[使用标记组织 Azure 资源](./resource-group-using-tags.md#azure-cli)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

成功设置资源组后，可能需要查看资源组的资源管理器模板。 匯出此範本有兩個優點︰

- 由于模板包含整个基础结构，因此将来可以自动完成解决方案的部署。
- 通过查看代表解决方案的 JavaScript 对象表示法 (JSON)，了解模板语法。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

该脚本在控制台上显示模板。  複製 JSON，並儲存為檔案。

有关详细信息，请参阅[导出资源组](./manage-resource-groups-portal.md#export-resource-groups-to-templates)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

[角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 是您對 Azure 中的資源存取進行管理的機制。 有关详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>後續步驟

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。