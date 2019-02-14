---
title: 篩選器和 Azure 媒體服務動態資訊清單 | Microsoft Docs
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來封存此選擇性資料流。
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
ms.date: 01/24/2019
ms.author: juliako
ms.openlocfilehash: 9c463740acf6ef464880a43e0e68de683b97f64f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813412"
---
# <a name="filters-and-dynamic-manifests"></a>篩選器與動態資訊清單

當提供您的內容給客戶 (串流處理即時事件或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔案中所述還大的彈性。 Azure 媒體服務可讓您為內容定義帳戶篩選器和資產篩選器。 

篩選器是伺服器端規則，可讓您的客戶執行下列動作： 

- 僅播放視訊的某個區段 (而非播放整個視訊)。 例如︰

    - 縮小資訊清單以顯示即時事件的子剪輯 (「子剪輯篩選」)，或
    - 修剪視訊開頭 (「修剪視訊」)。

- 只傳遞用來播放內容的裝置所支援的指定轉譯和/或指定的語言資料軌 (轉譯篩選)。 
- 調整簡報視窗 (DVR)，以在播放程式中提供長度有限的 DVR 視窗 (「調整簡報視窗」)。

本主題描述[概念](#concepts)並顯示篩選器定義。 接著提供常見案例的相關詳細資料。 您可以在本文的結尾找到說明如何以程式設計方式建立篩選器的連結。  

## <a name="concepts"></a>概念

### <a name="dynamic-manifests"></a>動態資訊清單

媒體服務根據預先定義的篩選器提供**動態資訊清單**。 定義篩選器之後，您的用戶端便可使用篩選器來串流處理視訊的特定轉譯或子剪輯。 用戶端會在資料流 URL 中指定篩選器。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。 

下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> 動態資訊清單不會變更資產和該資產的預設資訊清單。 您的用戶端可以選擇要求包含或不含篩選器的資料流。 
> 
> 

### <a name="manifest-files"></a>資訊清單檔案

當您將資產編碼以進行彈性位元速率資料流時，會建立 **資訊清單** (播放清單) 檔案 (此檔案是以文字或 XML 為基礎)。 **資訊清單** 檔案包含資料流中繼資料，例如：資料軌類型 (音訊、視訊或文字)、資料軌名稱、開始和結束時間、位元速率 (品質)、資料軌語言、簡報視窗 (持續時間固定的滑動視窗)，視訊轉碼器 (FourCC)。 此檔案也會透過提供下一個可播放視訊片段及其位置的相關資訊，來指示播放程式擷取下一個片段。 片段 (或區段) 實際上是視訊內容的「區塊」。

以下是 HLS 資訊清單檔案的範例： 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>取得並檢查資訊清單檔案

根據應將您資料流的哪個資料軌 (即時或點播視訊) 包含至動態建立的資訊清單中，來指定篩選器資料軌屬性條件清單。 您必須先載入 Smooth Streaming 資訊清單，才能取得並檢查資料軌的屬性。

[使用 .NET 上傳、編碼及串流處理檔案](stream-files-tutorial-with-api.md)教學課程說明如何使用 .NET 建置串流 URL (請參閱[建置 URL](stream-files-tutorial-with-api.md#get-streaming-urls) 一節)。 如果您執行應用程式，其中一個 URL 指向 Smooth Streaming 資訊清單：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/>複製該 URL 並將它貼上到瀏覽器的網址列中。 瀏覽器會下載該檔案。 您可以在您選擇的文字編輯器中開啟它。

如需 REST 範例，請參閱[使用 REST 上傳、編碼及串流處理檔案](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>監視視訊資料流的位元速率

您可以使用 [Azure 媒體播放器示範頁面](http://aka.ms/amp) \(英文\) 來監視視訊資料流的位元速率。 示範頁面會在 [Diagnostics] (診斷) 索引標籤中顯示診斷資訊：

![Azure 媒體播放器診斷][amp_diagnostics]

## <a name="defining-filters"></a>定義篩選器

資產篩選器有兩種： 

* [帳戶篩選器](https://docs.microsoft.com/rest/api/media/accountfilters) (全域) - 可以套用到 Azure 媒體服務帳戶中的任何資產，存留期和帳戶的相同。
* [資產篩選器](https://docs.microsoft.com/rest/api/media/assetfilters) (本機) - 只能套用篩選器建立時與其相關聯的資產，存留期和資產的相同。 

[帳戶篩選器](https://docs.microsoft.com/rest/api/media/accountfilters)和[資產篩選器](https://docs.microsoft.com/rest/api/media/assetfilters)類型對於定義/描述篩選器有完全相同的屬性。 除了在建立**資產篩選器**的時候，因為您需要指定要與篩選器相關聯之資產的名稱。

視您的案例而定，您會決定哪個類型的篩選器較合適 (資產篩選器或帳戶篩選器)。 帳戶篩選器適合裝置設定檔 (轉譯篩選)，資產篩選器可用於修剪特定資產。

您可以使用下列屬性來描述篩選器。 

|Name|說明|
|---|---|
|firstQuality|篩選器的首次品質位元速率。|
|presentationTimeRange|簡報時間範圍。 此屬性用於篩選資訊清單起始/結束點、簡報視窗長度，以及即時起始位置。 <br/>如需詳細資訊，請參閱 [PresentationTimeRange](#PresentationTimeRange)。|
|tracks|資料軌選取條件。 如需詳細資訊，請參閱[資料軌](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

將此屬性與**資產篩選器**搭配使用。 不建議搭配**帳戶篩選器**設定此屬性。

|Name|說明|
|---|---|
|**endTimestamp**|絕對結束時間界限。 適用於點播視訊 (VoD)。 對於即時簡報，系統會以無訊息方式忽略它並在簡報結束時套用，然後資料流會成為 VoD。<br/><br/>此值代表資料流的絕對結束點。 系統會將它四捨五入到最接近的下一個 GOP 起始。<br/><br/>使用 StartTimestamp 和 EndTimestamp 修剪播放清單 (資訊清單)。 例如，StartTimestamp=40000000 和 EndTimestamp = 100000000 會產生包含 StartTimestamp 和 EndTimestamp 之間媒體的播放清單。 如果片段跨越界限，則整個片段都會包含在資訊清單中。<br/><br/>此外，請參閱下列 **forceEndTimestamp** 定義。|
|**forceEndTimestamp**|適用於即時篩選器。<br/><br/>**forceEndTimestamp** 是布林值，指出 **endTimestamp** 是否設定為有效值。 <br/><br/>如果值為 **true**，就會指定 **endTimestamp** 值。 如果未指定，則會傳回不正確的要求。<br/><br/>例如，如果您想要在輸入視訊中定義在 5 分鐘時開始的篩選器，並且持續直到資料流結束，您可以將 **forceEndTimestamp** 設定為 false，並省略設定 **endTimestamp**。|
|**liveBackoffDuration**|僅適用於即時。 此屬性用於定義即時播放位置。 您可以使用此規則來延遲即時播放位置，並為播放程式建立伺服器端緩衝區。 LiveBackoffDuration 是相對於即時位置。 即時輪詢持續時間的最大值是 300 秒。|
|**presentationWindowDuration**|適用於即時。 使用 **presentationWindowDuration**，將滑動視窗套用至播放清單。 例如，設定 presentationWindowDuration=1200000000 以套用兩分鐘長的滑動視窗。 在即時邊緣 2 分鐘內的媒體都會包含在播放清單中。 如果片段跨越界限，則整個片段都會包含在播放清單中。 簡報視窗持續時間的最小值是 60 秒。|
|**startTimestamp**|適用於 VoD 或即時資料流。 此值代表資料流的絕對起始點。 系統會將此值四捨五入到最接近的下一個 GOP 起始。<br/><br/>使用 **startTimestamp** 和 **endTimestamp** 修剪播放清單 (資訊清單)。 例如，startTimestamp=40000000 和 endTimestamp = 100000000 會產生包含 StartTimestamp 和 EndTimestamp 之間媒體的播放清單。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**timescale**|適用於 VoD 或即時資料流。 timescale 是由上述指定的 timestamp 和 duration 使用。 預設的 timescale 為 10000000。 可以使用替代的 timescale。 預設值為 10000000 HNS (百奈秒)。|

### <a name="tracks"></a>資料軌

根據應將您資料流的哪個資料軌 (即時或點播視訊) 包含至動態建立的資訊清單中，來指定篩選器資料軌屬性條件 (FilterTrackPropertyConditions) 清單。 篩選器是使用邏輯 **AND** 和 **OR** 運算。

篩選器資料軌屬性條件描述資料軌類型、值 (下表中所述) 和運算 (Equal、NotEqual)。 

|Name|說明|
|---|---|
|**Bitrate**|使用資料軌的位元速率來篩選。<br/><br/>建議的值是位元速率範圍 (以每秒位元數為單位)。 例如，"0-2427000"。<br/><br/>注意：雖然您可以使用特定的位元速率值，如 250000 (每秒位元數)，但不建議使用此方法，因為資產之間的確切位元速率可能會變動。|
|**FourCC**|將資料軌的 FourCC 值用於篩選。<br/><br/>該值是轉碼器格式的第一個元素，如 [RFC 6381](https://tools.ietf.org/html/rfc6381) \(英文\) 中所指定。 目前支援下列轉碼器： <br/>視訊："avc1"、"hev1"、"hvc1"<br/>音訊："mp4a"、"ec-3"<br/><br/>若要判斷 Asset 中資料軌的 FourCC 值，請[取得並檢查資訊清單檔案](#get-and-examine-manifest-files)。|
|**語言**|使用資料軌的語言來篩選。<br/><br/>此值是您要包含之語言的標籤，如 RFC 5646 中所指定。 例如，"en"。|
|**名稱**|使用資料軌的名稱來篩選。|
|**類型**|使用資料軌的類型來篩選。<br/><br/>允許下列值："video"、"audio" 或 "text"。|

### <a name="example"></a>範例

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>轉譯篩選

您可以選擇將資產編碼成多個編碼設定檔 (H.264 Baseline、H.264 High、AACL、AACH、Dolby Digital Plus)，及多個高品質位元速率。 不過，並非所有的用戶端裝置都支援您所有的資產和位元速率。 例如，較舊的 Android 裝置只支援 H.264 Baseline+AACL。 將較高的位元速率傳送到無法獲益的裝置，會浪費頻寬和裝置計算。 這類裝置必須將所有指定的資訊解碼，才能縮小以顯示。

您可以使用動態資訊清單建立裝置設定檔，例如行動、主控台、HD/SD 等，並包括您想要納入設定檔中的資料軌和品質。

![轉譯篩選範例][renditions2]

下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 編碼資產可以動態地封裝至下列任何一個資料流通訊協定：HLS、MPEG DASH 和 Smooth。  圖表頂端顯示不含篩選器的資產其 HLS 資訊清單 (包含全部七個轉譯)。  左下角顯示名為 "ott" 的篩選器已套用到 HLS 資訊清單。 "ott" 篩選器指定要移除所有不到 1 Mbps 的位元速率，因此將最差的兩個品質等級從回應中去除。 右下角顯示名為 "mobile" 的篩選器已套用到 HLS 資訊清單中。 "mobile" 篩選器指定要移除的解析度大於 720p 的轉譯，因此去除兩個 1080p 的轉譯。

![轉譯篩選][renditions1]

## <a name="removing-language-tracks"></a>移除語言資料軌
您的資產可能包含多個音訊語言，例如英文、西班牙文、法文等。通常，播放程式 SDK 管理員會預設音訊資料軌選取範圍，以及每個使用者可選擇的可用音訊資料軌。 開發這類的播放程式 SDK 相當有挑戰性，因為在各個裝置特有的播放程式架構之間有不同的實作方式。 此外播放程式 API 在某些平台上受到限制，且不包含音訊選擇功能，因此使用者無法選取或變更預設的音訊資料軌。有了資產篩選器，您可以藉由建立只包含所需音訊語言的篩選器來控制行為。

![語言資料軌篩選][language_filter]

## <a name="trimming-start-of-an-asset"></a>修剪資產開頭
在大部分的即時資料流事件中，操作人員必須在實際的事件前先進行測試。 例如，他們可以在事件開始前包含如下 slate 訊息：「程式將立刻開始」。 如果程式正在進行封存，則測試和靜態圖像資料也會一併封存並包含在簡報中。 但是此資訊不會對用戶端顯示。 透過動態資訊清單，您可以建立開始時間篩選器，並從資訊清單中移除不必要的資料。

![開始修剪][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>從即時封存中建立子剪輯 (檢視)
許多即時事件的執行時間長，且即時封存可能包括多個事件。 即時事件結束後，廣播者可能想要將即時封存分解成邏輯程式的啟動和停止序列。 接下來，個別發佈這些虛擬程式，但不後續處理即時封存，亦不建立個別的資產 (這無法運用 CDN 中現有快取片段的優點)。 例如足球或籃球比賽的球季、棒球賽局，或是任何體育節目的個別事件，都是此類虛擬程式的範例。

使用動態資訊清單，您便可以使用開始/結束時間建立篩選器，並在即時封存頂端建立虛擬檢視。 

![子剪輯篩選][subclip_filter]

已篩選的資產：

![滑動][skiing]

## <a name="adjusting-presentation-window-dvr"></a>調整簡報視窗 (DVR)
目前，Azure 媒體服務提供持續時間可設為 5 分鐘到 25 小時的循環封存。 資訊清單篩選可以在封存頂端建立循環 DVR 視窗封存，而不會刪除媒體。 在許多情況下，廣播者想要提供受限制的 DVR 時段以隨著即時邊緣移動，並同時保留更大的封存視窗。 廣播者可能會想要使用超出 DVR 視窗的資料來反白顯示剪輯，或者想要為不同的裝置提供不同的 DVR 視窗。 例如，大多數的行動裝置不處理大型 DVR 視窗 (您可以讓行動裝置有 2 分鐘長的 DVR 視窗，而桌上型用戶端的有一小時長)。

![DVR 視窗][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>調整 LiveBackoff (即時位置)
資訊清單篩選可用來移除即時程式其即時邊緣幾秒鐘的時間。 篩選可讓廣播者在檢視者收到資料流前 (倒退 30 秒) 先觀賞預覽發佈點的簡報，並建立廣告插入點。 廣播者接著可將這些廣告及時推送到其用戶端架構，讓廣播者能夠收到與處理資訊，才有機會播放廣告。

除了廣告支援外，LiveBackoff 設定可用來調整檢視者位置，讓用戶端移動並點閱即時邊緣時，仍然可以從伺服器取得片段，而非得到 404 或 412 HTTP 錯誤。

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>將多個規則結合成單一篩選器
您可以將多個篩選規則結合成單一篩選器。 例如，您可以定義一個「範圍規則」，將靜態圖像從即時封存中移除，並同時篩選出可用的位元速率。 套用多個篩選規則時，最終結果會是所有規則的交集。

![多個規則][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>結合多個篩選條件 (篩選器組合)

您也可以將多個篩選條件結合成單一 URL。 

下列案例示範您可能會想結合篩選條件的理由：

1. 您要篩選例如 Android 或 iPAD 等行動裝置的視訊品質 (以限制視訊品質)。 若要移除不必要的品質，您可以建立適用於裝置設定檔的帳戶篩選器。 帳戶篩選器可以用於相同媒體服務帳戶下所有的資產，而不需進一步的關聯。 
2. 您也想要修剪資產的開始和結束時間。 若要達到此目的，您可建立資產篩選器，並設定開始/結束時間。 
3. 您必須結合這兩個篩選器 (若不組合，您便需要將品質篩選加入修剪篩選，這會讓篩選器更難以使用)。

若要結合篩選條件，您必須設定資訊清單/播放清單 URL 的篩選條件名稱，並以分號分隔。 假設您擁有名為 *MyMobileDevice* 的篩選條件，可篩選品質，且您有另一個名為 *MyStartTime* 的篩選條件，用來設定特定的開始時間。 您可以將它們結合如下：

您可以結合最多三個篩選。 

如需詳細資訊，請參閱 [此部落格](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) 。

## <a name="considerations-and-limitations"></a>考量與限制

- 不應為 VoD 篩選器設定 **forceEndTimestamp**、**presentationWindowDuration** 和 **liveBackoffDuration** 的值。 它們僅適用於即時篩選器案例。 
- 動態資訊清單會在 GOP 界限 (主要畫面格) 中運作，因此修剪後能夠有精確的 GOP。 
- 您可以將相同的篩選器名稱用於帳戶和資產篩選器。 資產篩選器有較高的優先順序，因此會覆寫帳戶篩選器。
- 如果您更新篩選器，則資料流端點需要 2 分鐘的時間來重新整理規則。 如果內容是透過使用某些篩選器提供的 (並在 Proxy 和 CDN 快取中快取)，則更新這些篩選器會導致播放程式失敗。 建議您在更新篩選器之後清除快取。 如果這個選項無法執行，請考慮使用不同的篩選器。
- 客戶必須手動下載資訊清單，並剖析確切的 startTimestamp 和時間範圍。
    
    - 若要判斷 Asset 中資料軌的屬性，請[取得並檢查資訊清單檔案](#get-and-examine-manifest-files)。
    - 設定資產篩選器 timestamp 屬性的公式： <br/>startTimestamp = &lt;資訊清單中的開始時間&gt; +  &lt;預期的篩選器開始時間 (秒)&gt;*timescale


## <a name="next-steps"></a>後續步驟

下列文章說明如何以程式設計方式建立篩選器。  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
