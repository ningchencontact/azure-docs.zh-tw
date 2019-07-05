---
title: 將自訂資源新增至 Azure REST API
description: 了解如何將自訂資源新增至 Azure REST API。 本文將逐步引導的需求和最佳作法，想要實作自訂資源的端點。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b94d59b55a62797e142768dc84ec499d714bd067
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479014"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>將自訂資源新增至 Azure 的 REST API

這篇文章將探討的需求和最佳作法，建立可實作自訂資源的 Azure 自訂資源提供者端點。 如果您不熟悉 Azure 自訂資源提供者，請參閱[自訂資源提供者概觀](./custom-providers-overview.md)。

## <a name="how-to-define-a-resource-endpoint"></a>如何定義資源端點

**端點**是指向基礎之間實作合約它與 Azure 服務的 URL。 端點在自訂資源提供者中定義，而且可以是任何可公開存取的 URL。 下列範例具有**resourceType**稱為`myCustomResource`藉由將`endpointURL`。

範例**ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>建置資源端點

**端點**可實**resourceType**必須為新的 API，在 Azure 中處理的要求和回應。 當使用的自訂資源提供者**resourceType**已建立，它會產生一組新的 Api 在 Azure 中。 在此情況下， **resourceType**會產生新的 Azure 資源的 API `PUT`， `GET`，並`DELETE`單一資源上執行 CRUD 以及`GET`擷取所有現有的資源：

操作單一資源 (`PUT`， `GET`，和`DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

擷取所有資源 (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

如需自訂的資源，自訂資源提供者會提供兩種類型**routingTypes**: 「`Proxy`"和"`Proxy, Cache`」。

### <a name="proxy-routing-type"></a>proxy 路由類型

「`Proxy`」 **RoutingType**方法的所有要求的 proxy**端點**自訂資源提供者中所指定。 使用時機"`Proxy`」:

- 需要回應的完整控制權。
- 整合系統與現有的資源。

若要深入了解 「`Proxy`」 資源，請參閱[自訂資源的 proxy 參考](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>proxy 快取中的路由類型

"`Proxy, Cache`」 **RoutingType** proxy 只`PUT`並`DELETE`要求方法**端點**自訂資源提供者中所指定。 自訂資源提供者會自動傳回`GET`要求根據它已儲存在其快取。 如果自訂的資源以快取標記，自訂資源提供者也會新增/覆寫對 Api 讓 Azure 成為符合規範的回應中的欄位。 使用時機"`Proxy, Cache`」:

- 建立新的系統有任何現有的資源。
- 使用現有的 Azure 生態系統。

若要深入了解 「`Proxy, Cache`」 資源，請參閱[自訂資源快取參考](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>建立自訂的資源

有兩種主要的方式，建立自訂的資源，從自訂資源提供者：

- Azure CLI
- Azure Resource Manager 範本

### <a name="azure-cli"></a>Azure CLI

建立自訂的資源：

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

參數 | 必要項 | 描述
---|---|---
is-full-object | *yes* | 指出屬性物件包含其他選項，例如位置、 標記、 sku 和/或計劃。
id | *yes* | 自訂資源的資源識別碼。 這應該要關閉的存在**ResourceProvider**
properties | *yes* | 將會傳送至要求本文**端點**。

刪除 Azure 的自訂資源：

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

參數 | 必要項 | 描述
---|---|---
id | *yes* | 自訂資源的資源識別碼。 這應該存在於登出**ResourceProvider**。

擷取 Azure 的自訂資源：

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

參數 | 必要項 | 描述
---|---|---
id | *yes* | 自訂資源的資源識別碼。 這應該要關閉的存在**ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

> [!NOTE]
> 回應包含適當的資源需要`id`， `name`，並`type`從**端點**。

Azure Resource Manager 範本需要`id`， `name`，和`type`已正確地傳回從下游的端點。 在表單中應傳回的資源回應：

範例**端點**回應：

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

範例 Azure Resource Manager 範本：

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

參數 | 必要項 | 描述
---|---|---
resourceTypeName | *yes* | **名稱**的**resourceType**自訂提供者中定義。
resourceProviderName | *yes* | 自訂資源提供者執行個體名稱。
customResourceName | *yes* | 自訂資源名稱。

## <a name="next-steps"></a>後續步驟

- [在 Azure 的自訂資源提供者的概觀](./custom-providers-overview.md)
- [教學課程：建立 Azure 自訂資源提供者和部署自訂的資源](./create-custom-provider.md)
- [如何：將自訂動作加入至 Azure 的 REST API](./custom-providers-action-endpoint-how-to.md)
- [參考：自訂資源的 Proxy 參考](./custom-providers-proxy-resource-endpoint-reference.md)
- [參考：自訂資源快取參考](./custom-providers-proxy-cache-resource-endpoint-reference.md)
