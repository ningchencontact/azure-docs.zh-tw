---
title: Azure 媒體服務 LiveEvent 類型 | Microsoft Docs
description: 本文會說明詳細的表格會比較 LiveEvent 類型。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150372"
---
# <a name="live-event-types-comparison"></a>實況活動類型比較

在「Azure 媒體服務」中，[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)可以是以下兩種類型之一：即時編碼和傳遞。 

## <a name="types-comparison"></a>類型比較 

下表比較功能的即時事件類型。 型別會設定期間建立使用[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -在內部部署即時編碼器會傳送多個的位元速率資料流。 內嵌的串流會通過而不需任何進一步處理的即時事件。 
* **LiveEventEncodingType.Standard** -的內部部署即時編碼器會傳送單一位元速率串流的即時事件和媒體服務會建立多重位元速率資料流。 如果發佈摘要是 720p 或更高的解析度**Default720p**預設會將編碼的 6 位元速率解析/組 （遵循本文稍後的詳細資料）。
* **LiveEventEncodingType.Premium1080p** -的內部部署即時編碼器會傳送單一位元速率串流的即時事件和媒體服務會建立多重位元速率資料流。 Default1080p 預設指定輸出組的位元速率解析/組 （遵循本文稍後的詳細資料）。 

| 功能 | 傳遞實況活動 | 標準或 Premium1080p 即時事件 |
| --- | --- | --- |
| 單一位元速率輸入會在雲端編碼為多重位元速率 |否 |是 |
| 發佈摘要的影片最大解析度 |4K (4096x2160，每秒 60 格畫面) |1080p (1920x1088，每秒 30 格畫面)|
| 發佈摘要中層級數的建議上限|最多 12 個|一個音訊|
| 輸出中的層級數上限| 與輸入相同|最多 6 （請參閱下方的系統預設值）|
| 發佈摘要的彙總頻寬上限|60 Mbps|N/A|
| 發佈中單一層級的最大位元速率 |20 Mbps|20 Mbps|
| 支援多種語言音訊播放軌|是|否|
| 支援的輸入視訊轉碼器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支援的輸出視訊轉碼器|與輸入相同|H.264/AVC|
| 支援的視訊位元深度、輸入及輸出|最多 10 位元，包括 HDR 10/HLG|8 位元|
| 支援的輸入音訊轉碼器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支援的輸出音訊轉碼器|與輸入相同|AAC-LC|
| 輸出影片的影片解析度上限|與輸入相同|標準-720p，Premium1080p-1080p|
| 輸入視訊的最大的畫面播放速率|每秒 60 畫面格數|標準或 Premium1080p-30 框架/秒|
| 輸入通訊協定|RTMP，分散式 MP4 (Smooth Streaming)|RTMP，分散式 MP4 (Smooth Streaming)|
| 價格|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|
| 最長執行時間| 24 小時 x 365 天，即時線性 | 多達 24 小時|
| 能夠透過內嵌的 CEA 608/708 字幕資料傳遞|是|是|
| 插入靜態圖像支援|否|否|
| 支援透過 API 發出廣告訊號| 否|否|
| 支援透過 SCTE-35 頻內訊息發出廣告訊號|是|是|
| 在比重摘要內能從短暫延遲中復原的能力|是|部分|
| 支援未統一輸入的 GOP|是|否 – 輸入必須有固定的 GOP 持續期間|
| 支援變動畫面播放速率輸入|是|否 – 輸入必須為固定畫面播放速率。 輕微的差異可以接受，例如：處於高速動態場景的情況。 但比重摘要無法卸除的畫面播放速率 （例如，為 15 框架/秒）。|
| 在遺失輸入摘要時自動關閉實況活動|否|經過 12 個小時，如果沒有 LiveOutput 仍在執行|

## <a name="system-presets"></a>系統預設值

解析度和位元速率的即時編碼器的輸出中包含取決於[presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)。 如果使用**標準**即時編碼器 (LiveEventEncodingType.Standard)，則*Default720p*預設都會指定一組 6 位元速率解析/組，如下所述。 否則，如果使用**Premium1080p**即時編碼器 (LiveEventEncodingType.Premium1080p)，則*Default1080p*預設所指定的位元速率解析/組輸出集。

> [!NOTE]
> 您無法套用 Default1080p 預設為即時的事件，如果已設定為標準的即時編碼-您會收到錯誤。 如果您嘗試套用預設為 Premium1080p 即時編碼器 Default720p，也會收到錯誤。

### <a name="output-video-streams-for-default720p"></a>Default720p 輸出視訊資料流

如果發佈摘要是 720p 或更高的解析度**Default720p**預設會將編碼成下列 6 個層級的摘要。 下表中位元速率是以 kbps 為單位，MaxFPS 代表該的最大允許的畫面播放速率 （在框架/秒），設定檔代表使用的 H.264 設定檔。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果您需要自訂即時編碼的預設值，請開啟支援票證，透過 Azure 入口網站。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為 720p，且最多只有 6 個圖層。 也就能指定要求的預設值為標準的即時編碼器。
> 可調整的特定值的位元速率與解析度，經過一段時間

### <a name="output-video-streams-for-default1080p"></a>輸出視訊資料流的 Default1080p

如果所佔比重摘要的 1080p 解析度**Default1080p**預設會將編碼成下列 6 個層級的摘要。

| Bitrate | 寬度 | 高度 | MaxFPS | 設定檔 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果您需要自訂即時編碼的預設值，請開啟支援票證，透過 Azure 入口網站。 您應指定解析度和位元速率的所需資料表。 ，請確認只有一個圖層中的 1080p，和最多 6 個圖層。 也就能指定要求的預設值為 Premium1080p 即時編碼器。
> 經過一段時間，您可以調整的位元速率和解析度的特定值。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>輸出音訊 Stream Default720p 和 Default1080p

兩個*Default720p*並*Default1080p*預設音訊編碼為 128 kbps 的立體聲 AAC LC。 取樣率會遵循的音訊資料軌在比重摘要。

## <a name="implicit-properties-of-the-live-encoder"></a>隱含的屬性的即時編碼器

上一節描述的即時編碼器，透過預設-例如圖層、 解析度及位元速率的數目可以明確地控制的屬性。 本章節將釐清隱含的屬性。

### <a name="group-of-pictures-gop-duration"></a>群組的圖片 (GOP) 持續時間

即時編碼器會遵循[GOP](https://en.wikipedia.org/wiki/Group_of_pictures)比重摘要-這表示輸出層會有相同的 GOP 持續期間的結構。 因此，建議您設定內部部署編碼器來產生有固定的 GOP 持續期間 （通常是 2 秒） 發佈摘要。 這可確保，從服務傳出 HLS 和 MPEG DASH 資料流也有固定的 GOP 持續時間。 可能大部分裝置所能容許時，會在 GOP 持續時間的小變化形式。

### <a name="frame-rate"></a>畫面播放速率

即時編碼器也會遵循個別的視訊畫面中所佔比重摘要-這表示輸出層會有相同的持續時間的畫面格的持續的時間。 因此，建議您設定內部部署編碼器來產生摘要具有固定畫面播放速率 （最多 30 框架/秒）。 這可確保，從服務傳出 HLS 和 MPEG DASH 資料流也有固定的畫面格速率持續時間。 畫面播放速率的小變化可能容許大部分的裝置，但不保證即時編碼器會產生正確地播放的輸出。 您的內部部署即時編碼器應該不會捨棄畫面 （例如。 電力偏低狀況） 或不同的畫面播放速率，以任何方式。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>解析所佔比重的摘要和輸出層

即時編碼器會設定以避免 upconverting 比重摘要。 因此最大解析度的輸出層不會超過的比重摘要。

比方說，如果您傳送摘要以 720p 即時的事件設定為 Default1080p 即時編碼，輸出只會有 5 個圖層，從 720p 3mbps，則移到 200 kbps 1080p。 或如果您要傳送在 360p 摘要成即時的事件，設定標準的即時編碼，則輸出會包含 3 個層級 （解析度 288 p、 216 p，和 192 p）。 在變質的情況下，如果您將說 160 x 90 像素的比重摘要傳送至標準的即時編碼器，輸出會包含一個圖層中 160 x 90 解析比重摘要的位元速率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>位元速率的比重摘要和輸出層

即時編碼器會設定為接受預設值，不論的位元速率發佈摘要中的位元速率設定。 如此一來可能會超過所佔比重摘要的輸出層的位元速率。 例如，如果您傳送摘要 720p 1 Mbps 的解析度，輸出層將維持相同[資料表](live-event-types-comparison.md#output-video-streams-for-default720p)上方。

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
