---
title: 使用 Azure 媒體服務之即時串流的概觀 | Microsoft Docs
description: 本文提供使用 Azure 媒體服務 v3 進行即時串流的概觀。
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
ms.date: 11/26/2018
ms.author: juliako
ms.openlocfilehash: 634563a2010562e20691abae132dc7540ef8faf2
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632690"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒體服務 v3 進行即時串流

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要使用媒體服務串流實況活動，您需要下列項目：  

- 拍攝實況活動用的相機。
- 即時視訊編碼器，可將相機 (或膝上型電腦等其他裝置) 中的訊號轉換成發佈摘要，再傳送至媒體服務。 發佈摘要可以包含廣告相關訊號，例如 SCTE-35 標記。
- 媒體服務中的元件可讓您內嵌、預覽、封裝、記錄、加密實況活動，並向客戶廣播這些活動，或是向 CDN 廣播以進一步發佈。

本文提供詳細的概觀、指引，並包含使用媒體服務進行即時串流所需之主要元件的圖表。

## <a name="overview-of-main-components"></a>主要元件概觀

若要使用媒體服務傳遞隨選或即時串流，至少需要一個 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)。 建立媒體服務帳戶時，**預設**的 StreamingEndpoint 會新增至「已停止」狀態的帳戶。 您必須啟動要將內容串流至檢視者的 StreamingEndpoint。 您可以使用預設的 **StreamingEndpoint**，或是使用需要的設定和 CDN 設定建立另一個自訂的 **StreamingEndpoint**。 您可以決定啟用多個 StreamingEndpoint，每個針對的 CDN 都不同，而且可為內容傳遞提供唯一的主機名稱。 

在媒體服務中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 會負責內嵌和處理即時影片摘要。 當您建立 LiveEvent 時系統會建立輸入端點，您可用此端點從遠端編碼器傳送有效的訊號。 遠端即時編碼器使用 [RTMP](https://en.wikipedia.org/wiki/Real-Time_Messaging_Protocol) 或是 [Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming#Microsoft_Smooth_Streaming) (分散式 MP4) 通訊協定，傳送發佈摘要至該輸入端點。  

一旦 **LiveEvent** 開始接收發佈摘要時，您可以使用其預覽端點 (預覽 URL) 來預覽及驗證是否在進一步發佈之前接收到即時串流。 確認預覽串流良好之後，您就可以使用 LiveEvent 讓即時串流透過一或多個 (預先建立的) **Streamingendpoint** 傳遞。 若要達成此目的，請在 **LiveEvent** 上建立新的 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)。 

**LiveOutput** 物件如同磁帶錄音機，可捕捉即時串流並記錄到媒體服務帳戶中的「資產」。 記錄的內容將會保存到您帳戶附加的 Azure 儲存帳戶，以及「資產」資源所定義的容器。  **LiveOuput** 也可讓您控制傳出的即時串流部分屬性，例如封存記錄中保留多少串流 (譬如雲端 DVR 的容量)。 磁碟上的封存檔是循環封存「時間」，只會保留 **LiveOutput** 的 **archiveWindowLength** 屬性中指定的內容量。 超出此時間的內容會自動從儲存容器中捨棄，而且無法復原。 您可以使用不同的封存長度和設定，在 LiveEvent 建立多個 LiveOutput (最多三個)。  

有了媒體服務，您就能充分運用**動態封裝**，從您要傳送至該服務的發佈摘要來預覽和廣播 [MPEG DASH、HLS 及 Smooth Streaming 格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)的即時串流。 您的檢視者可以搭配支援 HLS、DASH 或 Smooth Streaming 的播放器播放即時串流。 您可以在網路或行動應用程式中使用 [Azure 媒體播放器](http://amp.azure.net/libs/amp/latest/docs/index.html)，透過這些通訊協定中的任何一個傳遞串流。

媒體服務可讓您傳遞利用進階加密標準 (AES-128) 或下列三個主要數位版權管理 (DRM) 系統中任一個所動態加密 (**動態加密**) 的內容：Microsoft PlayReady、Google Widevine 和 Apple FairPlay。 媒體服務也提供服務，傳遞 AES 金鑰和 DRM 授權給授權用戶端。 如需如何使用媒體服務加密內容的詳細資訊，請參閱[保護內容概觀](content-protection-overview.md)

如有需要，您也可以套用 [動態篩選]，這可用來控制傳送至播放器的曲目編號、格式、位元速率及呈現方式時間範圍。 

### <a name="new-capabilities-for-live-streaming-in-v3"></a>v3 中的即時串流的新功能

有了媒體服務的 v3 API，您就能享用下列新功能：

- 新的低延遲模式。 如需詳細資訊，請參閱[延遲](live-event-latency.md)。
- 改進的 RTMP 支援 (更高的穩定性及更多來源編碼器支援)。
- RTMPS 安全內嵌。<br/>當您建立 LiveEvent 時，您會取得 4 個內嵌 URL。 4 個內嵌 URL 幾乎完全相同，並有相同的串流權杖 (AppId)，只有連接埠號碼部分不同。 其中兩個 URL 是 RTMPS 的主要部分和備份。   
- 使用媒體服務將單一位元速率貢獻饋送轉碼為有多個位元速率的輸出資料流時，您可以串流處理長達 24 小時的即時事件。 

## <a name="liveevent-types"></a>LiveEvent 類型

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 可以是下列兩種類型之一：即時通行和即時編碼。 

### <a name="pass-through"></a>傳遞

![即時通行](./media/live-streaming/pass-through.png)

使用即時通行 LiveEvent 時，您會依賴內部部署即時編碼器來產生多重位元速率視訊串流，並將其作為發佈摘要傳送至 LiveEvent (使用 RTMP 或 分散式 MP4 通訊協定)。 接著 LiveEvent 會傳達傳入的視訊串流，不會再進一步處理。 這類即時通行 LiveEvent 最適合長時間執行的實況活動，或是 24x365 的線性即時串流。 建立這種 LiveEvent 類型時，請指定 [無] (LiveEventEncodingType.None)。

您可以使用 H.264/AVC 或 H.265/HEVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 4K 的解析度和每秒 60 個畫面的畫面播放速率傳送發佈摘要。  如需更多詳細資料，請參閱 [LiveEvent 類型比較與限制](live-event-types-comparison.md)文章。

> [!NOTE]
> 如果您在很長一段時間內進行多個事件，而且已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
> 

請參閱 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) 中的實際範例。

