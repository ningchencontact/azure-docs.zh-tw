---
title: 適用於 Azure 事件方格訂用帳戶的無效信件與重試原則
description: 說明如何為事件方格自訂事件傳遞選項。 設定無效信件目的地，然後指定要重試傳遞的時間長度。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077777"
---
# <a name="dead-letter-and-retry-policies"></a>無效信件與重試原則

當建立事件訂用帳戶時，您可以自訂事件傳遞的設定。 本文說明如何設定無效信件位置及自訂重試設定。 如需這些功能的相關資訊，請參閱[事件方格訊息傳遞和重試](delivery-and-retry.md)。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>設定無效信件位置

若要設定無效信件位置，您必須要有儲存體帳戶用以保存無法傳遞至端點的事件。 下列指令碼會取得現有儲存體帳戶的資源識別碼，並且建立事件訂用帳戶；該事件訂用帳戶會使用儲存體帳戶中的容器作為無效信件端點。

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

若要關閉無效信件處理，請重新執行命令以建立事件訂閱，但是不提供 `deadletter-endpoint` 的值。 您不需要刪除事件訂閱。

## <a name="set-retry-policy"></a>設定重試原則

當建立事件方格訂用帳戶時，您可以設定事件方格應該嘗試傳遞事件的時間長度。 根據預設，事件方格會嘗試 24 小時 (1440 分鐘)，以及嘗試最多 30 次。 您可以為事件方格訂用帳戶設定上述任一值。 事件存留時間的值必須是介於 1 和 1440 的整數。 傳遞嘗試上限的值必須是介於 1 和 30 的整數。

您無法設定[重試排程](delivery-and-retry.md#retry-schedule-and-duration)。

若要將事件存留時間設定為 1440 分鐘以外的值，請使用：

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

若要將重試嘗試上限設定為 30 以外的值，請使用：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

如果您同時設定 `event-ttl` 和 `max-deliver-attempts`，事件方格會針對重試嘗試使用第一個到期的項目。

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure 函式應用程式來處理無效信件事件的範例應用程式，請參閱[適用於 .NET 的 Azure 事件方格無效信件範例](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/) \(英文\)。
* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
