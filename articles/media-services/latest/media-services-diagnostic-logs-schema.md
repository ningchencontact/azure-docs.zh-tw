---
title: Azure 媒體服務的診斷記錄結構描述-Azure
description: 本文說明 Azure 媒體服務的診斷記錄結構描述。
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
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322245"
---
# <a name="diagnostic-logs-schemas"></a>診斷記錄結構描述

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷的記錄檔，可協助您了解如何執行您的應用程式。 您可以監視媒體服務的診斷記錄檔，並建立警示和通知所收集的計量和記錄檔。 您可以將記錄傳送至[Azure 儲存體](https://azure.microsoft.com/services/storage/)，串流至[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)，並將它們以匯出[Log Analytics](https://azure.microsoft.com/services/log-analytics/)，或使用第 3 方服務。

如需詳細資訊，請參閱 < [Azure 監視器計量](../../azure-monitor/platform/data-platform.md)並[Azure 監視器診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)。

這篇文章說明媒體服務診斷記錄結構描述。

## <a name="top-level-diagnostic-logs-schema"></a>最上層診斷記錄結構描述

如需詳細的最上層的診斷記錄結構描述的詳細說明，請參閱[Azure 診斷記錄支援服務、 結構描述和類別](../../azure-monitor/platform/tutorial-dashboards.md)。

## <a name="key-delivery-log-schema"></a>金鑰傳遞記錄結構描述

### <a name="properties"></a>屬性

這些屬性專屬於金鑰傳遞記錄結構描述。

|名稱|描述|
|---|---|
|keyId|要求的索引鍵識別碼。|
|keyType|可能是下列其中一個值：「 清除 」 （不加密）、"FairPlay 」、 「 PlayReady"或者"Widevine 」。|
|policyName|Azure Resource Manager 原則的名稱。|
|tokenType|權杖類型。|
|statusMessage|狀態訊息。|

### <a name="examples"></a>範例

金鑰傳遞要求結構描述的屬性。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>後續步驟

[監視媒體服務的計量和診斷記錄](media-services-metrics-diagnostic-logs.md)
