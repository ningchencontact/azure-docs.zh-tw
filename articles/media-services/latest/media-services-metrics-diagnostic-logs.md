---
title: 監視媒體服務的計量和透過 Azure 監視器的診斷記錄 |Microsoft Docs
description: 這篇文章提供如何監視媒體服務的計量和診斷的記錄，透過 Azure 監視器的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964761"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>監視媒體服務的計量和診斷記錄

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷的記錄檔，可協助您了解如何執行您的應用程式。 Azure 監視器所收集的所有資料都適合其中兩個基本的型別，度量和記錄檔。 您可以監視媒體服務的診斷記錄檔，並建立警示和通知所收集的計量和記錄檔。 您以視覺化方式檢視和分析計量使用資料[計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。 您可以將記錄傳送至[Azure 儲存體](https://azure.microsoft.com/services/storage/)，串流至[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)，並將它們以匯出[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第 3 方服務。

如需詳細概觀，請參閱[Azure 監視器計量](../../azure-monitor/platform/data-platform.md)並[Azure 監視器診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)。

本主題討論目前可[媒體服務度量](#media-services-metrics)並[Media Services 診斷記錄](#media-services-diagnostic-logs)。

## <a name="media-services-metrics"></a>媒體服務度量

不論值是否變更，系統都會定期收集計量。 它們適合用於警示，因為它們可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速引發警示。

目前，下列媒體服務[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)azure 就會發出度量：

|計量|Display name|描述|
|---|---|---|
|Requests|Requests|提供的串流端點服務的要求總數的詳細資料。|
|輸出|輸出|輸出位元組總數。 比方說，位元組資料流處理的串流端點。|
|SuccessE2ELatency|成功的端對端延遲| 可讓要求成功的端對端延遲的相關資訊。|

例如，若要使用 CLI 的 「 輸出 」 計量，您會執行下列`az monitor metrics`CLI 命令：

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

如需如何建立計量警示的詳細資訊，請參閱[建立、 檢視及管理使用 Azure 監視器計量警示](../../azure-monitor/platform/alerts-metric.md)。

## <a name="media-services-diagnostic-logs"></a>媒體服務的診斷記錄

目前，您可以取得下列診斷記錄檔：

|名稱|描述|
|---|---|
|金鑰傳遞服務要求|顯示金鑰傳遞服務要求資訊的記錄檔。 如需詳細資訊，請參閱 <<c0> [ 結構描述](media-services-diagnostic-logs-schema.md)。|

若要啟用儲存體帳戶中診斷記錄的儲存體，您可以執行下列`az monitor diagnostic-settings`CLI 命令： 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例如:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>後續步驟 

[如何收集並取用來自 Azure 資源的記錄資料](../../azure-monitor/platform/diagnostic-logs-overview.md)。
