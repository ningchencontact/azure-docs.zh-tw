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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66225418"
---
# <a name="filters"></a>篩選器

當您將內容傳遞給客戶 （即時資料流的事件或隨選視訊） 您的用戶端可能需要更多的彈性比預設資產資訊清單檔中所述。 Azure 媒體服務提供[動態資訊清單](filters-dynamic-manifest-overview.md)根據預先定義的篩選條件。 

篩選器是伺服器端規則，可讓您的客戶執行下列動作： 

- 僅播放視訊的某個區段 (而非播放整個視訊)。 例如:
  - 縮小資訊清單以顯示即時事件的子剪輯 (「子剪輯篩選」)，或者
  - 修剪視訊開頭 (「修剪視訊」)。
- 只傳遞用來播放內容的裝置所支援的指定轉譯和/或指定的語言資料軌 (轉譯篩選)。 
- 調整簡報視窗 (DVR)，以在播放程式中提供長度有限的 DVR 視窗 (「調整簡報視窗」)。

媒體服務可讓您建立**帳戶篩選器**並**資產篩選器**對您的內容。 此外，您可以將您預先建立的篩選條件**串流定位器**。

## <a name="defining-filters"></a>定義篩選器

有兩種篩選： 

* [帳戶篩選器](https://docs.microsoft.com/rest/api/media/accountfilters) (全域) - 可以套用到 Azure 媒體服務帳戶中的任何資產，存留期和帳戶的相同。
* [資產篩選器](https://docs.microsoft.com/rest/api/media/assetfilters) (本機) - 只能套用篩選器建立時與其相關聯的資產，存留期和資產的相同。 

**帳戶篩選**並**資產篩選器**類型有完全相同的屬性來定義/描述篩選。 除了在建立**資產篩選器**的時候，因為您需要指定要與篩選器相關聯之資產的名稱。

視您的案例而定，您會決定哪個類型的篩選器較合適 (資產篩選器或帳戶篩選器)。 帳戶篩選器適合裝置設定檔 (轉譯篩選)，資產篩選器可用於修剪特定資產。

您可以使用下列屬性來描述篩選器。 

|Name|描述|
|---|---|
|firstQuality|篩選器的首次品質位元速率。|
|presentationTimeRange|簡報時間範圍。 此屬性用於篩選資訊清單起始/結束點、簡報視窗長度，以及即時起始位置。 <br/>如需詳細資訊，請參閱 [PresentationTimeRange](#presentationtimerange)。|
|tracks|資料軌選取條件。 如需詳細資訊，請參閱[資料軌](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

將此屬性與**資產篩選器**搭配使用。 不建議搭配**帳戶篩選器**設定此屬性。

|Name|描述|
|---|---|
|**endTimestamp**|適用於點播視訊 (VoD)。<br/>Live Streaming 展示檔，它會以無訊息方式忽略並且套用簡報結束，然後再將資料流時 VoD。<br/>這是簡報的長數值，表示絕對結束點，四捨五入為最接近的下一個 GOP 入門。 單位為的時幅，因此 endTimestamp 1800000000 的會是 3 分鐘。<br/>使用 startTimestamp 和 endTimestamp 修剪會播放清單 （資訊清單） 中的片段。<br/>比方說，startTimestamp = 40000000 和 endTimestamp = 100000000 使用預設的時幅，將會產生包含片段介於 4 秒和 VoD 簡報的 10 秒的播放清單。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**forceEndTimestamp**|適用於僅即時的資料流。<br/>指出是否必須存在 endTimestamp 屬性。 如果為 true，必須指定 endTimestamp 或傳回不正確的要求程式碼。<br/>允許的值：false、true。|
|**liveBackoffDuration**|適用於僅即時的資料流。<br/> 此值會定義用戶端可以尋找最新即時位置。<br/>您可以使用這個屬性，來延遲即時播放位置，為玩家建立伺服器端緩衝區項目。<br/>這個屬性的單位是時幅 （如下所示）。<br/>即時輪詢持續時間的上限為 300 秒 (3000000000)。<br/>例如，值為最新可用的內容是 20 秒的 2000000000 表示延遲其真正即時邊緣。|
|**presentationWindowDuration**|適用於僅即時的資料流。<br/>您可以使用 presentationWindowDuration 套用滑動視窗的播放清單中包含的片段。<br/>這個屬性的單位是時幅 （如下所示）。<br/>例如，設定 presentationWindowDuration=1200000000 以套用兩分鐘長的滑動視窗。 在即時邊緣 2 分鐘內的媒體都會包含在播放清單中。 如果片段跨越界限，則整個片段都會包含在播放清單中。 簡報視窗持續時間的最小值是 60 秒。|
|**startTimestamp**|適用於影片 (Vod) 或即時資料流。<br/>這是長數值，表示資料流的絕對的起始點。 系統會將此值四捨五入到最接近的下一個 GOP 起始。 因此 startTimestamp 150000000 的會是 15 秒時幅，已包含單位。<br/>使用 startTimestamp 和 endTimestampp 修剪會播放清單 （資訊清單） 中的片段。<br/>比方說，startTimestamp = 40000000 和 endTimestamp = 100000000 使用預設的時幅，將會產生包含片段介於 4 秒和 VoD 簡報的 10 秒的播放清單。 如果片段跨越界限，則整個片段都會包含在資訊清單中。|
|**timescale**|在簡報時間範圍內，指定的增量數目為一秒內，以套用到所有的時間戳記和持續時間。<br/>預設值為 10000000-一千萬遞增一秒，其中每個增量會是 100 奈秒內。<br/>比方說，如果您想要在 30 秒設定 startTimestamp，您會使用 300000000 值時使用預設的時幅。|

### <a name="tracks"></a>資料軌

您指定的篩選器追蹤屬性條件 (FilterTrackPropertyConditions) 清單型的串流 （即時資料流或隨選視訊） 的追蹤記錄應納入動態建立資訊清單。 篩選器是使用邏輯 **AND** 和 **OR** 運算。

篩選器資料軌屬性條件描述資料軌類型、值 (下表中所述) 和運算 (Equal、NotEqual)。 

|Name|描述|
|---|---|
|**Bitrate**|使用資料軌的位元速率來篩選。<br/><br/>建議的值是位元速率範圍 (以每秒位元數為單位)。 例如，"0-2427000"。<br/><br/>注意：雖然您可以使用特定的位元速率值，如 250000 (每秒位元數)，但不建議使用此方法，因為資產之間的確切位元速率可能會變動。|
|**FourCC**|將資料軌的 FourCC 值用於篩選。<br/><br/>該值是轉碼器格式的第一個元素，如 [RFC 6381](https://tools.ietf.org/html/rfc6381) \(英文\) 中所指定。 目前支援下列轉碼器： <br/>視訊："avc1"、"hev1"、"hvc1"<br/>音訊："mp4a"、"ec-3"<br/><br/>若要判斷在資產中的資料軌的 FourCC 值，取得，並檢查資訊清單檔案。|
|**語言**|使用資料軌的語言來篩選。<br/><br/>此值是您要包含之語言的標籤，如 RFC 5646 中所指定。 例如，"en"。|
|**名稱**|使用資料軌的名稱來篩選。|
|**類型**|使用資料軌的類型來篩選。<br/><br/>允許下列值："video"、"audio" 或 "text"。|

### <a name="example"></a>範例

下列範例會定義即時資料流的篩選器： 

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

## <a name="associating-filters-with-streaming-locator"></a>篩選關聯串流定位器

您可以指定一份[資產或帳戶篩選器](filters-concept.md)在您[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)。 [動態封裝程式](dynamic-packaging-overview.md)適用於這份清單，以及那些用戶端在 URL 中所指定的篩選條件。 這個組合會產生[動態資訊清單](filters-dynamic-manifest-overview.md)，根據在 URL 中的篩選器 + 串流定位器您指定的篩選器。 

請參閱下列範例：

* [篩選關聯串流定位器-.NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [篩選關聯串流定位器-CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>更新篩選器
 
**串流定位器**可以更新篩選器時，為不可更新。 

不建議更新主動發佈相關聯的篩選條件的定義**串流定位器**，特別是在啟用 CDN。 串流處理伺服器和 Cdn 可以有可能會導致傳回過時的快取資料的內部快取。 

如果必須變更篩選定義，請考慮建立新的篩選器，並將它加入至**串流定位器**URL 或發行新**串流定位器**直接參考篩選條件。

## <a name="next-steps"></a>後續步驟

下列文章說明如何以程式設計方式建立篩選器。  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

