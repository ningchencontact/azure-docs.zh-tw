---
title: Azure 事件中樞取得連接字串 | Microsoft Docs
description: 取得 Azure 事件中樞連接字串
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 10/15/2018
ms.author: shvija
ms.openlocfilehash: bfc82f2dc280c3528f38c9cb466473a76328e552
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285380"
---
# <a name="get-an-event-hubs-connection-string"></a>取得事件中樞連接字串

若要使用事件中樞，您必須建立事件中樞命名空間。 命名空間是可裝載多個事件中樞/Kafka 主題的範圍容器。 此命名空間可為您提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) \(英文\)。 一旦建立命名空間之後，您就可以獲得與事件中樞通訊所需的連接字串。

Azure 事件中樞的連接字串會在其中內嵌下列元件：

* FQDN = 您所建立之 EventHubs 命名空間的 FQDN (這將包含 EventHubs 命名空間名稱，後面接著 servicebus.windows.net)
* SharedAccessKeyName = 您為應用程式 SAS 金鑰選擇的名稱
* SharedAccessKey = 產生的金鑰值。

連接字串範本看起來如下
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

範例連接字串可能看起來像是 `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

本文將為您介紹獲得連接字串的各種方式。

## <a name="get-connection-string-from-the-portal"></a>從入口網站取得連接字串

當您具備事件中樞命名空間之後，入口網站的 [概觀] 區段就能為您提供連接字串，如下所示：

![事件中樞連接字串](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

當您在 [概觀] 區段中按一下連接字串連結時，即會開啟 [SAS 原則] 索引標籤，如下圖所示：

![事件中樞 SAS 原則](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

您可以新增新的 SAS 原則並取得連接字串，或使用已為您建立的預設原則。 開啟原則時，即可獲得連接字串，如下圖所示：

![事件中樞取得連接字串](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>使用 Azure PowerShell 取得連接字串
您可以使用 Get-AzureRmEventHubNamespaceKey 來取得指定原則/規則名稱的連接字串，如下所示：

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

如需更多詳細資料，請參閱[Azure 事件中樞 PowerShell 模組](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey) \(英文\)。

## <a name="getting-the-connection-string-with-azure-cli"></a>使用 Azure CLI 取得連接字串
您可以使用下列命令來取得命名空間的連接字串：

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

若要深入了解，請參閱[適用於事件中樞的 Azure CLI](https://docs.microsoft.com/cli/azure/eventhubs) \(英文\)。

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)
