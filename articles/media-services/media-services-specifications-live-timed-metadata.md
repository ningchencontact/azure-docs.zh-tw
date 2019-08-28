---
title: Azure 媒體服務 - 針對即時串流中的計時中繼資料傳送訊號 | Microsoft Docs
description: 此規格概述內嵌和串流至 Azure 媒體服務時, 用來發出計時中繼資料信號的方法。 這包括對一般計時中繼資料信號 (ID3) 的支援, 以及用於廣告插入和接合條件信號的 SCTE-35 信號。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: df2a86dd1292f58511765e842ee97daddcff4e3e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102927"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>針對即時串流中的計時中繼資料傳送訊號 

上次更新日期：2019-08-22

### <a name="conformance-notation"></a>一致性標記法

此文件中「必須」、「不得」、「必要」、「應」、「不應」、「應該」、「不應該」、「建議」、「可能」與「選擇性」需依 RFC 2119 所述來解釋

## <a name="1-introduction"></a>1.簡介 

為了通知在用戶端播放機上插入廣告或自訂中繼資料事件, 廣播者通常會使用在影片內內嵌的計時中繼資料。 若要啟用這些案例, 媒體服務提供從即時串流通道的內嵌點傳輸計時中繼資料至用戶端應用程式的支援。
此規格概述即時串流信號中計時中繼資料媒體服務所支援的數種模式。

1. [SCTE-35] 符合 [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] 和 [RFC8216] 所述標準的信號

2. [SCTE-35] 符合 RTMP 廣告信號的舊版 [Adobe-Primetime] 規格的信號。
   
3. 一般計時中繼資料信號模式, 適用于**不**是 [SCTE-35] 的訊息, 而且可能會包含應用程式開發人員所定義的 [ID3v2] 或其他自訂架構。

## <a name="11-terms-used"></a>1.1 使用的詞彙

| 詞彙              | 定義 |
|-------------------|------------|
| 廣告中斷          | 一或多個廣告可能排程傳遞的位置或時間點。與「可用」和「放置」商機相同。 |
| Ad 決策服務| 決定將對使用者顯示哪些 ad 和持續時間的外部服務。 服務通常是由合作夥伴提供, 並超出本檔的範圍。|
| 提示               | 未來廣告中斷的時間和參數的指示。 請注意, 提示可能表示擱置切換至廣告中斷、擱置切換到廣告中斷內的下一個 ad, 以及從廣告中斷切換至主要內容。 |
| R          | Azure 媒體服務「串流端點」提供了破折號和 HLS 的動態封裝功能, 在媒體產業中稱為「包裝程式」。 
| 呈現時間 | 事件呈現給檢視者的時間。 此時間代表使用者會看到事件的媒體時間軸位置。 例如，SCTE-35 splice_info() 命令訊息的呈現時間是 splice_time()。 |
| 抵達時間      | 事件訊息抵達的時間。 此時間通常與事件的呈現時間截然不同，因為事件訊息是在事件的呈現時間之前傳送。                                     |
| 疏鬆資料軌      | 不連續且時間已與父代或控制播放軌同步的媒體播放軌。                                                                                                                                    |
| 原始            | Azure 媒體串流服務                                                                                                                                                                                                |
| 通道接收      | Azure 媒體即時串流服務                                                                                                                                                                                           |
| HLS               | Apple HTTP 即時串流通訊協定                                                                                                                                                                                               |
| DASH              | HTTP 上的動態自適性串流                                                                                                                                                                                             |
| Smooth            | Smooth Streaming 通訊協定                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG 2 傳輸串流                                                                                                                                                                                                         |
| RTMP              | 即時多媒體通訊協定                                                                                                                                                                                                    |
| uimsbf            | 不帶正負號的整數，最顯著位元在前。                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1.2 規範參考

下列檔包含布建, 這份文字中的參考會構成本檔的條款。 所有檔都受限於標準本文的修訂, 而讀者則建議您調查下列可能套用最新版本檔的可能性。 讀者也會提醒, 較新版本的參考檔可能無法與此 Azure 媒體服務的計時中繼資料規格版本相容。


