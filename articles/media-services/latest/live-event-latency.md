---
title: Azure 媒體服務中的 LiveEvent 延遲 | Microsoft Docs
description: 本主題提供 LiveEvent 延遲的概觀並示範如何設定低延遲。
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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619806"
---
# <a name="liveevent-latency-in-media-services"></a>媒體服務中的 LiveEvent 延遲

本文示範如何在 **LiveEvent** 上設定低延遲。 它也會討論您在各種播放器中使用低延遲設定時會看到的典型結果。 結果會隨著 CDN 和網路延遲而有所不同。 

若要使用新的 **LowLatency** 功能，您可以在 **LiveEvent** 上將 **StreamOptionsFlag** 設定為 **LowLatency**。 啟動並執行資料流後，您可以使用 [Azure 媒體播放器](http://ampdemo.azureedge.net/) (AMP) 示範頁面，並將播放選項設定為使用 [低延遲啟發學習法設定檔]。

下列 .NET 範例會示範如何在 **LiveEvent** 上設定 **LowLatency**：

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

參閱完整範例：[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)。

## <a name="pass-through-liveevents-latency"></a>傳遞 LiveEvent 延遲

下表會顯示媒體服務中延遲 (啟用 LowLatency 旗標時) 的典型結果，其測量方式為從發佈摘要抵達服務到播放器可以要求播放之間的時間。

||已啟用 2 秒 GOP 低延遲|已啟用 1 秒 GOP 低延遲|
|---|---|---|
|AMP 中的 DASH|10 秒|8 秒|
|原生 iOS 播放程式上的 HLS|14 秒|10 秒|

> [!NOTE]
> 端對端延遲可能會因區域網路狀況或引進 CDN 快取層而有所不同。 建議您測試實際的設定。

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

