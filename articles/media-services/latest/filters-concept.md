---
title: 在 Azure 媒體服務中定義篩選條件
description: 本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。 媒體服務會建立動態資訊清單來完成此選擇性資料流。
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372934"
---
# <a name="define-account-filters-and-asset-filters"></a>定義帳戶篩選器和資產篩選器  

當提供您的內容給客戶 (串流處理即時事件或點播視訊) 時，您的用戶端可能需要比預設資產資訊清單檔案中所述還大的彈性。 Azure 媒體服務可讓您為內容定義帳戶篩選器和資產篩選器。 

篩選器是伺服器端規則，可讓您的客戶執行下列動作： 

- 僅播放視訊的某個區段 (而非播放整個視訊)。 例如︰
  - 縮小資訊清單以顯示即時事件的子剪輯 (「子剪輯篩選」)，或者
  - 修剪視訊開頭 (「修剪視訊」)。
- 只傳遞用來播放內容的裝置所支援的指定轉譯和/或指定的語言資料軌 (轉譯篩選)。 
- 調整簡報視窗 (DVR)，以在播放程式中提供長度有限的 DVR 視窗 (「調整簡報視窗」)。

媒體服務根據預先定義的篩選器提供[動態資訊清單](filters-dynamic-manifest-overview.md)。 定義篩選條件後，您的用戶端就可以在串流 URL 中使用它們。 篩選器可以套用至自適性串流通訊協定：Apple HTTP 即時串流 (HLS)、MPEG-DASH 和 Smooth Streaming。

下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>定義篩選條件

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

## <a name="example"></a>範例

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

## <a name="next-steps"></a>後續步驟

下列文章說明如何以程式設計方式建立篩選器。  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

