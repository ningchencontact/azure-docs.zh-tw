---
title: 影片索引子場景、螢幕擷取畫面和主要畫面格-Azure
titlesuffix: Azure Media Services
description: 本主題概述影片索引子的場景、快照和主要畫面格。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815646"
---
# <a name="scenes-shots-and-keyframes"></a>場景、擷取畫面和主要畫面格

影片索引子支援根據結構化和語義屬性, 將影片分割成時態單位。 這項功能可讓客戶根據不同的資料細微性, 輕鬆地流覽、管理及編輯其影片內容。 例如, 根據本主題中所述的場景、快照和主要畫面格。   

![場景、擷取畫面和主要畫面格](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>場景偵測  
 
影片索引子會根據視覺提示判斷影片中的場景變更時機。場景會描述單一事件, 而且它是由一系列與語義相關的連續快照所組成。 場景縮圖是其基礎快照的第一個主要畫面格。 影片索引子會根據連續拍照的色彩連貫性, 將影片分割成場景, 並抓取每個場景的開始和結束時間。 場景偵測被視為一項挑戰的工作, 因為它牽涉到量化影片的語義層面。

> [!NOTE]
> 適用于至少包含3個場景的影片。

## <a name="shot-detection"></a>分鏡偵測

影片索引子會根據視覺提示來決定影片中的快照集, 方法是追蹤相鄰框架的色彩配置中的突然和漸進轉換。 這個快照的中繼資料包括開始和結束時間, 以及該課程中所包含的主要畫面格清單。 此相片是一次從相同相機拍攝的連續畫面格。

## <a name="keyframe-detection"></a>主要畫面格偵測

選取最能代表該快照的框架。 主要畫面格是根據美觀屬性 (例如, 對比和 stableness) 從整段影片中選取的代表性框架。 影片索引子會根據哪些客戶可以抓取主要畫面格縮圖, 抓取作為拍照中繼資料一部分的主要畫面格識別碼清單。 

主要畫面格會與輸出 JSON 中的拍照相關聯。 

## <a name="next-steps"></a>後續步驟

[檢查 API 所產生的影片索引子輸出](video-indexer-output-json-v2.md#scenes)
