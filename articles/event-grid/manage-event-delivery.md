---
title: 適用於 Azure 事件方格訂用帳戶的無效信件與重試原則
description: 說明如何為事件方格自訂事件傳遞選項。 設定無效信件目的地，然後指定要重試傳遞的時間長度。
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tomfitz
ms.openlocfilehash: 0a89a315f9c97f3cc6a8683f13c22b5066dc5dab
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277744"
---
# <a name="dead-letter-and-retry-policies"></a>無效信件與重試原則

當建立事件訂用帳戶時，您可以自訂事件傳遞的設定。 本文說明如何設定無效信件位置及自訂重試設定。 如需這些功能的相關資訊，請參閱[事件方格訊息傳遞和重試](delivery-and-retry.md)。

## <a name="install-preview-feature"></a>安裝預覽功能

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>設定無效信件位置

若要設定無效信件位置，您必須要有儲存體帳戶用以保存無法傳遞至端點的事件。 範例會取得現有儲存體帳戶的資源識別碼。 它們會建立事件訂閱，使用該儲存體帳戶中的容器作為無效信件端點。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

若要關閉無效信件處理，請重新執行命令以建立事件訂閱，但是不提供 `deadletter-endpoint` 的值。 您不需要刪除事件訂閱。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

若要關閉無效信件處理，請重新執行命令以建立事件訂閱，但是不提供 `DeadLetterEndpoint` 的值。 您不需要刪除事件訂閱。

## <a name="set-retry-policy"></a>設定重試原則

當建立事件方格訂用帳戶時，您可以設定事件方格應該嘗試傳遞事件的時間長度。 依預設，事件方格會嘗試 24 小時 (1440 分鐘) 或 30 次。 您可以為事件方格訂用帳戶設定上述任一值。 事件存留時間的值必須是介於 1 和 1440 的整數。 重試次數上限的值必須是介於 1 到 30 之間的整數。

您無法設定[重試排程](delivery-and-retry.md#retry-schedule-and-duration)。

### <a name="azure-cli"></a>Azure CLI

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

若要將重試次數上限設定為 30 以外的值，請使用：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

如果您同時設定 `event-ttl` 和 `max-deliver-attempts`，事件方格就會使用前者作為到期條件，來判斷何時停止事件傳遞。

### <a name="powershell"></a>PowerShell

若要將事件存留時間設定為 1440 分鐘以外的值，請使用：

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

若要將重試次數上限設定為 30 以外的值，請使用：

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

如果您同時設定 `EventTtl` 和 `MaxDeliveryAttempt`，事件方格就會使用前者作為到期條件，來判斷何時停止事件傳遞。

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure 函式應用程式來處理無效信件事件的範例應用程式，請參閱[適用於 .NET 的 Azure 事件方格無效信件範例](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/) \(英文\)。
* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
