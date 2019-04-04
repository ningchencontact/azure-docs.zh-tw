---
title: 影片索引子的場景、 擷取畫面和主要畫面格-Azure
titlesuffix: Azure Media Services
description: 本主題提供概觀影片索引器場景、 擷取畫面和主要畫面格。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896312"
---
# <a name="scenes-shots-and-keyframes"></a>場景、 擷取畫面和主要畫面格

分割成時態單位結構化及語意屬性為基礎的影片的影片索引子支援。 這項功能可讓客戶輕鬆地瀏覽、 管理及編輯它們以不同的資料粒度的視訊內容。 例如，根據場景、 擷取畫面和主要畫面格，本主題中所述。 **場景偵測**功能目前處於預覽狀態。   

![場景、 擷取畫面和主要畫面格](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>場景偵測 （預覽）

影片索引器會決定場景是根據視覺提示的視訊中的變更時。場景描述單一事件，它由一系列的語意與相關的連續擷取畫面所組成。 場景縮圖是其基礎的擷取畫面中的第一個主要畫面格。 影片索引器分成根據色彩一致性跨連續擷取畫面的背景的影片，並擷取開頭和結束時間的每個場景。 場景偵測會被視為具挑戰性的工作，因為它涉及量化語意層面的影片。

> [!NOTE]
> 適用於包含至少 3 個場景的影片。

## <a name="shot-detection"></a>分鏡偵測

影片索引器會判斷追蹤突然和漸進式轉換色彩配置中的相鄰的畫面格的視覺提示，根據影片中擷取變更時。 擷取的中繼資料包含開始和結束時間，以及包含該擷取畫面中的主要畫面格的清單。 擷取畫面是在同一時間從相同的相機採取個連續畫面格。

## <a name="keyframe-detection"></a>主要畫面格偵測

選取最能代表螢幕擷取畫面格。 主要畫面格是代表整個美觀的屬性 （例如，對比和 stableness） 為基礎的視訊從選取的畫面格。 影片索引器會擷取一份主要畫面格識別碼擷取的中繼資料，根據哪些客戶可以擷取主要畫面格的縮圖的一部分。 

主要畫面格是在 JSON 輸出中的擷取畫面與相關聯。 

## <a name="next-steps"></a>後續步驟

[檢查影片索引器輸出所產生的 API](video-indexer-output-json-v2.md#scenes)