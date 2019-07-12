---
title: 自訂資源快取參考
description: Azure 自訂資源提供者的自訂資源快取參考。 這篇文章將探討端點實作快取的自訂資源的需求。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795233"
---
# <a name="custom-resource-cache-reference"></a>自訂資源快取參考

這篇文章將探討端點實作快取的自訂資源的需求。 如果您不熟悉 Azure 自訂資源提供者，請參閱[自訂資源提供者概觀](./custom-providers-overview.md)。

## <a name="how-to-define-a-cache-resource-endpoint"></a>如何定義快取資源端點

可以建立 proxy 資源，藉由指定**routingType**來 「 Proxy 快取 」。

範例自訂資源提供者：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

## <a name="building-proxy-resource-endpoint"></a>建立 proxy 資源端點

**端點**實作 「 Proxy、 快取 」 資源**端點**必須為新的 API，在 Azure 中處理的要求和回應。 在此情況下， **resourceType**會產生新的 Azure 資源的 API `PUT`， `GET`，並`DELETE`的單一資源上執行 CRUD 以及`GET`擷取所有現有的資源：

> [!NOTE]
> Azure API 會產生要求方法`PUT`， `GET`，並`DELETE`，但快取**端點**只需要處理`PUT`並`DELETE`。
> 我們建議**端點**也會實作`GET`。

### <a name="create-a-custom-resource"></a>建立自訂資源

Azure API 的連入要求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

然後將此要求轉送到**端點**形式：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

同樣地，從回應**端點**然後轉送回給客戶。 應該會傳回端點的回應：

- 有效的 JSON 物件文件。 所有的陣列和字串應該是巢狀下最上層的物件。
- `Content-Type`標頭應該設定為"application/json;charset = utf-8"。
- 自訂資源提供者將會覆寫`name`， `type`，和`id`要求的欄位。
- 自訂資源提供者只會傳回下的欄位`properties`快取端點的物件。

**端點**回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`， `id`，和`type`欄位將會自動產生自訂資源的自訂資源提供者。

Azure 的自訂資源提供者的回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>移除自訂資源

Azure API 的連入要求：

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

然後將此要求轉送到**端點**形式：

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同樣地，從回應**端點**然後轉送回給客戶。 應該會傳回端點的回應：

- 有效的 JSON 物件文件。 所有的陣列和字串應該是巢狀下最上層的物件。
- `Content-Type`標頭應該設定為"application/json;charset = utf-8"。
- 如果技術等級 200 的回應會傳回 Azure 自訂資源提供者只會從其快取移除項目。 即使資源不存在，**端點**應該會傳回 204。

**端點**回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure 的自訂資源提供者的回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>擷取自訂的資源

Azure API 的連入要求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

要求將會**未**轉送給**端點**。

Azure 的自訂資源提供者的回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>列舉所有的自訂資源

Azure API 的連入要求：

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

此要求會**未**轉送給**端點**。

Azure 的自訂資源提供者的回應：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

- [在 Azure 的自訂資源提供者的概觀](./custom-providers-overview.md)
- [快速入門：建立 Azure 自訂資源提供者和部署自訂的資源](./create-custom-provider.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-custom-providers-101.md)
- [如何：將自訂動作加入至 Azure 的 REST API](./custom-providers-action-endpoint-how-to.md)
- [參考：自訂資源的 Proxy 參考](./custom-providers-proxy-resource-endpoint-reference.md)
