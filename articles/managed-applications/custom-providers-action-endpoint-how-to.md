---
title: 將自訂動作加入至 Azure REST API
description: 了解如何將自訂動作新增至 Azure REST API。 本文將逐步引導的需求和想要實作自訂動作的結束點的最佳作法。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 1bfc0be81d42e922c47755543fb65aa413ec73a9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478754"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>將自訂動作加入至 Azure 的 REST API

這篇文章將探討的需求和最佳作法來建立自訂資源提供者的 Azure 端點，可實作自訂動作。 如果您不熟悉 Azure 自訂資源提供者，請參閱[自訂資源提供者概觀](./custom-providers-overview.md)。

## <a name="how-to-define-an-action-endpoint"></a>如何定義動作端點

**端點**是指向基礎之間實作合約它與 Azure 服務的 URL。 端點在自訂資源提供者中定義，而且可以是任何可公開存取的 URL。 下列範例具有**動作**稱為`myCustomAction`藉由將`endpointURL`。

範例**ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>建置動作端點

**端點**可實**動作**必須為新的 API，在 Azure 中處理的要求和回應。 當使用的自訂資源提供者**動作**已建立，它會產生一組新的 Api 在 Azure 中。 在此情況下，動作將產生的新 Azure 動作 API`POST`呼叫：

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 的連入要求：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

然後將此要求轉送到**端點**形式：

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

同樣地，從回應**端點**然後轉送回給客戶。 應該會傳回端點的回應：

- 有效的 JSON 物件文件。 所有的陣列和字串應該是巢狀下最上層的物件。
- `Content-Type`標頭應該設定為"application/json;charset = utf-8"。

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure 的自訂資源提供者的回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>呼叫自訂動作

有兩種主要的方法呼叫從自訂資源提供者的自訂動作：

- Azure CLI
- Azure Resource Manager 範本

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

參數 | 必要項 | 描述
---|---|---
action | *yes* | 中所定義的動作名稱**ResourceProvider**。
識別碼 | *yes* | 資源識別碼**ResourceProvider**。
要求本文 | *no* | 將會傳送至要求本文**端點**。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

> [!NOTE]
> 動作與 Azure Resource Manager 範本中的支援有限。 為了讓呼叫是在範本中的動作，它必須包含[ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list)在其名稱的前置詞。

範例**ResourceProvider**清單動作：

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

範例 Azure Resource Manager 範本：

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

參數 | 必要項 | 描述
---|---|---
resourceIdentifier | *yes* | 資源識別碼**ResourceProvider**。
apiVersion | *yes* | 資源執行階段 API 版本。 這應該一律是"2018年-09-01-preview"。
functionValues | *no* | 將會傳送至要求本文**端點**。

## <a name="next-steps"></a>後續步驟

- [在 Azure 的自訂資源提供者的概觀](./custom-providers-overview.md)
- [教學課程：建立 Azure 自訂資源提供者和部署自訂的資源](./create-custom-provider.md)
- [如何：將自訂資源新增至 Azure 的 REST API](./custom-providers-resources-endpoint-how-to.md)
