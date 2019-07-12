---
title: Azure 的自訂資源提供者的概觀
description: 深入了解 Azure 自訂資源提供者，以及如何擴充 Azure API 平面，以符合您的工作流程。
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796090"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 的自訂資源提供者概觀

Azure 的自訂資源提供者是 azure 平台擴充性。 它可讓您定義可用來擴充預設的自訂 Api 的 Azure 體驗。 這份文件描述：

- 如何建置和部署 Azure 自訂資源提供者。
- 如何使用 Azure 自訂資源提供者來擴充現有的工作流程。
- 尋找指南和程式碼範例，若要開始的位置。

![自訂提供者概觀](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 自訂提供者目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="what-can-custom-resource-providers-do"></a>自訂資源提供者怎麼辦

以下是一些您可以使用 Azure 自訂資源提供者達到的範例：

- 擴充 Azure Resource Manager REST API，以包含內部和外部服務。
- 啟用現有的 Azure 工作流程上的自訂案例。
- 自訂 Azure Resource Manager 範本的控制項和效果。

## <a name="what-is-a-custom-resource-provider"></a>什麼是自訂的資源提供者

Azure 的自訂資源提供者會藉由建立 Azure 和端點之間的合約。 這個合約定義了一份新的資源，以及透過新的資源，動作**Microsoft.CustomProviders/resourceProviders**。 然後，自訂資源提供者會公開這些新的 Api，在 Azure 中。 Azure 的自訂資源提供者由三個部分組成： 自訂資源提供者**端點**，和自訂資源。

## <a name="how-to-build-custom-resource-providers"></a>如何建置自訂資源提供者

自訂資源提供者是一份 Azure 與端點之間的合約。 此合約會描述 Azure 應該與端點互動的方式。 資源提供者會喜歡 proxy，且會將轉送要求和回應與指定**端點**。 資源提供者可以指定兩種類型的合約： [ **resourceTypes** ](./custom-providers-resources-endpoint-how-to.md)並[**動作**](./custom-providers-action-endpoint-how-to.md)。 透過端點定義會啟用這些。 端點定義由三個欄位組成：**名稱**， **routingType**，並**端點**。

範例端點：

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

屬性 | 必要項 | 描述
---|---|---
name | *yes* | 端點定義的名稱。 Azure 會公開 （expose) 透過其 API，在此名稱 ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | 決定使用的合約型別**端點**。 如果未指定，它會預設為"Proxy"。
endpoint | *yes* | 若要將要求路由端點。 這會處理回應，以及要求的任何副作用。

### <a name="building-custom-resources"></a>建置自訂的資源

**ResourceTypes**描述新的自訂資源新增到 Azure。 這些會公開基本的 RESTful CRUD 方法。 請參閱[深入了解建立自訂的資源](./custom-providers-resources-endpoint-how-to.md)

範例使用自訂資源提供者**resourceTypes**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

上述範例新增至 Azure Api:

HttpMethod | 範例 URI | 描述
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API 呼叫來建立新的資源。
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 若要刪除現有的資源 Azure REST API 呼叫。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API 呼叫來擷取現有的資源。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Azure REST API 呼叫來擷取現有的資源清單。

### <a name="building-custom-actions"></a>建置自訂動作

**動作**描述新增到 Azure 的新動作。 這些可以公開在 資源提供者之上或之下巢狀**resourceType**。 請參閱[深入了解建立自訂動作](./custom-providers-action-endpoint-how-to.md)

範例使用自訂資源提供者**動作**:

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
  "location": "eastus"
}
```

上述範例新增至 Azure Api:

HttpMethod | 範例 URI | 描述
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Azure REST API 呼叫啟動動作。

## <a name="looking-for-help"></a>尋求協助

如果您有適用於 Azure 自訂資源提供者開發的問題，請嘗試詢問[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)。 類似的問題可能已提出和回答，因此請先之前公佈。 將標記加入```azure-custom-providers```取得快速回應 ！

## <a name="next-steps"></a>後續步驟

在本文中，您會了解自訂提供者。 請移至下一篇文章，以建立自訂提供者。

- [快速入門：建立 Azure 自訂資源提供者和部署自訂的資源](./create-custom-provider.md)
- [教學課程：在 Azure 中建立自訂動作和資源](./tutorial-custom-providers-101.md)
- [如何：將自訂動作加入至 Azure 的 REST API](./custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源新增至 Azure 的 REST API](./custom-providers-resources-endpoint-how-to.md)
