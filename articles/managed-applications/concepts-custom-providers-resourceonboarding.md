---
title: Azure 自訂提供者資源上線
description: 深入瞭解使用 Azure 自訂提供者的資源上線，以將管理或設定套用至其他 Azure 資源類型。
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609147"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 自訂提供者資源上線總覽

Azure 自訂提供者資源上架是適用于 Azure 資源類型的擴充性模型。 它可讓您大規模地對現有的 Azure 資源套用作業或管理。 如需詳細資訊，請參閱[Azure 自訂提供者可以如何擴充 azure](./custom-providers-overview.md)。 本檔說明：

- 資源上線的作用為何。
- 資源上線的基本概念，以及如何使用它。
- 何處可以找到要開始使用的指南和程式碼範例。

> [!IMPORTANT]
> 自訂提供者目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-resource-onboarding-do"></a>資源上線的功能

類似于[Azure 自訂提供者自訂資源](./custom-providers-resources-endpoint-how-to.md)，資源上線會定義合約，這將會對端點進行「上架」要求的 proxy。 與自訂資源不同的是，資源上線不會建立新的資源類型，而是允許現有資源類型的延伸。 此外，資源上架會與 Azure 原則搭配運作，因此可以大規模地進行資源的管理和設定。 資源上線工作流程的一些範例：

- 安裝和管理虛擬機器延伸模組。
- 上傳並設定 Azure 儲存體帳戶的預設值。
- 啟用大規模的基準診斷設定。

## <a name="resource-onboarding-basics"></a>資源上線基本概念

資源上線是使用 "CustomProviders/resourceProviders" 和 "CustomProviders/association" 資源類型，透過 Azure 自訂提供者進行設定。 若要啟用自訂提供者的資源上線，在設定過程中，請建立名為 "association" 的**resourceType** ，並具有包含 "Extension" 的**routingType** 。 此外，「CustomProviders/關聯」和「CustomProviders/resourceProviders」不需要屬於相同的資源群組。

範例 Azure 自訂提供者：

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

屬性 | 必要 | 說明
---|---|---
名稱 | 是 | 端點定義的名稱。 針對資源上線，名稱必須是「關聯」。
routingType | 是 | 判斷具有**端點**的合約類型。 針對資源上線，有效的**routingTypes**為「Proxy、快取、延伸模組」和「Webhook，快取，延伸模組」。
endpoint | 是 | 要作為要求路由傳送目的地的端點。 這會處理回應以及要求的任何副作用。

建立具有「關聯」資源類型的自訂提供者之後，您就可以使用「CustomProviders/關聯」做為目標。 「CustomProviders/關聯」是擴充功能資源，可以擴充任何其他 Azure 資源。 建立 "CustomProviders/association" 的實例時，它會接受屬性**targetResourceId**，這應該是有效的 "CustomProviders/resourceProviders" 或 "Microsoft. 解決方案/應用程式" 資源識別碼。 在這些情況下，會將要求轉送到我們建立的 "CustomProviders/resourceProviders" 實例上的 "association" 資源類型。

> [!Note]
> 如果將「Microsoft 解決方案/應用程式」資源識別碼當做**targetResourceId**提供，則必須在名為「公用」的受控資源群組中部署 "CustomProviders/resourceProviders"。

範例 Azure 自訂提供者關聯：

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

屬性 | 必要 | 說明
---|---|---
targetResourceId | 是 | "CustomProviders/resourceProviders" 或 "Microsoft. 解決方案/應用程式" 的資源識別碼。

## <a name="how-to-use-resource-onboarding"></a>如何使用資源上線

資源上線功能的運作方式是使用 "CustomProviders/association" 擴充功能資源擴充其他資源。 在下列範例中，將會針對虛擬機器提出要求，但可以擴充任何資源。

首先，必須建立具有「關聯」資源類型的自訂提供者資源。 這會宣告在建立對應的 "CustomProviders/association" 資源（以自訂提供者為目標）時，將使用的回呼 URL。

範例 "CustomProviders/resourceProviders" 建立要求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

建立自訂提供者之後，我們現在可以將其他資源設為目標，並將自訂提供者的副作用套用至它們。

範例「CustomProviders/關聯」建立要求：

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

然後，此要求會轉送至初始建立的自訂提供者中指定的端點，其格式為 "targetResourceId"：

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

端點應該會回應 "application/json" `Content-Type` 和有效的 JSON 回應主體。 在 JSON 的 "properties" 物件下傳回的欄位將會新增至關聯傳迴響應。

## <a name="looking-for-help"></a>尋求協助

如果您有關于 Azure 自訂資源提供者開發的問題，請嘗試詢問[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您已了解自訂提供者。 請移至下一篇文章以建立自訂提供者。

- [教學課程：使用自訂提供者的資源上線](./tutorial-custom-providers-resource-onboarding.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-custom-providers-101.md)
- [快速入門：建立 Azure 自訂資源提供者並部署自訂資源](./create-custom-provider.md)
- [如何：將自訂動作新增至 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源新增至 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
