---
title: Azure 的自訂提供者預覽的概觀
description: 說明使用 Azure Resource Manager 建立自訂資源提供者概念
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159851"
---
# <a name="azure-custom-providers-preview-overview"></a>Azure 的自訂提供者預覽概觀

您可以使用 Azure 自訂提供者，來擴充 Azure 來處理您的服務。 您建立自己的資源提供者，包括自訂的資源類型和動作。 自訂提供者整合與 Azure Resource Manager。 您可以使用 Resource Manager 的功能，例如範本部署和角色型存取控制，來部署和保護您的服務。

這篇文章提供自訂提供者和其功能的概觀。 下圖顯示資源和動作的自訂提供者中定義的工作流程。

![自訂提供者概觀](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 自訂提供者目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="define-your-custom-provider"></a>定義您的自訂提供者

首先，讓 Azure 資源管理員了解您的自訂提供者。 Azure 部署自訂提供者資源使用的資源類型**Microsoft.CustomProviders/resourceProviders**。 在該資源中，您為您的服務定義的資源和動作。

例如，如果您的服務需要名為的資源類型**使用者**，該資源類型納入您的自訂提供者定義。 每個資源類型，您可以提供的端點，提供該資源類型的 REST 作業 (PUT、 GET、 DELETE)。 端點可以裝載在任何環境中，而且其中包含您的服務如何處理資源類型上的作業的邏輯。

您也可以定義您資源提供者的自訂動作。 動作代表 POST 作業。 使用動作，例如開始、 停止或重新啟動的作業。 您提供處理要求的端點。

下列範例示範如何定義自訂提供者使用的動作和資源類型。

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

針對**routingType**，接受的值是`Proxy`和`Cache`。 Proxy 會表示要求的資源類型或動作由端點處理。 資源類型，沒有動作僅支援快取設定。 若要指定快取，您也必須指定 proxy。 快取表示從端點的回應會儲存最佳化讀取的作業。 使用快取設定，讓您更輕鬆地實作是與其他資源管理員服務的一致且符合規範的 API。

## <a name="deploy-your-resource-types"></a>部署您的資源類型

定義您的自訂提供者之後, 您可以部署您的自訂的資源類型。 下列範例示範的 JSON，您納入您的範本來部署您的自訂提供者的資源類型。 此資源類型可以部署在相同的範本與其他 Azure 資源。

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>管理存取權

使用 Azure[角色型存取控制](../role-based-access-control/overview.md)來管理您的資源提供者的存取。 您可以指派[內建角色](../role-based-access-control/built-in-roles.md)例如擁有者、 參與者或讀取器的使用者。 或者，您可以定義[自訂角色](../role-based-access-control/custom-roles.md)，專屬於您的資源提供者中的作業。

## <a name="next-steps"></a>後續步驟

在本文中，您會了解自訂提供者。 請移至下一篇文章，以建立自訂提供者。

> [!div class="nextstepaction"]
> [教學課程：建立自訂提供者和部署自訂的資源](create-custom-provider.md)