### <a name="live-encoding"></a>即時編碼  

![即時編碼](./media/live-streaming/live-encoding.png)

使用即時編碼和媒體服務時，您可以設定內部部署即時編碼器將單一位元速率視訊作為發佈摘要，傳送至 LiveEvent (使用 RTMP 或 分散式 MP4 通訊協定)。 LiveEvent 將傳入的單一位元速率串流編碼成[多重位元速率視訊串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，使其能夠傳遞，以便透過 MPEG-DASH、HLS 及 Smooth Streaming 等通訊協定播放裝置。 建立這種 LiveEvent 類型時，請將編碼類型指定為 [基本]\(LiveEventEncodingType.Basic\)。

您可以使用 H.264/AVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 1080p 的解析度和每秒 30 個畫面的畫面播放速率傳送發佈摘要。 如需更多詳細資料，請參閱 [LiveEvent 類型比較與限制](live-event-types-comparison.md)文章。

## <a name="liveevent-types-comparison"></a>LiveEvent 類型比較

下列文章的資料表會比較兩個 LiveEvent 類型的功能：[比較](live-event-types-comparison.md)。

## <a name="liveoutput"></a>LiveOutput

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 可讓您控制傳出即時資料流的屬性，例如錄製了多少資料流 (例如雲端 DVR 的容量)，以及檢視者是否可以開始觀看即時資料流。 **LiveEvent** 和其 **LiveOutput** 之間的關聯性就像傳統的電視廣播，其中頻道 (**LiveEvent**) 表示持續的視訊資料流，而錄製 (**LiveOutput**) 的範圍限制在特定時間區段 (例如下午 6:30 至下午 7:00 的晚間新聞)。 您可以使用數位視訊錄影機 (DVR) 錄製電視，而 LiveEvent 中對等的功能是透過 ArchiveWindowLength 屬性來管理。 它是 ISO-8601 時間範圍持續時間 (例如 PTHH:MM:SS)，可指定 DVR 的容量，並且可設定為最小 3 分鐘至最大 25 小時的值。


> [!NOTE]
> **LiveOutput** 在建立時開始，並在刪除時結束。 當您刪除 **LiveOutput** 時，您不是刪除基礎的 **Asset** 和 Asset 中的內容。  

如需詳細資訊，請參閱[使用雲端 DVR](live-event-cloud-dvr.md)。

## <a name="streamingendpoint"></a>StreamingEndpoint

讓資料流流入 **LiveEvent** 之後，便可建立**資產**、**LiveOutput** 及 **StreamingLocator** 來開始串流事件。 這會封存資料流，並透過 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 將它提供給檢視器。

建立媒體服務帳戶時，預設串流端點會新增至「已停止」狀態的帳戶。 若要開始串流內容並利用動態封裝和動態加密功能，您想要串流內容的串流端點必須處於「執行中」狀態。

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />LiveEvent 狀態和計費

LiveEvent 只要其狀態轉換為「**執行中**」，即會開始計費。 若要停止 LiveEvent 計費，您必須停止 LiveEvent。

如需詳細資訊，請參閱[狀態和計費](live-event-states-billing.md)。

## <a name="latency"></a>Latency

如需 LiveEvent 延遲的詳細資訊，請參閱[延遲](live-event-latency.md)。

## <a name="live-streaming-workflow"></a>即時串流工作流程

即時串流工作流程的步驟如下：

1. 建立 LiveEvent。
2. 建立新的資產物件。
3. 建立 LiveOutput 並使用您建立的資產名稱。
4. 如果想要使用 DRM 加密內容，請建立「串流原則」和「內容金鑰」。
5. 如果不是使用 DRM，請使用內建的「串流原則」類型來建立「串流定位器」。
6. 列出「串流原則」上的路徑，以取得要使用的 URL (這些具有決定性)。
7. 取得您想要串流的來源「串流端點」主機名稱。 
8. 結合步驟 6 的 URL 和步驟 7 的主機名稱，即可取得完整的 URL。

如需詳細資訊，請參閱以 [Live .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) 範例為基礎的[即時串流教學課程](stream-live-tutorial-with-api.md)。

## <a name="next-steps"></a>後續步驟

- [LiveEvent 類型比較](live-event-types-comparison.md)
- [狀態和計費](live-event-states-billing.md)
- [延遲](live-event-latency.md)
