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
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 4f8851248c395a1f03c46490c8eb5e71221dd133
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549896"
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

### <a name="region-code-name"></a>區域碼名稱 

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

## <a name="endpoints"></a>端點  

從不同國家/地區的 Azure 雲端連線到媒體服務帳戶時，下列端點非常重要。

### <a name="global-azure"></a>全域 Azure

|端點 ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| 權杖對象 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|端點||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| 權杖對象 | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| 端點 ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| 權杖對象 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|端點||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| 權杖對象 |  `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
