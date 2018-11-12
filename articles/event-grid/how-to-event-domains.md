---
title: 如何使用事件網域管理 Azure 事件方格中的大量主題集合，並將事件發佈到這些主題中
description: 說明如何使用事件網域建立和管理 Azure 事件方格中的主題，並將事件發佈到這些主題中。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d6da1ee603c85556693b145ba17d1e0cd0dfabd7
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034535"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>使用事件網域管理主題並發佈事件

本文將說明如何：

* 建立事件方格網域
* 訂閱主題
* 列出金鑰
* 將事件發佈至網域

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>建立事件網域

建立事件網域可透過 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 的 `eventgrid` 擴充功能來完成。 網域建好後，您可以使用它來管理大量的主題集合。

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

建立成功會傳回下列內容：

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

請記下 `endpoint` 和 `id`，因為管理網域和發佈事件時需要這些項目。

## <a name="create-topics-and-subscriptions"></a>建立主題和訂用帳戶

事件方格服務會根據為網域主題建立事件訂閱的呼叫，在網域中自動建立及管理對應的主題。 沒有任何個別步驟可用來建立網域中的主題。 同樣地，刪除主題的最後一個事件訂閱時，也會將主題一併刪除。

訂閱網域中的主題如同訂閱任何其他 Azure 資源：

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

提供的資源識別碼是稍早建立網域時傳回的識別碼。 若要指定您想要訂閱的主題，請在資源識別碼結尾新增 `/topics/<my-topic>`。

若要建立可接收網域中所有事件的網域範圍事件訂閱，請提供網域作為 `resource-id`，並且不要指定任何主題，例如 `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`。

如果您需要能訂閱事件的測試端點，您可以一律部署[預先建置的 Web 應用程式](https://github.com/Azure-Samples/azure-event-grid-viewer)，以顯示傳入的事件。 您可以將事件傳送到您的測試網站：`https://<your-site-name>.azurewebsites.net/api/updates`。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

為主題設定的權限會儲存在 Azure Active Directory，必須明確地刪除。 如果使用者在主題上具有寫入權限，則刪除事件訂閱並不會撤銷使用者建立事件訂閱的存取權。

## <a name="manage-access-to-topics"></a>管理主題的存取權

您可以透過[角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)來管理主題的存取權。 角色指派會使用角色型存取檢查，限制只有特定範圍上的授權使用者可在 Azure 資源上執行作業。

事件方格有兩個內建角色，可用來將網域中各種主題的存取權指派給特定使用者。 這些角色是 `EventGrid EventSubscription Contributor (Preview)` (可讓您建立和刪除訂閱) 和 `EventGrid EventSubscription Reader (Preview)` (只允許列出事件訂閱)。

下列命令會限制 `alice@contoso.com` 只能建立和刪除 `foo` 主題上的事件訂閱：

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

請參閱[事件方格安全性和驗證](./security-authentication.md)，以深入了解下列項目：

* 管理存取控制
* 作業類型
* 建立自訂角色定義

## <a name="publish-events-to-an-event-grid-domain"></a>將事件發佈到事件方格網域

將事件發佈至網域與[發行至自訂主題](./post-to-custom-topic.md)一樣。 唯一的差別是您要指定每個事件的目的地主題。 下列事件陣列會將 `"id": "1111"` 的事件傳送到主題 `foo`，同時將 `"id": "2222"` 的事件傳送到主題 `bar`：

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

若要取得網域適用的金鑰：

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

然後使用您慣用的方法，讓 HTTP POST 將您的事件發佈至事件方格網域。

## <a name="next-steps"></a>後續步驟

* 如需深入了解事件網域和其為何實用的整體概念，請參閱[事件網域的概觀](./event-domains.md)。