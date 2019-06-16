---
title: Azure 媒體服務 LiveEvent 類型 | Microsoft Docs
description: 本文內有 LiveEvent 類型的詳細比較表。
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075012"
---
# <a name="live-event-types-comparison"></a>實況活動類型比較

在「Azure 媒體服務」中，[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)可以是以下兩種類型之一：即時編碼和傳遞。 

## <a name="types-comparison"></a>類型比較 

下表比較功能的即時事件類型。

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

從即時編碼器輸出中所包含的位元速率與解析度取決[presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)。 如果使用**標準**即時編碼器 (LiveEventEncodingType.Standard)，則*Default720p*預設都會指定一組 6 個的解析/位元速率組，如下所述。 否則，如果使用**Premium1080p**即時編碼器 (LiveEventEncodingType.Premium1080p)，則*Default1080p*預設的比較輸出解析度/位元速率組。 

### <a name="output-video-streams-for-default720p"></a>Default720p 輸出視訊資料流

**Default720p**會將輸入的視訊編碼成下列 6 個圖層。

| 位元速率 | 寬度 | 高度 | MaxFPS | 設定檔 | 輸出串流名稱 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |高 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |高 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |高 |Video_512x288_850kbps |
| 550 |384 |216 |30 |高 |Video_384x216_550kbps |
| 200 |340 |192 |30 |高 |Video_340x192_200kbps |

> [!NOTE]
> 如果您需要自訂即時編碼的預設值，請開啟支援票證，透過 Azure 入口網站。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為 720p，且最多只有 6 個圖層。 也就能指定要求的預設值為標準的即時編碼器。

### <a name="output-video-streams-for-default1080p"></a>輸出視訊資料流的 Default1080p

**Default1080p**會將輸入的視訊編碼成下列 6 個圖層。

| 位元速率 | 寬度 | 高度 | MaxFPS | 設定檔 | 輸出串流名稱 |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |高 |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |高 |Video_960x540_1600kbps |
| 800 |640 |360 |30 |高 |Video_640x360_800kbps |
| 400 |480 |270 |30 |高 |Video_480x270_400kbps |
| 200 |320 |180 |30 |高 |Video_320x180_200kbps |

> [!NOTE]
> 如果您需要自訂即時編碼的預設值，請開啟支援票證，透過 Azure 入口網站。 您應指定解析度和位元速率的所需資料表。 ，請確認只有一個圖層中的 1080p，和最多 6 個圖層。 也就能指定要求的預設值為 Premium1080p 即時編碼器。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>輸出音訊 Stream Default720p 和 Default1080p

兩個*Default720p*並*Default1080p*預設音訊編碼為立體聲 AAC-LC 128 kbps，取樣率的 48 kHz。

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
