---
title: 使用範本的 Azure Event Grid 訂用帳戶
description: 使用 Resource Manager 範本建立 Event Grid 訂用帳戶。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>針對 Event Grid 訂用帳戶使用 Resource Manager 範本

本文示範如何使用 Azure Resource Manager 範本來建立 Event Grid 訂用帳戶。 您使用的格式會根據您是要訂閱資源群組事件還是特定資源類型的事件而有所不同。 這兩個格式都會在本文中有所說明。

## <a name="subscribe-to-resource-group-events"></a>訂閱資源群組事件

訂閱資源群組事件時，請針對資源類型使用 `Microsoft.EventGrid/eventSubscriptions`。 對於事件點類型，則請使用 `WebHook` 或 `EventHub`。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
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

當您將此範本部署到資源群組時，您就會訂閱該資源群組的事件。

## <a name="subscribe-to-resource-events"></a>訂閱資源事件

在訂閱資源事件時，您會在訂用帳戶定義中納入資源類型和名稱，從而將訂用帳戶關聯至正確的資源。 對於資源類型，使用 `<provider-namespace>/<resource-type>/providers/eventSubscriptions`。 對於名稱，使用 `<resource-name>/Microsoft.EventGrid/<subscription-name>`。 對於事件點類型，則請使用 `WebHook` 或 `EventHub`。

下列範例示範如何訂閱 Blob 儲存體事件。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "apiVersion": "2018-01-01",
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
* 如需 Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。
* 若要開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。