---
title: Azure 媒體服務中的即時串流概念 - 即時事件與即時輸出 |Microsoft Docs
description: 本文提供使用 Azure 媒體服務 v3 中即時串流概念的概觀。
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
ms.openlocfilehash: c4be56b3ee32a5177c66353ba45c6b3647c732f2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240077"
---
# <a name="live-events-and-live-outputs"></a>即時事件與即時輸出

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要在「媒體服務 v3」中設定即時串流事件，您必須了解本文中所討論的概念：

* [實況活動](#live-events)
* 實況活動類型
* 實況活動類型比較
* [實況活動建立選項](#live-event-creation-options)
* [實況活動內嵌 URL](#live-event-ingest-urls)
* [實況活動預覽 URL](#live-event-preview-url)
* [實況輸出](#live-outputs)。

## <a name="live-events"></a>即時活動

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)負責內嵌和處理即時視訊摘要。 當您建立「實況活動」時，系統會建立輸入端點，供您用來從遠端編碼器傳送即時訊號。 遠端即時編碼器使用 [RTMP](https://www.adobe.com/devnet/rtmp.html) 或是 [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (分散式 MP4) 通訊協定，傳送發佈摘要至該輸入端點。 針對 Smooth Streaming 內嵌通訊協定，支援的 URL 配置為 `http://` 或 `https://`。 RTMP 內嵌通訊協定支援的 URL 配置為 `rtmp://` 或 `rtmps://`。 

## <a name="live-event-types"></a>實況活動類型

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)可以是下列兩種類型之一：傳遞和即時編碼。 

### <a name="pass-through"></a>傳遞

![即時通行](./media/live-streaming/pass-through.svg)

使用傳遞**實況活動**時，您會依賴內部部署即時編碼器來產生多重位元速率視訊資料流，然後將其當作發佈摘要傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會接著完成傳入的視訊資料流，而不會再進一步處理。 這類即時通行 LiveEvent 最適合長時間執行的實況活動，或是 24x365 的線性即時串流。 建立這類型的「實況活動」時，請指定 [無] (LiveEventEncodingType.None)。

您可以使用 H.264/AVC 或 H.265/HEVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 4K 的解析度和每秒 60 個畫面的畫面播放速率傳送發佈摘要。  如需更多詳細資料，請參閱[實況活動類型比較](live-event-types-comparison.md)一文。

> [!NOTE]
> 如果您在很長一段時間內進行多個事件，而且已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
> 

請參閱 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).中的 .NET 程式碼範例。

### <a name="live-encoding"></a>即時編碼  

![即時編碼](./media/live-streaming/live-encoding.svg)

搭配「媒體服務」使用即時編碼時，您會設定讓內部部署即時編碼器將單一位元速率視訊當作發佈摘要，傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會將該項傳入的單一位元速率資料流編碼成[多重位元速率視訊資料流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，使其可供透過 MPEG-DASH、HLS 及 Smooth Streaming 等通訊協定，傳遞給播放裝置。 建立這類型的「實況活動」時，請將編碼類型指定為 [標準] (LiveEventEncodingType.Standard)。

您可以使用 H.264/AVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 1080p 的解析度和每秒 30 個畫面的畫面播放速率傳送發佈摘要。 如需更多詳細資料，請參閱[實況活動類型比較](live-event-types-comparison.md)一文。

使用即時編碼時 (即時事件設定為**標準**)，編碼預設值可讓您定義如何將內送資料流編碼成多個位元速率或圖層。 如需資訊，請參閱[系統預設](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 目前，唯一允許的預設的值為標準類型的即時事件*Default720p*。 如果您需要使用自訂的即時編碼預設值，請連絡amshelp@microsoft.com。 您應該指定解析度和位元速率的所需的資料表。 ，請確認只有一個圖層中的 720p，和最多 6 個圖層。

## <a name="live-event-creation-options"></a>實況活動建立選項

建立「實況活動」時，您可以指定下列選項：

* 「實況活動」的串流通訊協定 (目前支援 RTMP 和 Smooth Streaming 通訊協定)。<br/>當「實況活動」或其相關「實況輸出」正在執行時，您無法變更通訊協定選項。 如果您需要不同的通訊協定，則應該為每個串流通訊協定建立個別的「實況活動」。  
* 內嵌和預覽的 IP 限制。 您可以定義獲允許將視訊內嵌到這個「實況活動」的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。<br/>如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。<br/>IP 位址必須採用下列其中一個格式：具有 4 個數字、CIDR 位址範圍的 IpV4 位址。
* 在建立事件時，您可以指定要自動加以啟動。 <br/>當自動啟動設為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。 或者，您可以在準備好開始進行串流處理時啟動事件。 

    如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。

## <a name="live-event-ingest-urls"></a>實況活動內嵌 URL

建立「實況活動」之後，您即可取得將提供給即時內部部署編碼器的內嵌 URL。 即時編碼器會使用這些 URL 來輸入即時資料流。 如需詳細資訊，請參閱[建議的內部部署即時編碼器](recommended-on-premises-live-encoders.md)。 

您可以使用非虛名 URL 或虛名 URL。 

* 非虛名 URL

    非虛名 URL 是 AMS v3 中的預設模式。 您可能會快速取得「實況活動」，但只有在實況活動開始時，才會知道內嵌 URL。 如果您停止/開始「實況活動」，URL 將會變更。 <br/>在使用者想要使用應用程式來進行串流處理，而該應用程式想要儘快取得實況活動且取得動態內嵌 URL 不是問題的情況下，「非虛名」會相當有用。
* 虛名 URL

    大型媒體廣播者如果使用硬體廣播編碼器，而不想要在開始「實況活動」時重新設定其編碼器，就會偏好使用「虛名」模式。 他們會想要有不會隨著時間改變的預測性內嵌 URL。

> [!NOTE] 
> 為了讓內嵌 URL 可供預測，您需要使用「虛名」模式並傳遞您自己的存取權杖 (以避免在 URL 中使用隨機權杖)。

### <a name="live-ingest-url-naming-rules"></a>即時內嵌 URL 命名規則

下方的 *random* \(隨機\) 字串是 128 位元的十六進位數字 (由 32 個字元的 0-9 a-f 所組成)。<br/>
下方的 *access token* \(存取金鑰\) 是您必須為固定 URL 指定的存取金鑰。 它也是 128 位元的十六進位數字。

#### <a name="non-vanity-url"></a>非虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>實況活動預覽 URL

在**實況活動**開始接收發佈摘要之後，您便可以使用其預覽端點來預覽及驗證是否有在接收即時資料流，然後才進行進一步的發佈。 確認預覽資料流狀況良好之後，您就可以使用 LiveEvent 讓即時資料流可供透過一或多個 (預先建立的) **串流端點**進行傳遞。 若要達到此目的，您需在**實況活動**上建立新的[實況輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)。 

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

## <a name="live-outputs"></a>即時輸出

讓資料流流入「實況活動」之後，您即可建立[資產](https://docs.microsoft.com/rest/api/media/assets)、[實況輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)及[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)來開始串流事件。 「即時事件」會封存資料流，並透過[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)將它提供給檢視者。  

> [!NOTE]
> 「實況輸出」會在建立時開始，並在刪除時結束。 當您刪除「實況輸出」時，所刪除的並不是基礎「資產」及資產中的內容。 

**實況活動**與其**實況輸出**之間的關聯性類似於傳統的電視廣播，其中頻道 (**實況活動**) 代表持續的視訊資料流，而錄製 (**實況輸出**) 的範圍則限制在特定的時間區段 (例如下午 6:30 至下午 7:00 的晚間新聞)。 您可以使用「數位影像錄影機」(DVR) 來錄製電視，而「實況活動」中對等的功能則會透過 **ArchiveWindowLength** 屬性來管理。 它是 ISO-8601 時間範圍持續時間 (例如 PTHH:MM:SS)，可指定 DVR 的容量，並且可設定為最小 3 分鐘至最大 25 小時的值。

**實況輸出**物件就像磁帶錄音機，可捕捉即時資料流並記錄到您「媒體服務」帳戶的「資產」中。 記錄的內容將會保存到您帳戶附加的 Azure 儲存帳戶，以及「資產」資源所定義的容器。 **實況輸出**可讓您控制傳出即時資料流的一些屬性，像是封存中可保存多少資料流 (例如雲端 DVR 的容量)，以及檢視者是否可以開始觀看即時資料流。 磁碟上的封存是循環封存「時段」，只會保留**實況輸出**之 **archiveWindowLength** 屬性中指定的內容量。 超出此時間的內容會自動從儲存容器中捨棄，而且無法復原。 您可以使用不同的封存長度和設定，在**實況活動**建立多個**實況輸出** (最多三個)。  

如果您已使用**串流定位器**來發佈**實況輸出**的**資產**，**實況活動** (最長可達 DVR 時段長度) 將繼續可供檢視，直到「串流定位器」到期或被刪除 (以先到者為準) 為止。

如需詳細資訊，請參閱[使用雲端 DVR](live-event-cloud-dvr.md)。

## <a name="next-steps"></a>後續步驟

- [串流即時事件](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
