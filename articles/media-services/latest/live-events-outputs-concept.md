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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605659"
---
# <a name="live-events-and-live-outputs"></a>即時事件與即時輸出

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要設定媒體服務 v3 中的即時串流事件，您需要了解本文所討論的概念。

> [!TIP]
> 媒體服務 v2 Api，從移轉的客戶**即時事件**實體取代**通道**v2 中並**Live 輸出**取代**程式**.

## <a name="live-events"></a>即時活動

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)負責內嵌和處理即時視訊摘要。 當您建立即時的事件時，會建立主要和次要的輸入的端點，可用來從遠端的編碼器傳送有效的訊號。 遠端的即時編碼器會傳送輸入端點使用的比重摘要[RTMP](https://www.adobe.com/devnet/rtmp.html)或是[Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (分散 MP4) 輸入通訊協定。 將內容 RTMP 內嵌通訊協定，可以傳送純文字 (`rtmp://`) 或在網路上安全地加密 (`rtmps://`)。 針對 Smooth Streaming 內嵌通訊協定，支援的 URL 配置為 `http://` 或 `https://`。  

## <a name="live-event-types"></a>實況活動類型

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)可以是下列兩種類型之一：傳遞和即時編碼。 型別會設定期間建立使用[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -在內部部署即時編碼器會傳送多個的位元速率資料流。 內嵌的串流會通過而不需任何進一步處理的即時事件。 
* **LiveEventEncodingType.Standard** -的內部部署即時編碼器會傳送單一位元速率串流的即時事件和媒體服務會建立多重位元速率資料流。 如果發佈摘要是 720p 或更高的解析度**Default720p**預設會將編碼的 6 位元速率解析/組。
* **LiveEventEncodingType.Premium1080p** -的內部部署即時編碼器會傳送單一位元速率串流的即時事件和媒體服務會建立多重位元速率資料流。 Default1080p 預設指定位元速率解析/組輸出的集。 

### <a name="pass-through"></a>傳遞

![即時通行](./media/live-streaming/pass-through.svg)

使用傳遞**實況活動**時，您會依賴內部部署即時編碼器來產生多重位元速率視訊資料流，然後將其當作發佈摘要傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會接著完成傳入的視訊資料流，而不會再進一步處理。 這類傳遞即時事件最適合用於長時間執行的即時事件，或 24 x 365 線性即時資料流。 建立這類型的「實況活動」時，請指定 [無] (LiveEventEncodingType.None)。

您可以使用 H.264/AVC 或 H.265/HEVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 4K 的解析度和每秒 60 個畫面的畫面播放速率傳送發佈摘要。  如需更多詳細資料，請參閱[實況活動類型比較](live-event-types-comparison.md)一文。

> [!NOTE]
> 如果您在很長一段時間內進行多個事件，而且已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
> 

請參閱 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).中的 .NET 程式碼範例。

### <a name="live-encoding"></a>即時編碼  

![即時編碼](./media/live-streaming/live-encoding.svg)

