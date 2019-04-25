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
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322279"
---
# <a name="live-event-types-comparison"></a>實況活動類型比較

在「Azure 媒體服務」中，[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)可以是以下兩種類型之一：即時編碼和傳遞。 

## <a name="types-comparison"></a>類型比較 

下表比較兩種「實況活動」類型的功能。

| 功能 | 傳遞實況活動 | 標準實況活動 |
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
| 輸出影片的影片解析度上限|與輸入相同|720p (每秒 30 格畫面)|
| 輸入通訊協定|RTMP，分散式 MP4 (Smooth Streaming)|RTMP，分散式 MP4 (Smooth Streaming)|
| 價格|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/media-services/) 並按一下 [即時影片] 索引標籤|
| 最長執行時間| 24 小時 x 365 天，即時線性 | 多達 24 小時|
| 能夠透過內嵌的 CEA 608/708 字幕資料傳遞|是|是|
| 插入靜態圖像支援|否|否|
| 支援透過 API 發出廣告訊號| 否|否|
| 支援透過 SCTE-35 頻內訊息發出廣告訊號|是|是|
| 在比重摘要內能從短暫延遲中復原的能力|是|否 (在超過 6 秒沒有任何輸入資料之後，「實況活動」便會開始顯示靜態圖像)|
| 支援未統一輸入的 GOP|是|否 – 輸入必須有固定的 GOP 持續期間|
| 支援變動畫面播放速率輸入|是|否 – 輸入必須為固定畫面播放速率。 輕微的差異可以接受，例如：處於高速動態場景的情況。 但是發佈摘要無法降低畫面播放速率 (例如，每秒 15 格畫面)。|
| 在遺失輸入摘要時自動關閉實況活動|否|經過 12 個小時，如果沒有 LiveOutput 仍在執行|

## <a name="system-presets"></a>系統預設值

使用即時編碼時 ([即時事件] 設定為 [標準])，編碼預設值會定義傳入的資料流如何編碼為多重位元速率或圖層。 目前，唯一允許值的預設值*Default720p* （預設值）。

**Default720p** 會將視訊編碼成下列 6 層。

### <a name="output-video-stream"></a>輸出視訊串流

| 位元速率 | 寬度 | 高度 | MaxFPS | 設定檔 | 輸出串流名稱 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |高 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |高 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |高 |Video_512x288_850kbps |
| 550 |384 |216 |30 |高 |Video_384x216_550kbps |
| 200 |340 |192 |30 |高 |Video_340x192_200kbps |

> [!NOTE]
> 如果您需要使用自訂的即時編碼預設值，請連絡 amshelp@microsoft.com。 您應指定解析度和位元速率的所需資料表。 請確認只有一個圖層為 720p，且最多只有 6 個圖層。

### <a name="output-audio-stream"></a>輸出音訊串流

音訊編碼為 128 kbps 的立體聲 AAC-LC，取樣率為 48 kHz。

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