|標準  |定義  |
|---------|---------|
|[Adobe-Primetime] | [Primetime 數位程式插入信號規格1。2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [FLASH ActionScript 語言參考](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| AMF0            | [「動作訊息格式 AMF0」](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [破折號-IF-IOP]     | 虛線產業論壇 Interop 指導方針 v 4。2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | HTTP 即時串流的計時中繼資料-[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [CMAF-ID3]         | [一般媒體應用程式格式的計時中繼資料 (CMAF)](https://aomediacodec.github.io/av1-id3/)
| ID3v2           | ID3 標記版本2.4。0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12:第12部分 ISO 基底媒體檔案格式, FourthEdition 2012-07-15  |
| [MPEGDASH]        | 資訊技術--透過 HTTP 的動態彈性串流 (破折號)--第1部分:媒體簡報描述和區段格式。 5月2014。 發佈. URL： https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | 資訊技術--多媒體應用程式格式 (MPEG)--第19部分:分段媒體的通用媒體應用程式格式 (CMAF)。 2018年1月。 發佈. URL： https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 資訊技術--MPEG 系統技術--第7部分:ISO base media 檔案格式檔案中的一般加密。 2016年2月。 發佈. URL： https://www.iso.org/standard/68042.html |
| [MS-MS-SSTR]         | [「Microsoft Smooth Streaming Protocol」, 2014 的5月15日](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-內嵌]  | [Azure 媒體服務的分散 MP4 即時內嵌規格](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | Jenkins Pantos, Ed.;W. 可能。 HTTP 即時串流。 2017年8月。 供. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| RFC4648         |Base16、Base32 和 Base64 資料編碼-[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |[「Adobe 的即時訊息通訊協定」, 2012 年12月21日](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35:2019-適用于纜線的數位程式插入提示訊息- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 –以 IP 為基礎的纜線服務的 MPEG 破折號第1部分:MPD 條件約束和延伸模組 |
| [SCTE-214-3]      | 以 IP 為基礎的纜線服務的 SCTE 214-3 2015 MPEG 破折號第3部分:虛線/FF 設定檔 |
| [SCTE-224]        | SCTE 224 2018r1 –事件排程和通知介面 |
| [SCTE-250]        | 事件和信號管理 API (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2.計時中繼資料內嵌

Azure 媒體服務針對 [RTMP] 和 Smooth Streaming [MS-MS-SSTR] 通訊協定支援即時的頻內中繼資料。 即時中繼資料可用來定義自訂事件, 包括您自己獨特的自訂架構 (JSON、二進位、XML), 以及產業定義的格式, 例如 ID3, 或 SCTE-35 用於廣播串流中的廣告信號。 

本文提供有關如何使用 Azure 媒體服務支援的內嵌通訊協定來傳送自訂計時中繼資料信號的詳細資訊。 本文也會說明如何使用計時中繼資料信號來裝飾 HLS、虛線和 Smooth Streaming 的資訊清單, 以及如何在傳遞內容時, 以 CMAF (片段) 或傳輸資料流程 (TS) 區段進行 HLS。 

計時中繼資料的常見使用案例包括:

 - SCTE-35 廣告信號, 以在實況活動或線性廣播中觸發廣告中斷
 - 可在用戶端應用程式 (瀏覽器、iOS 或 Android) 觸發事件的自訂 ID3 中繼資料
 - 自訂定義的 JSON、二進位或 XML 中繼資料, 以在用戶端應用程式觸發事件
 - 來自即時編碼器、IP 攝影機或無人機的遙測
 - 來自 IP 攝影機的事件, 例如動作、臉部偵測等等。
 - 來自行動相機、無人機或移動裝置的地理位置資訊
 - 歌曲歌詞
 - 線性即時摘要上的程式界限
 - 要顯示在即時摘要上的影像或增強的中繼資料
 - 運動分數或遊戲時鐘資訊
 - 要在瀏覽器中與影片一起顯示的互動式廣告套件
 - 測驗或投票
  
Azure 媒體服務即時事件和封裝程式能夠接收這些計時中繼資料信號, 並將它們轉換成中繼資料的資料流程, 以使用 HLS 和破折號等標準通訊協定來連接用戶端應用程式。


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 計時中繼資料

[RTMP] 通訊協定允許針對各種案例 (包括自訂中繼資料和 SCTE-35 廣告信號) 傳送計時中繼資料信號。 

廣告信號 (提示訊息) 會以內嵌于 [RTMP] 串流內的 [AMF0] 提示訊息形式傳送。 提示訊息可能會在實際事件或 [SCTE35] 廣告拼接信號必須發生之前傳送。 為了支援此案例, 會在提示訊息內傳送事件的實際呈現時間戳記。 如需詳細資訊，請參閱 [AMF0]。

RTMP 內嵌的 Azure 媒體服務支援下列 [AMF0] 命令:

- **onUserDataEvent** -用於自訂中繼資料或 [ID3v2] 計時中繼資料
- **onAdCue** -主要用於向即時串流中的廣告放置機會發出信號。 支援兩種形式的提示, 分別是簡單模式和 "SCTE-35" 模式。 
- **onCuePoint** -支援特定的內部部署硬體編碼器 (例如 Elemental 即時編碼器) 來發出信號 [SCTE35] 訊息。 
  

下表描述媒體服務將同時內嵌 "simple" 和 [SCTE35] 訊息模式的 AMF 訊息裝載格式。

您可以使用 [AMF0] 訊息的名稱來區分相同類型的多個事件資料流程。  對於 [SCTE-35] 訊息和「簡單」模式, AMF 訊息的名稱必須是 [Adobe-Primetime] 規格中所需的 "onAdCue"。  在內嵌時, Azure 媒體服務應該忽略下面未列出的任何欄位。

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>透過使用 "onUserDataEvent" 的自訂中繼資料來 2.1.1 RTMP

如果您想要使用 RTMP 通訊協定, 從上游編碼器、IP 攝影機、無人機或裝置提供自訂的中繼資料摘要, 請使用 "onUserDataEvent" [AMF0] data message 命令類型。

**"OnUserDataEvent"** 資料訊息命令必須攜帶具有下列定義的訊息承載, 以供媒體服務並封裝為頻內檔案格式, 以及 HLS、虛線和 Smooth Streaming 的資訊清單。
建議您在每0.5 秒 (500 毫秒) 或即時串流發生穩定性問題時, 不常經常傳送計時中繼資料訊息。 如果您需要提供框架層級的中繼資料, 每個訊息都可以匯總來自多個框架的中繼資料。 如果您要傳送多位元率串流, 建議您也只提供單一位元速率的中繼資料, 以減少頻寬並避免與影片/音訊處理產生干擾。 

**"OnUserDataEvent"** 的承載應為 [MPEGDASH] EventStream XML 格式訊息。 這可讓您輕鬆地傳入自訂定義的架構, 以在 CMAF [MPEGCMAF] 透過 HLS 或破折號通訊協定傳遞的「emsg」承載中攜帶。 每個虛線事件資料流程訊息都包含一個 schemeIdUri, 可做為 URN 訊息配置識別碼, 並定義訊息的裝載。 適用于 [scte- https://aomedia.org/emsg/ID3 35] 的某些配置 (例如 [ID3v2] 或**urn: scte: scte35: 2013: bin** ) 是由業界聯盟標準化, 以達成互通性。 任何應用程式提供者都可以使用其控制的 URL (擁有的網域) 來定義自己的自訂配置, 並可在選擇時提供該 URL 的規格。 如果播放程式有定義之配置的處理常式, 則這是唯一需要瞭解裝載和通訊協定的元件。

[MPEG 破折號] EventStream XML 承載的架構定義為 (摘錄自破折號-IEC-23009-1-第3版)。 請注意, 目前只支援每個 "EventStream" 一次「事件種類」。 只有在**EventStream**中提供多個事件時, 才會處理第一個**事件**元素。

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>具有 ID3 架構識別碼和 base64 編碼資料裝載的 XML 事件資料流程範例。  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>含有自訂架構識別碼和 base64 編碼二進位資料的範例事件資料流程  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>具有自訂架構識別碼和自訂 JSON 的範例事件資料流程  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>內建支援的配置識別碼 Uri
| 配置識別碼 URI                 |  描述                                             |
|-------------------------------|----------------------------------------------------------|
| HTTPs:\//aomedia.org/emsg/ID3   | 描述如何將 [ID3v2] 中繼資料當做 CMAF 相容 [MPEGCMAF] 中的計時中繼資料來攜帶。 如需詳細資訊, 請參閱[一般媒體應用程式格式的計時中繼資料 (CMAF)](https://aomediacodec.github.io/av1-id3/) |

### <a name="event-processing-and-manifest-signaling"></a>事件處理和資訊清單信號

收到有效的 **"onUserDataEvent"** 事件時, Azure 媒體服務會尋找符合 EventStreamType (定義于 [MPEGDASH]) 的有效 XML 承載、剖析 XML 裝載, 並將它轉換成 [MPEGCMAF]儲存在即時封存中, 並傳輸到媒體服務封裝程式。   封裝工具會偵測即時資料流中的 [emsg] 方塊, 並:

- (a) 「將它動態封裝」到 TS 區段, 以傳遞至 HLS 用戶端, 符合 HLS 計時中繼資料規格 [HLS-TMD], 或
- (b) 傳遞給它, 以透過 HLS 或破折號傳遞 CMAF 片段, 或 
- (c) 將它轉換成可透過 Smooth Streaming [MS-SSTR] 傳遞的稀疏軌信號。

除了適用于 HLS 的頻外 ' emsg ' 格式 CMAF 或 TS PE 封包之外, 破折號 (MPD) 和 Smooth Streaming 的資訊清單會包含內建事件資料流程的參考 (在 Smooth Streaming 中也稱為「稀疏資料流程追蹤」)。 

個別事件或其資料裝載不會直接在 HLS、虛線或流暢的資訊清單中輸出。 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>OnUserDataEvent 事件的其他資訊條件約束和預設值

- 如果未在 EventStream 元素中設定此時間刻度, 則預設會使用 RTMP 1 kHz 的時間刻度
- OnUserDataEvent 訊息的傳遞限制為每個500毫秒的最大值一次。如果您更頻繁地傳送事件, 可能會影響即時摘要的頻寬和穩定性

## <a name="212-rtmp-ad-cue-signaling-with-oncuepoint"></a>具有 "onCuePoint" 的 2.1.2 RTMP ad 提示信號

Azure 媒體服務可以接聽並回應數個 [AMF0] 訊息類型, 可用來對即時資料流中的各種即時同步處理中繼資料發出信號。  [Adobe Primetime] 規格會定義兩個稱為 "simple" 和 "SCTE-35" 模式的提示類型。 針對「簡單」模式, 媒體服務支援名為 "onAdCue" 的單一 AMF 提示訊息, 其使用符合下表所定義之「簡單模式」信號的裝載。  

下一節將說明 RTMP 「簡單」模式的承載, 其可用來表示將會傳遞至 HLS、破折號和 Microsoft Smooth Streaming 的用戶端資訊清單的基本 "spliceOut" 廣告信號。 當客戶沒有以 SCTE 35 為基礎的複雜廣告信號部署或插入系統, 而且使用基本內部部署編碼器透過 API 傳送提示訊息時, 這非常有用。 一般來說, 內部部署編碼器會支援 REST 型 API 來觸發此信號, 這也會在影片中插入 IDR 框架並啟動新的 GOP, 藉此「接合條件」影片串流。

## <a name="213--rtmp-ad-cue-signaling-with-oncuepoint---simple-mode"></a>使用 "onCuePoint" 的 2.1.3 RTMP ad 提示信號-簡單模式

| 欄位名稱 | 欄位類型 | 必要項？ | 描述                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必要項 | 事件訊息。  應該是 "SpliceOut" 以指定簡單模式拼接。                                              |
| ID         | String     | 必要項 | 描述拼接或區段的唯一識別碼。 識別訊息的此執行個體                            |
| 持續時間   | Number     | 必要項 | 拼接的持續時間。 單位是小數秒。                                                                |
| elapsed    | Number     | 選擇性 | 當訊號重複以支援收聽時，此欄位應該是開始拼接後經過的呈現時間量。 單位是小數秒。 當使用簡單模式時，此值不應該超過拼接的原始時間長度。                                                  |
| time       | Number     | 必要項 | 應該是拼接的時間 (以呈現時間表示)。 單位是小數秒。                                     |

---
 
## <a name="214-rtmp-ad-cue-signaling-with-oncuepoint---scte-35-mode"></a>具有 "onCuePoint"-SCTE-35 模式的 2.1.4 RTMP ad 提示信號

當您使用的「更先進的廣播生產」工作流程需要將完整的 SCTE-35 承載訊息傳遞至 HLS 或破折號資訊清單時, 最好使用 [Adobe-Primetime] 規格的「SCTE-35 模式」。  此模式支援直接傳送到內部部署即時編碼器的頻內 SCTE-35 信號, 然後使用 [Adobe-Primetime] 規格中指定的「SCTE-35 模式」, 將信號編碼成 RTMP 串流。 

通常 SCTE-35 訊息只能出現在內部部署編碼器的 MPEG-2 傳輸串流 (TS) 輸入中。 請洽詢您的編碼器製造商, 以取得如何設定包含 SCTE-35 之傳輸資料流程內嵌的詳細資料, 並讓它能夠在 Adobe SCTE-35 模式下傳遞至 RTMP。

在此案例中, 必須使用 **"onAdCue"** [AMF0] 訊息類型, 從內部部署編碼器傳送下列承載。

| 欄位名稱 | 欄位類型 | 必要項？ | 描述                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必要項 | 事件訊息。  對於 [SCTE-35] 訊息, 這必須是 base64 編碼的 [RFC4648] 二進位 splice_info_section (), 才能將訊息傳送至 HLS、平滑和虛線用戶端。                                              |
| Type       | String     | 必要項 | 可識別訊息配置的 URN 或 URL。 對於 [SCTE-35] 訊息, 這**應該**是 **"scte35"** , 以便將訊息傳送至 HLS、平滑和虛線用戶端, 以符合 [Adobe-Primetime]。 (選擇性) URN "urn: scte: scte35: 2013: bin" 也可以用來表示 [SCTE-35] 訊息。 |
| ID         | String     | 必要項 | 描述拼接或區段的唯一識別碼。 識別訊息的此執行個體。  具有同等語意的訊息應該有相同的值。|
| 持續時間   | Number     | 必要項 | 事件或廣告拼接區段的期間 (若已知)。 如果未知, 此值**應該**是0。                                                                 |
| elapsed    | Number     | 選擇性 | 當 [SCTE-35] 廣告訊號重複以收聽時，此欄位應該是開始拼接後經過的呈現時間量。 單位是小數秒。 在 [SCTE-35] 模式中，此值可能會超過原本指定的拼接或區段持續時間。                                                  |
| time       | Number     | 必要項 | 事件或拼接的呈現時間。  簡報時間和持續時間**應該**與類型1或2的串流存取點 (SAP) 一致, 如 [ISO-14496-12] 附錄 I 中所定義。針對 HLS 輸出, 時間和持續時間**應該**與區段界限一致。 呈現時間與相同事件串流中不同事件訊息的期間不能重疊。 單位是小數秒。

---
## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>針對 Elemental Live 以 "onCuePoint" 2.1.5 RTMP 廣告信號

Elemental Live 內部部署編碼器支援 RTMP 信號中的廣告標記。 Azure 媒體服務目前僅支援 RTMP 的 "onCuePoint" Ad 標記類型。  您可以在 Elemental Media Live 編碼器設定或 API 的 Adobe RTMP 群組設定中啟用此功能, 方法是將 "**ad_markers**" 設定為 "onCuePoint"。  如需詳細資訊, 請參閱 Elemental Live 檔。 在 RTMP 群組中啟用這項功能, 會將 SCTE-35 信號傳遞給要由 Azure 媒體服務處理的 Adobe RTMP 輸出。

"OnCuePoint" 訊息類型定義于 [Adobe-Flash-AS] 中, 並在從 Elemental 即時 RTMP 輸出傳送時具有下列裝載結構。


| 屬性  |描述  |
|---------|---------|
|  name     | Elemental Live 的名稱應該是 '**scte35**'。 |
|time     |  在時間軸期間, 影片檔案中出現提示點的時間 (以秒為單位) |
| Type     | 提示點的類型應該設定為「**事件**」。 |
| 參數 | 名稱/值組字串的關聯陣列, 其中包含來自 SCTE-35 訊息的資訊, 包括識別碼和持續時間。 這些值會由 Azure 媒體服務剖析, 並包含在資訊清單裝飾標記中。  |


使用此 ad 標記模式時, HLS 資訊清單輸出類似 Adobe "Simple" 模式。 

### <a name="216-cancellation-and-updates"></a>2.1.6 取消和更新

您可以透過傳送具有相同呈現時間與 ID 的多個訊息來取消或更新訊息。 呈現時間與 ID 可唯一識別事件，而且針對符合預滾動限制式之特定呈現時間所接收的最後一個訊息是針對其採取動作的訊息。 更新的事件會取代先前接收的任何訊息。 預滾動限制式是四秒。 系統將會針對在呈現時間前最後四秒接收的訊息採取動作。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 分散的 MP4 內嵌 (Smooth Streaming)

如需即時資料流內嵌的需求, 請參閱 [MS-MS-SSTR-內嵌]。 以下各節提供有關計時呈現中繼資料內嵌的詳細資料。  計時呈現中繼資料會內嵌為疏鬆播放軌，疏鬆播放軌同時在即時伺服器中繼資料方塊 (請參閱 MS-SSTR) 與影片方塊 (‘moov’) 中定義。  

每個疏鬆片段都是由影片片段方塊 (‘moof’) 與媒體資料方塊 (‘mdat’) 所組成，其中 ‘mdat’ 方塊是二進位訊息。

為了達到框架正確插入廣告的效果, 編碼器必須在需要插入提示的呈現時間分割片段。  必須建立新的片段, 其開頭為新建立的 IDR 框架, 或類型1或2的串流存取點 (SAP), 如 [ISO-14496-12] 附錄 I 中所定義。這可讓 Azure 媒體封裝程式正確產生 HLS 資訊清單和虛線多段式資訊清單, 其中新期間是以框架精確的接合條件式呈現時間開始。

### <a name="221-live-server-manifest-box"></a>2.2.1 即時伺服器資訊清單方塊

稀疏播放軌**必須**在具有 **\<textstream\>** 專案的即時伺服器資訊清單方塊中宣告, 而且**必須**設定下列屬性:

| **屬性名稱** | **欄位類型** | **必要？** | **描述**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | 必要項      | **必須**為 "0", 表示具有未知、變數位元速率的追蹤。                                                                                                                                                                                                 |
| parentTrackName    | String         | 必要項      | **必須**是父代播放軌的名稱, 其中的稀疏追蹤時間代碼為對齊的時間。 父系播放軌不能是疏鬆播放軌。                                                                                                                    |
| manifestOutput     | Boolean        | 必要項      | **必須**是 "true", 表示稀疏播放軌將內嵌在平滑用戶端資訊清單中。                                                                                                                                                               |
| Subtype            | String         | 必要項      | **必須**是四個字元代碼 "DATA"。                                                                                                                                                                                                                        |
| 配置             | String         | 必要項      | **必須**是可識別訊息配置的 URN 或 URL。 對於 [SCTE-35] 訊息, 這**必須**是 "urn: SCTE: scte35: 2013: bin", 才能將訊息傳送至 HLS、平滑和虛線用戶端, 以符合 [SCTE-35]。 |
| trackName          | String         | 必要項      | **必須**是稀疏播放軌的名稱。trackName 可用來區分具有相同配置的不同事件串流。 每個唯一的事件資料流程都**必須**有唯一的追蹤名稱。                                                                           |
| timescale          | Number         | 選擇性      | **必須**是父軌的時間刻度。                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 影片方塊

影片方塊 (‘moov’) 依循即時伺服器資訊清單方塊作為疏鬆播放軌之串流標題的一部分。

' Moov ' 方塊**應該**包含**TrackHeaderBox (' tkhd ')** 方塊, 如 [ISO-14496-12] 中所定義, 並具有下列條件約束:

| **欄位名稱** | **欄位類型**          | **必要？** | **描述**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| 持續時間       | 64 位元不帶正負號的整數 | 必要項      | **應**為 0, 因為追蹤方塊的樣本數為零, 且追蹤方塊中樣本的總持續時間為0。 |

---

' Moov ' 方塊**應該**包含**HandlerBox (' hdlr ')** , 如 [ISO-14496-12] 中所定義, 並具有下列條件約束:

| **欄位名稱** | **欄位類型**          | **必要？** | **描述**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 位元不帶正負號的整數 | 必要項      | **應該**是 ' meta '。 |

---

' Stsd ' 方塊**應該**包含具有編碼名稱的 MetaDataSampleEntry 方塊, 如 [ISO-14496-12] 中所定義。  例如, 對於 SCTE-35 訊息, 編碼名稱**應該**是 ' SCTE '。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 影片片段方塊與媒體資料方塊

疏鬆播放軌片段由影片片段方塊 (‘moof’) 與媒體資料方塊 (‘mdat’) 所組成。

> [!NOTE]
> 為了達到框架正確插入廣告的效果, 編碼器必須在需要插入提示的呈現時間分割片段。  必須建立新的片段, 其開頭為新建立的 IDR 框架, 或類型1或2的串流存取點 (SAP), 如 [ISO-14496-12] 附錄 I 中所定義
> 

MovieFragmentBox (' moof ') 方塊**必須**包含**TrackFragmentExtendedHeaderBox (' uuid ')** 方塊, 如 [MS-ms-sstr] 中所定義, 並具有下欄欄位:

| **欄位名稱**         | **欄位類型**          | **必要？** | **描述**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 位元不帶正負號的整數 | 必要項      | **必須**是事件的抵達時間。 此值與父代播放軌的訊息一致。   |
| fragment_duration      | 64 位元不帶正負號的整數 | 必要項      | **必須**是事件的持續時間。 持續時間可以是零，指出持續時間未知。 |

---


MediaDataBox (' mdat ') 方塊的格式**必須**如下:

| **欄位名稱**          | **欄位類型**                   | **必要？** | **描述**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 版本                 | 32 位元不帶正負號的整數 (uimsbf) | 必要項      | 決定 ‘mdat’ 方塊內容的格式。 將會忽略無法辨識的版本。 目前唯一支援的版本是 1。                                                                                                                                                                                                                                                                                                                                                      |
| ID                      | 32 位元不帶正負號的整數 (uimsbf) | 必要項      | 識別訊息的此執行個體。 具有對等語意的訊息應該有相同的值；亦即，處理具有相同 id 的任一事件訊息就已足夠。                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 位元不帶正負號的整數 (uimsbf) | 必要項      | Fragment_absolute_time 的總和 (在 TrackFragmentExtendedHeaderBox 中指定) 和 presentation_time_delta**必須**是事件的呈現時間。 簡報時間和持續時間**應該**與類型1或2的串流存取點 (SAP) 一致, 如 [ISO-14496-12] 附錄 I 中所定義。針對 HLS 輸出, 時間和持續時間**應該**與區段界限一致。 相同事件資料流程內不同事件訊息的呈現時間和持續時間**不得**重迭。 |
| message                 | 位元組陣列                       | 必要項      | 事件訊息。 對於 [SCTE-35] 訊息, 訊息是二進位 splice_info_section ()。 對於 [SCTE-35] 訊息, 這**必須**是 splice_info_section (), 才能將訊息傳送至 HLS、平滑和虛線用戶端, 以符合 [SCTE-35]。 對於 [SCTE-35] 訊息, 二元 splice_info_section () 是 ' mdat ' 方塊的裝載, 而且**不**是以 base64 編碼。                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 取消與更新

您可以透過傳送具有相同呈現時間與 ID 的多個訊息來取消或更新訊息。  呈現時間與 ID uniquely 可唯一識別事件。 針對符合預滾動限制式之特定呈現時間所接收的最後一個訊息是針對其採取動作的訊息。 更新的訊息會取代先前接收的任何訊息。  預滾動限制式是四秒。 系統將會針對在呈現時間前最後四秒接收的訊息採取動作。 


## <a name="3-timed-metadata-delivery"></a>3 計時中繼資料傳遞

事件串流資料對「媒體服務」而言是不透明的。 「媒體服務」只會在內嵌端點與用戶端端點之間傳送三小部分的資訊。 下列屬性會傳遞至用戶端, 符合 [SCTE-35] 和/或用戶端的串流通訊協定:

1.  配置 – 可識別訊息配置的 URN 或 URL。
2.  呈現時間 – 媒體時間軸上之事件的呈現時間。
3.  持續時間 – 事件的持續時間。
4.  ID – 選擇性的事件唯一識別碼。
5.  訊息 – 事件資料。

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming 資訊清單  

如需如何格式化稀疏訊息追蹤的詳細資訊, 請參閱 sparse 追蹤處理 [MS-SSTR]。對於 [SCTE35] 訊息, Smooth Streaming 會將 base64 編碼的 splice_info_section () 輸出到稀疏片段中。
StreamIndex**必須**具有 "DATA" 的子類型, 而且 CustomAttributes**必須**包含 Name = "Schema" 和 Value = "urn: scte: scte35: 2013: bin" 的屬性。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>顯示 base64 編碼 [SCTE35] splice_info_section () 的平滑用戶端資訊清單範例
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS 資訊清單裝飾

Azure 媒體服務支援下列 HLS 資訊清單標記, 以在即時或隨選事件期間通知廣告可用資訊。 

- 如 Apple HLS 中所定義的 EXT-X DATERANGE [RFC8216]
- 以 [Adobe-Primetime] 定義的 EXT X 提示-此模式會被視為「舊版」。 客戶應該盡可能採用 EXT X DATERANGE 標記。

每個標記的資料輸出會根據使用的內嵌信號模式而有所不同。 例如, 使用 Adobe Simple 模式的 RTMP 內嵌並不包含完整的 SCTE-35 base64 編碼承載。

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple HLS with Adobe Primetime EXT-X-DATERANGE (建議選項)

Apple HTTP 即時串流 [RFC8216] 規格允許發出 [SCTE-35] 訊息的信號。 這些訊息會插入區段播放清單中, 每個 [RFC8216] 區段的 DATERANGE 標記, 標題為「將 SCTE-35 對應到 EXT-X-DATERANGE」。  用戶端應用層可以剖析 M3U 播放清單並處理 M3U 標記, 或透過 Apple player 架構接收事件。  

Azure 媒體服務 (第3版 API) 中的**建議**方法是遵循 [RFC8216], 並在資訊清單中使用 [SCTE35] ad 可用裝飾的 EXT-X_DATERANGE 標記。

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS with Adobe Primetime EXT X 提示 (舊版)

Azure 媒體服務 (版本2和 3 API) 中提供了「舊版」的實作為, 其使用 [Adobe-Primetime] "SCTE-35 模式" 中定義的 EXT X 提示標記。 在此模式中, Azure 媒體服務會在 EXT X 提示標記中內嵌 base64 編碼的 [SCTE-35] splice_info_section ()。  

「舊版」的 EXT X 提示標記定義如下, 而且也可以在 [Adobe-Primetime] 規格中加以參考。 這應該只在需要時用於舊版 SCTE35 信號, 否則建議的標記會在 [RFC8216] 中定義為 EXT-DATERANGE。 

| **屬性名稱** | **型別**                      | **必要？**                             | **描述**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 提示                | 加上括號的字串                 | 必要項                                  | 編碼為 base64 編碼字串的訊息, 如 [RFC4648] 中所述。 對於 [SCTE-35] 訊息, 這是 base64 編碼的 splice_info_section ()。                                                                                                |
| 類型               | 加上括號的字串                 | 必要項                                  | 可識別訊息配置的 URN 或 URL。 針對 [SCTE-35] 訊息，類型接受特殊值 “scte35”。                                                                                                                                |
| id                 | 加上括號的字串                 | 必要項                                  | 事件的唯一識別碼。 如果內嵌訊息時未指定 ID，「Azure 媒體服務」將會產生一個唯一 ID。                                                                                                                                          |
| 持續時間           | 十進位浮點數數字 | 必要項                                  | 事件的持續時間。 如果未知, 此值**應該**是0。 單位是小數秒。                                                                                                                                                                                           |
| ELAPSED            | 十進位浮點數數字 | 選擇性，但對滑動視窗而言是必要的 | 當信號重複以支援滑動呈現視窗時, 此欄位**必須**是自活動開始以來已經過的呈現時間量。 單位是小數秒。 此值可能會超過原本指定的拼接或區段持續時間。 |
| TIME               | 十進位浮點數數字 | 必要項                                  | 事件的呈現時間。 單位是小數秒。                                                                                                                                                                                                                    |


HLS 播放程式應用程式層將會使用 TYPE 來識別訊息格式、將訊息解碼、套用必要時間轉換，以及處理事件。  根據事件時間戳記，事件時間在父代播放軌的區段播放清單中是同步的。  它們是插入在最接近的區段 (#EXTINF tag) 前面。

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>使用 "Legacy" Adobe Primetime EXT-X 提示的 3.2.3 HLS 區段播放清單範例

下列範例顯示使用 Adobe Primetime EXT--提示標記的 HLS 資訊清單裝飾。  "提示" 參數包含完整的 base64 編碼 SCTE-35 splice_info 裝載, 表示此信號在 Adobe SCTE-35 信號模式中使用 RTMP, 或透過 Smooth Streaming SCTE-35 信號模式傳入。 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>"Legacy" Adobe Primetime EXT-X 提示的 3.2.4 HLS 訊息處理

事件是在每個視訊語音訊播放軌的區段播放清單中收到訊號通知。EXT X 提示標籤的位置**必須**一律緊接在第一個 HLS 區段 (用於拼接外或區段開始) 之前, 或是緊接在最後一個 HLS 區段之後 (用於拼接或區段結尾) 到其 TIME 和 DURATION 屬性所參考的, 如下所示:必須是 [Adobe-Primetime]。

當滑動呈現視窗啟用時, EXT X 提示標籤的重複頻率**必須**足以讓拼接或區段一律在區段播放清單中完整描述, 而已耗用的屬性則**必須**用來表示如 [Adobe-Primetime] 所需, 接合或區段已啟用。

當滑動呈現視窗啟用時，若它們所指的媒體時間已從滑動呈現視窗滾出，標籤會從區段播放清單移除。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 虛線資訊清單裝飾 (MPD)

[MPEGDASH] 提供三種通知事件的方式:

1.  在 MPD EventStream 中發出信號的事件
2.  使用事件訊息方塊 (' emsg ') 在範圍內發出的事件
3.  1 與 2 兩者的組合

在 MPD EventStream 中發出信號的事件適用于 VOD 串流, 因為用戶端可以在下載 MPD 時立即存取所有事件。 它也適用于 SSAI 信號, 其中下游 SSAI 廠商需要從多個期間的 MPD 資訊清單剖析信號, 並動態插入廣告內容。  頻內 (' emsg ') 解決方案適用于即時串流, 其中用戶端不需要再次下載 MPD, 或在用戶端與來源之間不會發生 SSAI 資訊清單操作。 

Azure 媒體服務虛線的預設行為是使用事件訊息方塊 (' emsg '), 在 MPD EventStream 和頻內發出信號。

透過 [RTMP] 或 [MS-MS-SSTR] 內嵌的提示訊息會對應到虛線事件, 並使用內建的 ' emsg ' 方塊和/或 in-MPD Eventstream。 

[頻外 SCTE-35 信號] 會遵循 [SCTE-214-3] 中定義的定義和需求, 以及 [虛線-IF-IOP] 區段 13.12.2 (' SCTE35 Events ')。 

若為頻外 [SCTE-35], 事件訊息方塊 (' emsg ') 會使用 schemeId = "urn: SCTE: scte35: 2013: bin"。 針對 MPD 資訊清單裝飾, EventStream schemeId 會使用 "urn: scte: scte35: 2014: xml + bin"。  此格式是事件的 XML 表示, 其中包含抵達內嵌之完整 SCTE-35 訊息的二進位 base64 編碼輸出。 

在 [SCTE-214-1] sec 6.7.4 (MPD) 和 [SCTE-214-3] sec 7.3.2 (SCTE 35 提示訊息的托架) 中, 有 [SCTE-35] 的提示訊息的標準化參考定義。

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG 破折號 (MPD) EventStream 信號

事件的資訊清單 (MPD) 裝飾會使用 EventStream 專案在 MPD 中發出信號, 這會出現在 Period 元素中。 使用的 schemeId 是 "urn: scte: scte35: 2014: xml + bin"。

> [!NOTE]
> 為求簡潔, [SCTE-35] 允許在信號. Binary 元素 (而不是 SpliceInfoSection 元素) 中使用 base64 編碼的區段, 做為完全剖析的提示訊息的替代方法。
> Azure 媒體服務使用此 ' xml + bin ' 方法在 MPD 資訊清單中發出信號。
> 這也是建議用於 [虛線--------------IOP] 的方法-如 [IOP] 指導方針, 請參閱虛線的「 [Ad 插入事件資料流程](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)」一節
> 

EventStream 元素具有下列屬性：

| **屬性名稱** | **型別**                | **必要？** | **描述**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | 必要項      | 識別訊息的配置。 配置是設定為即時伺服器資訊清單方塊中 Scheme 屬性的值。 值**應該**是可識別訊息配置的 URN 或 URL;支援的輸出 schemeId 應該是 "urn: scte: scte35: 2014: xml + bin" per [SCTE-214-1] sec 6.7.4 (MPD), 因為這次服務僅支援 "xml + bin", 以便在 MPD 中進行簡潔。  |
| value              | string                  | 選擇性      | 由配置擁有者用來自訂訊息語意的額外字串值。 為了區分多個具有相同配置的事件資料流程, 此值**必須**設定為事件資料流程的名稱 ([ms-sstr-內嵌] 的 trackname 前或 [RTMP] 內嵌的 AMF 訊息名稱)。 |
| Timescale          | 32 位元不帶正負號的整數 | 必要項      | 時間刻度, 以每秒的刻度數為單位。                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 範例 MPEG 破折號資訊清單 (MPD) 使用 EventStream 的 SCTE-35 信號

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> 請注意, presentationTime 是轉譯為相對於期間開始時間的 [SCTE-35] 事件的呈現時間, 而不是訊息的抵達時間。
> [MPEGDASH] 將Event@presentationTime定義為 "指定事件的呈現時間 (相對於期間開始)。
> 呈現時間 (以秒為單位) 的值是這個屬性值與EventStream@timescale屬性值的除法。
> 如果不存在, 表示時間的值為0。


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 MPEG 破折號-頻外事件訊息方塊信號

頻內事件串流要求 MPD 在「自適性集合」層必須有 InbandEventStream 元素。  此元素有必要 schemeIdUri 屬性與選擇性 timescale 屬性，它們也出現在事件訊息方塊中 (‘emsg’)。  MPD 中未定義配置識別碼的事件訊息方塊不**應**存在。

對於頻外 [SCTE-35], 信號**必須**使用 schemeId = "urn: SCTE: scte35: 2013: bin"。
在 [SCTE-214-3] sec 7.3.2 (SCTE 35 提示訊息的 [SCTE]) 中, 會定義 [-35] 的「內建訊息」的標準化定義。

下列詳細資料會概述用戶端在與 [SCTE-214-3] 相容時, 應該會在 ' emsg ' 中預期的特定值:

| **欄位名稱**          | **欄位類型**          | **必要？** | **描述**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | 必要項      | 識別訊息的配置。 配置是設定為即時伺服器資訊清單方塊中 Scheme 屬性的值。 此值**必須**是可識別訊息配置的 URN。 對於 [SCTE-35] 訊息, 這**必須**是 "urn: SCTE: scte35: 2013: bin", 符合 [SCTE-214-3] |
| 值                   | string                  | 必要項      | 由配置擁有者用來自訂訊息語意的額外字串值。 若要區分具有相同配置的多個事件串流，值必須設定為事件串流的名稱 (適用於 Smooth 內嵌的 trackName，或適用於 RTMP 內嵌的 AMF 訊息名稱)。                                                                  |
| Timescale               | 32 位元不帶正負號的整數 | 必要項      | ‘emsg’ 方塊內時間與持續時間的時幅 (以每秒滴答數表示)。                                                                                                                                                                                                                                                                        |
| presentation_time_delta | 32 位元不帶正負號的整數 | 必要項      | 事件呈現時間與此區段中最早的呈現時間之間的媒體呈現時間差異。 簡報時間和持續時間**應該**與類型1或2的串流存取點 (SAP) 一致, 如 [ISO-14496-12] 附錄 I 中所定義。                                                                                            |
| event_duration          | 32 位元不帶正負號的整數 | 必要項      | 事件的持續時間，或指定 0xFFFFFFFF 以指出未知的持續時間。                                                                                                                                                                                                                                                                                          |
| Id                      | 32 位元不帶正負號的整數 | 必要項      | 識別訊息的此執行個體。 具有同等語意的訊息應該有相同的值。 如果內嵌訊息時未指定 ID，「Azure 媒體服務」將會產生一個唯一 ID。                                                                                                                                                    |
| Message_data            | 位元組陣列              | 必要項      | 事件訊息。 針對 [SCTE-35] 訊息, 訊息資料是二進位 splice_info_section (), 符合 [SCTE-214-3] |

### <a name="334-dash-message-handling"></a>3.3.4 破折號訊息處理

事件會以頻內方式接收傳送的訊號通知，針對視訊語音訊播放軌，都是在 ‘emsg’ 方塊內。  針對 presentation_time_delta 為 15 秒或低於 15 秒的所有區段要求，都會發生訊號處理。 

當滑動呈現視窗啟用時，若事件訊息的時間與持續時間總和小於資訊清單中媒體資料的時間，事件訊息會從 MPD 移除。  換句話說，當其所指之媒體時間已滾出滑動呈現視窗之外，就會從資訊清單移除事件訊息。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4.SCTE-35 適用于編碼器廠商的內嵌實施指導方針

下列指導方針是可能會影響編碼器廠商的此規格執行的常見問題。  下列指導方針已根據真實世界合作夥伴的意見反應收集, 讓您更輕鬆地為其他人執行此規格。 

[SCTE-35] 訊息是以二進位格式內嵌, 使用 " **urn: SCTE: scte35: 2013: bin"** (適用于 [MS-ms-sstr-內嵌]) 和類型 **"scte35"** (適用于 [RTMP] 內嵌)。 為促進 [SCTE-35] 計時的轉換 (以 MPEG-2 傳輸串流呈現時間戳記 (PTS) 為基礎)，PTS (pts_time + pts_adjustment of the splice_time()) 與媒體時間軸之間的對應是由事件呈現時間 (適用於 Smooth 內嵌的 fragment_absolute_time 欄位，或適用於 RTMP 內嵌的時間欄位) 所提供。 對應是必要的，因為 33 位元 PTS 值大約每隔 26.5 小時就會滾動一次。

Smooth Streaming 內嵌 [MS-SSTR-內嵌] 需要媒體資料箱 (' mdat ')**必須**包含 [SCTE-35] 中定義的**splice_info_section ()** 。 

對於 RTMP 內嵌, AMF 訊息的 [提示] 屬性會設定為 [SCTE-35] 中定義的 base64 編碼**splice_info_section ()** 。  

當訊息具有上述格式時, 會將其傳送至 HLS、平滑和虛線用戶端, 如上面所定義。  

當您使用 Azure 媒體服務平臺測試您的執行時, 請先使用「傳遞」 LiveEvent 開始測試, 再移至 [編碼 LiveEvent] 上的測試。

---

## <a name="change-history"></a>變更記錄

| Date     | 變更                                                                            |
|----------|------------------------------------------------------------------------------------|
| 07/2/19  | 已針對 SCTE35 支援修改 RTMP 內嵌, 已為 Elemental Live 新增 RTMP "onCuePoint" | 
| 08/22/19 | 已更新以將自訂中繼資料的 OnUserDataEvent 新增至 RTMP                         |

## <a name="next-steps"></a>後續步驟
檢視媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
