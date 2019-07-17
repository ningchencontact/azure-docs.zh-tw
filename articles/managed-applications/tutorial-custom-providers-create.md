---
title: 建立和使用 Azure 自訂提供者
description: 本教學課程將介紹如何建立和使用自訂提供者。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799127"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>撰寫自訂提供者的 RESTful 端點

自訂提供者可讓您在 Azure 上自訂工作流程。 自訂提供者是 Azure 和 `endpoint` 之間的合約。 本教學課程會詳細解說自訂提供者的建立程序。 如果您不熟悉 Azure 自訂提供者，請參閱[自訂資源提供者概觀](./custom-providers-overview.md)。

本教學課程分成下列步驟：

- 什麼是自訂提供者
- 定義自訂動作和資源
- 部署自訂提供者

本教學課程會以下列教學課程為基礎來建置：

- [撰寫自訂提供者的 RESTful 端點](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>建立自訂提供者

> [!NOTE]
> 本教學課程不會介紹如何撰寫端點。 如果您沒有 RESTful 端點，請遵循[撰寫 RESTful 端點的教學課程](./tutorial-custom-providers-function-authoring.md)。

建立了 `endpoint` 後，您可以產生建立自訂提供者，以產生其與 `endpoint` 之間的合約。 自訂提供者可讓您指定端點定義的清單。

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

屬性 | 必要 | 說明
---|---|---
name | 是  | 端點定義的名稱。 Azure 會透過其 API 在 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' 底下公開此名稱
routingType | 否  | 決定 `endpoint` 的合約類型。 如果未指定，則會預設為 "Proxy"。
endpoint | 是  | 要作為要求路由傳送目的地的端點。 這會處理回應以及要求的任何副作用。

在此情況下，`endpoint` 是 Azure 函式的觸發 URL。 `<yourapp>`、`<funcname>` 和 `<functionkey>` 應該取代為所建立函式的值。

## <a name="defining-custom-actions-and-resources"></a>定義自訂動作和資源

自訂提供者包含建模於 `actions` 和 `resourceTypes` 底下的端點定義清單。 `actions` 會對應至自訂提供者所公開的自訂動作，`resourceTypes` 則是自訂資源。 在本教學課程中，我們會使用 `action` (稱為 `myCustomAction`) 和 `resourceType` (稱為 `myCustomResources`) 來定義自訂提供者。

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

請將 `endpoint` 取代為稍早在上一個教學課程所建立函式中的觸發 URL。

## <a name="deploying-the-custom-provider"></a>部署自訂提供者

> [!NOTE]
> `endpoint` 應該取代為函式 URL。

您可以使用 Azure Resource Manager 範本來部署上述自訂提供者。

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>使用自訂動作和資源

在建立了自訂提供者之後，我們可以利用新的 Azure API。 下一節會說明如何呼叫和使用自訂提供者。

### <a name="custom-actions"></a>自訂動作

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` 和 `{resourceGroupName}` 應該取代為自訂提供者部署所在的訂用帳戶和資源群組。

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

參數 | 必要 | 說明
---|---|---
動作 | 是  | 所建立自訂提供者中定義的動作名稱。
ids | 是  | 所建立自訂提供者的資源識別碼。
request-body | 否  | 將會傳送至 `endpoint` 的要求本文。

# <a name="templatetabtemplate"></a>[範本](#tab/template)

無。

---

### <a name="custom-resources"></a>自訂資源

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` 和 `{resourceGroupName}` 應該取代為自訂提供者部署所在的訂用帳戶和資源群組。

建立自訂資源：

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

參數 | 必要 | 說明
---|---|---
is-full-object | 是  | 指出屬性物件包含其他選項，例如位置、標籤、SKU 和/或方案。
id | 是  | 自訂資源的資源識別碼。 此識別碼應該依存於所建立的自訂提供者。
properties | 是  | 將會傳送至 `endpoint` 的要求本文。

刪除 Azure 自訂資源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

參數 | 必要 | 說明
---|---|---
id | 是  | 自訂資源的資源識別碼。 此識別碼應該依存於所建立的自訂提供者。

擷取 Azure 自訂資源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

參數 | 必要 | 說明
---|---|---
id | 是  | 自訂資源的資源識別碼。 此識別碼應該依存於所建立的自訂提供者。

# <a name="templatetabtemplate"></a>[範本](#tab/template)

Azure Resource Manager 範本的範例：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

參數 | 必要 | 說明
---|---|---
resourceTypeName | 是  | 自訂提供者中所定義 resourceType  的 `name`。
resourceProviderName | 是  | 自訂提供者執行個體名稱。
customResourceName | 是  | 自訂資源名稱。

---

> [!NOTE]
> 自訂提供者的部署和使用完成之後，請記得清除任何已建立的資源，包括 Azure 函式。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 請移至下一篇文章以建立自訂提供者。

- [操作說明：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [操作說明：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
