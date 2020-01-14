---
title: 將 Azure 事件方格用於 CloudEvents 結構描述中的事件
description: 說明如何在 Azure 事件方格中設定事件的 CloudEvents 結構描述。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 78498f06a1ec921813d886b426d62452f850f5e5
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922753"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>搭配事件方格使用 CloudEvents v1.0 架構

除了其[預設事件架構](event-schema.md)之外，Azure 事件方格原本就支援 CloudEvents V1.0 和[HTTP 通訊協定](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)系結的[JSON 實](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)值中的事件。 [CloudEvents](https://cloudevents.io/) 是用來說明事件資料的[開放式規格](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供用以發佈和取用雲端型事件的常見事件結構描述，可簡化互通性。 此結構描述可支援統一的工具、路由和處理事件的標準方式，以及將外部事件結構描述還原序列化的通用方式。 透過通用結構描述，您將可更輕鬆地跨平台整合工作。

目前有數個[共同作業者](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (包括 Microsoft) 正透過 [Cloud Native Computing Foundation](https://www.cncf.io/) 建置 CloudEvents。 它目前以1.0 版的形式提供。

本文說明如何透過事件方格使用 CloudEvents 結構描述。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>安裝預覽功能

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent 結構描述

下列範例說明 CloudEvents 格式的 Azure Blob 儲存體事件：

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

如需可用欄位、其類型和定義的詳細說明，請參閱[這裡](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)： CloudEvents v1.0。

在 CloudEvents 結構描述中傳遞的事件標頭值與事件方格結構描述的該值相同，不同之處在於 `content-type`。 針對 CloudEvents 結構描述，該標頭值是 `"content-type":"application/cloudevents+json; charset=utf-8"`。 針對事件方格結構描述，該標頭值是 `"content-type":"application/json; charset=utf-8"`。

## <a name="configure-event-grid-for-cloudevents"></a>設定 CloudEvents 的事件方格

您可以使用事件方格來輸入和輸出 CloudEvents 結構描述中的事件。 您可以將 CloudEvents 用於系統事件 (例如 Blob 儲存體事件和 IoT 中樞事件) 及自訂事件。 它也可以在線上來回轉換這些事件。


| 輸入結構描述       | 輸出結構描述
|--------------------|---------------------
| CloudEvents 格式 | CloudEvents 格式
| 事件方格格式  | CloudEvents 格式
| CloudEvents 格式 | 事件方格格式
| 事件方格格式  | 事件方格格式

對於所有的事件結構描述，事件方格在發佈至事件方格主題和建立事件訂閱時，都需要進行驗證。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

### <a name="input-schema"></a>輸入結構描述

當您建立自訂主題時，您可以設定自訂主題的輸入結構描述。

對於 Azure CLI，請使用：

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

對於 PowerShell，請使用：

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

CloudEvents 的目前版本不支援事件的批次處理。 若要透過 CloudEvent 結構描述將事件發佈至主題，請個別發佈每個事件。

### <a name="output-schema"></a>輸出結構描述

當您建立事件訂用帳戶時，您可以設定輸出結構描述。

對於 Azure CLI，請使用：

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

對於 PowerShell，請使用：
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 目前，當事件是在 CloudEvents 結構描述中傳遞時，您無法針對 Azure Functions 應用程式使用事件方格觸發程序。 使用 HTTP 觸發程序。 如需實作 HTTP 觸發程序 (該觸發程序會接收 CloudEvents 結構描述中的事件) 的範例，請參閱[使用 HTTP 觸發程序作為事件方格觸發程序](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger)。

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 的端點驗證

如果您已經熟悉事件方格，您可能會注意到事件方格的端點驗證交握以防止濫用。 CloudEvents v1.0 會使用 HTTP OPTIONS 方法來執行自己的[濫用保護語義](security-authentication.md#webhook-event-delivery)。 您可以在 [此處](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)閱讀相關資訊。 使用 CloudEvents 架構進行輸出時，事件方格會使用搭配 CloudEvents v1.0 的濫用保護來取代事件方格驗證事件機制。

## <a name="next-steps"></a>後續步驟

* 如需關於監視事件傳遞的資訊，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 建議您測試、評論和[參與](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) CloudEvents。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
