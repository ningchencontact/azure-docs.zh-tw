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
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 3ed38ce75e5ee7c9f05533d64b28171482bfec51
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725927"
---
# <a name="liveevent-types-comparison"></a>LiveEvent 類型比較

在 Azure 媒體服務中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 可以是以下兩種類型之一：即時編碼和即時通行。 

## <a name="types-comparison"></a>類型比較 

下表比較兩種 LiveEvent 類型的功能。

| 功能 | 即時通行 LiveEvent | 標準 LiveEvent |
| --- | --- | --- |
| 單一位元速率輸入會在雲端編碼為多重位元速率 |否 |是 |
| 發佈摘要的影片最大解析度 |4K (4096x2160，每秒 60 格畫面) |1080p (1920x1088，每秒 30 格畫面)|
| 發佈摘要中層級數的建議上限|最多 12 個|一個音訊|
| 輸出中的層級數上限| 與輸入相同|最多 7 個|
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
| 最長執行時間|24x365 即時線性|全天候|
| 能夠透過內嵌的 CEA 608/708 字幕資料傳遞|是|是|
| 插入靜態圖像支援|否|否|
| 支援透過 API 發出廣告訊號| 否|否|
| 支援透過 SCTE-35 頻內訊息發出廣告訊號|是|是|
| 在比重摘要內能從短暫延遲中復原的能力|是|否 (經過 6 秒以上且未有任何輸入資料時，LiveEvent 便會開始靜態圖像)|
| 支援未統一輸入的 GOP|是|否 – 輸入必須有固定的 GOP 持續期間|
| 支援變動畫面播放速率輸入|是|否 – 輸入必須為固定畫面播放速率。 輕微的差異可以接受，例如：處於高速動態場景的情況。 但是發佈摘要無法降低畫面播放速率 (例如，每秒 15 格畫面)。|
| 在輸入摘要遺失時自動關閉 LiveEvent|否|經過 12 個小時，如果沒有 LiveOutput 仍在執行|

## <a name="next-steps"></a>後續步驟

[即時串流概觀](live-streaming-overview.md)
