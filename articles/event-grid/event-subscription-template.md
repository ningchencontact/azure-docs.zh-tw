---
title: "Azure 事件方格訂用帳戶使用範本"
description: "使用資源管理員範本建立事件方格的訂用帳戶。"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2b9f55f8e944d688b622e30a773e1a34698f22ec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>使用資源管理員範本事件方格訂用帳戶

這篇文章會示範如何使用 Azure Resource Manager 範本來建立事件方格訂用帳戶。 您所使用的格式不同根據您要訂閱資源群組事件，或針對特定資源類型的事件。 這篇文章中，會顯示這兩種格式。

## <a name="subscribe-to-resource-group-events"></a>資源群組的事件訂閱

資源群組的事件訂閱，當使用`Microsoft.EventGrid/eventSubscriptions`資源類型。 事件類型，使用`WebHook`或`EventHub`。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

當您將此範本部署到資源群組時，您訂閱事件，該資源群組。

## <a name="subscribe-to-resource-events"></a>訂閱資源的事件

當訂閱資源的事件，關聯，您的訂用帳戶正確的資源包括訂閱定義中的資源類型和名稱。 資源類型，使用`<provider-namespace>/<resource-type>/providers/eventSubscriptions`。 使用的名稱， `<resource-name>/Microsoft.EventGrid/<subscription-name>`。 事件類型，使用`WebHook`或`EventHub`。

下列範例會示範如何訂閱 Blob 儲存體的事件。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 如需簡介至資源管理員，請參閱[Azure 資源管理員概觀](../azure-resource-manager/resource-group-overview.md)。
* 若要開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。