---
title: 具有 Azure Resource Manager 範本的條件式部署
description: 描述如何有條件地在 Azure Resource Manager 範本中部署資源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259505"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Resource Manager 範本中的條件式部署

有時候, 您需要在範本中選擇性地部署資源。 `condition`使用元素來指定是否部署資源。 此元素的值會解析為 true 或 false。 當此值為 true 時，會部署資源。 當此值為 false 時，則不會部署資源。 此值只能套用至整個資源。

## <a name="new-or-existing-resource"></a>新的或現有的資源

您可以使用條件式部署來建立新的資源, 或使用現有的資源。 下列範例顯示如何使用條件來部署新的儲存體帳戶, 或使用現有的儲存體帳戶。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

當參數**newOrExisting**設定為**new**時, 條件會評估為 true。 已部署儲存體帳戶。 不過, 當**newOrExisting**設定為 [**現有**] 時, 條件會評估為 false, 而且不會部署儲存體帳戶。

如需使用 `condition` 項目的完整範例範本，請參閱[ 使用新的或現有的虛擬網路、儲存體和公用 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允許條件

您可以傳入參數值, 指出是否允許條件。 下列範例會部署 SQL server, 並選擇性地允許 Azure Ip。

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

如需完整的範本, 請參閱[AZURE SQL 邏輯伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>執行時間函式

如果您使用[reference](resource-group-template-functions-resource.md#reference)或[list](resource-group-template-functions-resource.md#list)函數搭配有條件地部署的資源, 即使未部署資源, 也會評估該函數。 如果函數參考不存在的資源, 就會收到錯誤。

使用[if](resource-group-template-functions-logical.md#if)函式可確保只有在部署資源時, 才會針對條件評估函式。 如需使用 if 和 reference 搭配條件式部署資源的範例範本, 請參閱[if](resource-group-template-functions-logical.md#if)函式。

## <a name="next-steps"></a>後續步驟

* 如需建立範本的建議，請參閱 [Azure Resource Manager 範本最佳做法](template-best-practices.md)。
* 若要建立資源的多個實例, 請參閱[Azure Resource Manager 範本中的資源、屬性或變數反復](resource-group-create-multiple.md)專案。