搭配「媒體服務」使用即時編碼時，您會設定讓內部部署即時編碼器將單一位元速率視訊當作發佈摘要，傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 然後設定即時的事件，讓它將編碼該內送單一位元速率串流處理至[多重位元速率視訊資料流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，並傳遞要播放裝置透過通訊協定，例如 MPEG DASH、 HLS、 Smooth 提供輸出資料流。

當您使用即時編碼時，您可以傳送摘要只解析度 30 框架/AAC H.264/AVC 視訊轉碼器與第二，畫面播放速率的 1080p 解析度的比重 （AAC LC、 HE-AACv1 或 HE-AACv2） 音訊轉碼器。 請注意，傳遞即時事件，可支援解析度最高到 4k 在 60 框架/秒。 如需更多詳細資料，請參閱[實況活動類型比較](live-event-types-comparison.md)一文。

從即時編碼器輸出中所包含的位元速率與解析度取決，預設值。 如果使用**標準**即時編碼器 (LiveEventEncodingType.Standard)，則*Default720p*預設都會指定一組 6 個解析/位元速率組，從 720p 3.5Mbps 到 200 kbps 192 p。 否則，如果使用**Premium1080p**即時編碼器 (LiveEventEncodingType.Premium1080p)，則*Default1080p*預設都會指定一組 6 個解析/位元速率組，從 1080p 3.5Mbps 在到 200 kbps 180p。 如需相關資訊，請參閱[系統預設值](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果您需要自訂即時編碼的預設值，請開啟支援票證，透過 Azure 入口網站。 您應指定解析度和位元速率的所需資料表。 ，請確認只有一個圖層 （如果要求的標準的即時編碼器的預設值） 的 720p 或 1080p （如果要求 Premium1080p 即時編碼器的預設值），和最多 6 個圖層。

## <a name="live-event-creation-options"></a>實況活動建立選項

建立「實況活動」時，您可以指定下列選項：

* 「實況活動」的串流通訊協定 (目前支援 RTMP 和 Smooth Streaming 通訊協定)。<br/>當「實況活動」或其相關「實況輸出」正在執行時，您無法變更通訊協定選項。 如果您需要不同的通訊協定，則應該為每個串流通訊協定建立個別的「實況活動」。  
* 內嵌和預覽的 IP 限制。 您可以定義獲允許將視訊內嵌到這個「實況活動」的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。<br/>如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。<br/>IP 位址必須採用下列其中一個格式：具有 4 個數字、CIDR 位址範圍的 IpV4 位址。
* 在建立事件時，您可以指定要自動加以啟動。 <br/>當自動啟動設為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。 或者，您可以在準備好開始進行串流處理時啟動事件。 

    如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。

## <a name="live-event-ingest-urls"></a>實況活動內嵌 URL

建立「實況活動」之後，您即可取得將提供給即時內部部署編碼器的內嵌 URL。 即時編碼器會使用這些 URL 來輸入即時資料流。 如需詳細資訊，請參閱[建議的內部部署即時編碼器](recommended-on-premises-live-encoders.md)。 

您可以使用非虛名 URL 或虛名 URL。 

> [!NOTE] 
> 針對要預測的內嵌 URL，設定「虛名」模式。

* 非虛名 URL

    非虛名 URL 是媒體服務 v3 中的預設模式。 您可能會快速取得「實況活動」，但只有在實況活動開始時，才會知道內嵌 URL。 如果您停止/開始「實況活動」，URL 將會變更。 <br/>在使用者想要使用應用程式來進行串流處理，而該應用程式想要儘快取得實況活動且取得動態內嵌 URL 不是問題的情況下，「非虛名」會相當有用。
    
    如果用戶端應用程式不需要建立預先產生的內嵌 URL 在即時事件之前，只讓自動產生存取權杖的即時活動的媒體服務。
* 虛名 URL

    大型媒體廣播者如果使用硬體廣播編碼器，而不想要在開始「實況活動」時重新設定其編碼器，就會偏好使用「虛名」模式。 他們會想要有不會隨著時間改變的預測性內嵌 URL。
    
    若要指定此模式中，您設定`vanityUrl`要`true`在建立時 (預設值是`false`)。 您也需要傳遞您自己的存取權杖 (`LiveEventInput.accessToken`) 在建立時。 您指定的語彙基元的值，以避免在 URL 中的隨機權杖。 存取權杖必須是有效的 GUID 字串 （不論有無連字號）。 一旦將模式設定無法更新。

    存取權杖必須是唯一的資料中心內。 如果您的應用程式需要使用虛名 URL，建議一律建立您的存取權杖 （而不是重複使用任何現有的 GUID） 的新 GUID 執行個體。 

    使用下列 Api 啟用虛名 URL，並將存取權杖設定為有效的 GUID (例如`"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`)。  
    
    |語言|啟用虛名 URL|設定存取權杖|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>即時內嵌 URL 命名規則

* 下方的 *random* \(隨機\) 字串是 128 位元的十六進位數字 (由 32 個字元的 0-9 a-f 所組成)。
* *您的存取權杖*-使用虛名模式時，您會設定有效的 GUID 字串。 例如： `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 。
* *資料流名稱*-指出特定連線的資料流名稱。 資料流名稱值通常會加入您所使用的即時編碼器。 您可以設定即時編碼器使用任何名稱來描述連接，例如:"video1_audio1"，"video2_audio1"，"stream"。

#### <a name="non-vanity-url"></a>非虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>實況活動預覽 URL

即時事件開始後接收的比重摘要，您可以使用其預覽端點，來預覽及驗證您進一步在發佈之前收到的即時資料流。 您已核取預覽資料流是很好之後，您可以使用即時事件，若要提供給透過一或多個 （預先建立的） 串流端點傳遞即時資料流。 若要達成此目的，您建立新[Live 輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)即時事件。 

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

## <a name="live-event-long-running-operations"></a>即時事件長時間執行的作業

如需詳細資訊，請參閱[長時間執行的作業](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>即時輸出

讓資料流流入「實況活動」之後，您即可建立[資產](https://docs.microsoft.com/rest/api/media/assets)、[實況輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)及[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)來開始串流事件。 「即時事件」會封存資料流，並透過[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)將它提供給檢視者。  

> [!NOTE]
> 「實況輸出」會在建立時開始，並在刪除時結束。 當您刪除「實況輸出」時，所刪除的並不是基礎「資產」及資產中的內容。 

之間的關聯性**即時事件**及其**Live 輸出**是類似於傳統電視廣播，因此通道 （即時事件） 代表固定的視訊和錄製 （即時串流輸出） 的範圍限定為特定的時間區段 （例如晚上消息下午 6 點 30 到下午 7:00）。 您可以錄製電視使用數位視訊錄影機 (DVR) – 在即時事件對等的功能透過管理**archiveWindowLength**屬性。 它是 ISO-8601 時間範圍持續時間 (例如 PTHH:MM:SS)，可指定 DVR 的容量，並且可設定為最小 3 分鐘至最大 25 小時的值。

即時的輸出物件，就像是的磁帶將會攔截的錄製器 」 和 「 記錄到媒體服務帳戶中的資產的即時資料流。 記錄的內容將會保存到您帳戶附加的 Azure 儲存帳戶，以及「資產」資源所定義的容器。 Live 輸出也可讓您控制傳出的即時資料流，例如封存記錄 （例如，容量的雲端 DVR） 中的資料流中有多少會保留，而且檢視者啟動監看即時資料流的某些屬性。 在磁碟上的保存是循環封存 「 視窗 」，只會保留的 archiveWindowLength Live 輸出屬性中指定的內容量。 超出此時間的內容會自動從儲存容器中捨棄，而且無法復原。 即時事件時，使用不同的封存長度和設定，您可以建立多個即時輸出 （最多三個最大值）。  

如果您已經發行即時的輸出**Asset**使用**串流定位器**，即時事件 （最多 DVR 視窗長度） 將會繼續直到串流定位器的到期或刪除，就可以檢視視何者先。

如需詳細資訊，請參閱[使用雲端 DVR](live-event-cloud-dvr.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[即時串流教學課程](stream-live-tutorial-with-api.md)
