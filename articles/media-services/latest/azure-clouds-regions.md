---
title: 可使用 Azure 媒體服務 v3 的雲端和區域| Microsoft Docs
description: 本文會討論可使用 Azure 媒體服務 v3 的 Azure 雲端和區域。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: d176152429ecac1ed4e570533f1bc0426cc7655f
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767447"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>存在 Azure 媒體服務 v3 的雲端和區域

Azure 媒體服務 v3 可透過 Azure Resource Manager 資訊清單在全域 Azure、Azure Government、Azure 德國、Azure China 21Vianet 中使用。 但是，並非所有 Azure 雲端中都提供所有的媒體服務功能。 本文件概述主要媒體服務 v3 元件的可用性。

## <a name="feature-availability-in-azure-clouds"></a>Azure 雲端中的功能可用性

| 功能|全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 可用 | 尚未提供 | 尚未提供 | 尚未提供 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  可用 | 尚未提供 | 尚未提供 | 尚未提供 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  可用 | 尚未提供 | 尚未提供 | 尚未提供 |
| [StandardEncoderPreset](encoding-concept.md) | 可用 | 可用 | 可用 | 可用 |
| [LiveEvents](live-streaming-overview.md) | 可用 | 可用 | 可用 | 可用 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 可用 | 可用 | 可用 | 可用 |

## <a name="regionsgeographieslocations"></a>區域/地理位置/位置

* [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)
* [依區域的產品](https://azure.microsoft.com/global-infrastructure/services/)
* [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="region-code-name"></a>區域碼名稱 

如果需要提供**位置**參數，則需要提供區域代碼名稱作為**位置**值。 若要取得您帳戶所在且應該路由傳送呼叫之區域的代碼名稱，您可以在 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 中執行下面這一行

```bash
az account list-locations
```

執行以上所示的這一行之後，您會取得所有 Azure 區域的清單。 瀏覽至包含您要尋找之 *displayName* 的 Azure 區域，並使用其 *name* 值作為 **location** 參數。

例如，若是美國西部 2 (下方顯示) 的 Azure 區域，您將使用 "westus2" 作為 **location** 參數。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
