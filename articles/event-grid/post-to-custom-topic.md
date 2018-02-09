---
title: "張貼事件以自訂 Azure Event Grid 主題"
description: "描述如何針對 Azure Event Grid 將事件張貼到自訂主題"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>針對 Azure Event Grid 張貼到自訂主題

本文說明如何將事件張貼到自訂主題。 它會顯示貼文與事件資料的格式。 [服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) 只適用於符合預期格式的貼文。

## <a name="endpoint"></a>端點

將 HTTP POST 傳送至自訂主題時，請使用 URI 格式：`https://<topic-endpoint>?api-version=2018-01-01`。

例如，`https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` 便是有效的 URI。

若要使用 Azure CLI 取得自訂主題的端點，請使用：

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

若要使用 Azure PowerShell 取得自訂主題的端點，請使用：

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>頁首

在要求中，包含名為 `aeg-sas-key` 的標頭值，其中包含用於驗證的金鑰。

例如，有效的標頭值為 `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`。

若要使用 Azure CLI 取得自訂主題的索引鍵，請使用：

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

若要使用 PowerShell 取得自訂主題的索引鍵，請使用：

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>事件資料

對於自訂主題，最上層的資料包含與標準資源定義事件相同的欄位。 其中一個屬性是資料屬性，其中包含自訂主題特有的屬性。 身為事件發行者，您必須判斷該資料物件的屬性。 使用下列結構描述：

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

如需這些屬性的說明，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

例如，有效的事件資料結構描述為：

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="next-steps"></a>後續步驟

* 如需路由傳送自訂事件的簡介，請參閱[使用 Azure CLI 和 Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)或[使用 Azure PowerShell 和 Event Grid 建立和路由傳送自訂事件](custom-event-quickstart-powershell.md)。
* 如需驗證金鑰的詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
